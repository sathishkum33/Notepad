import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import MultiLabelBinarizer
import torch
from torch.utils.data import Dataset, DataLoader
from transformers import BertTokenizer, BertModel
import torch.nn as nn
import torch.optim as optim
import requests
import numpy as np

# Load the data
data = pd.read_csv('work_items.csv')

# Preprocess the data
data['description'] = data['description'].fillna('')
data['text'] = data['title'] + ' ' + data['description']

# Encode tags
mlb = MultiLabelBinarizer()
y = mlb.fit_transform(data['tags'].apply(lambda x: x.split(',')))
X = data['text']

# Split the data
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Define the Dataset and DataLoader
tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')

class WorkItemDataset(Dataset):
    def __init__(self, texts, labels):
        self.texts = texts
        self.labels = labels

    def __len__(self):
        return len(self.texts)

    def __getitem__(self, idx):
        text = self.texts[idx]
        label = self.labels[idx]
        encoding = tokenizer.encode_plus(
            text,
            add_special_tokens=True,
            max_length=256,
            return_token_type_ids=False,
            padding='max_length',
            return_attention_mask=True,
            return_tensors='pt',
        )
        return {
            'text': text,
            'input_ids': encoding['input_ids'].flatten(),
            'attention_mask': encoding['attention_mask'].flatten(),
            'labels': torch.tensor(label, dtype=torch.float)
        }

train_dataset = WorkItemDataset(X_train, y_train)
test_dataset = WorkItemDataset(X_test, y_test)

train_loader = DataLoader(train_dataset, batch_size=16, shuffle=True)
test_loader = DataLoader(test_dataset, batch_size=16)

# Define the Model
class WorkItemTagger(nn.Module):
    def __init__(self, n_classes):
        super(WorkItemTagger, self).__init__()
        self.bert = BertModel.from_pretrained('bert-base-uncased')
        self.drop = nn.Dropout(p=0.3)
        self.out = nn.Linear(self.bert.config.hidden_size, n_classes)
        
    def forward(self, input_ids, attention_mask):
        _, pooled_output = self.bert(
            input_ids=input_ids,
            attention_mask=attention_mask
        )
        output = self.drop(pooled_output)
        return self.out(output)

model = WorkItemTagger(n_classes=y.shape[1])

# Train the Model
device = torch.device('cuda') if torch.cuda.is_available() else torch.device('cpu')
model = model.to(device)

criterion = nn.BCEWithLogitsLoss()
optimizer = optim.Adam(model.parameters(), lr=2e-5)

def train_epoch(model, data_loader, criterion, optimizer, device):
    model.train()
    losses = []
    for batch in data_loader:
        input_ids = batch['input_ids'].to(device)
        attention_mask = batch['attention_mask'].to(device)
        labels = batch['labels'].to(device)
        
        outputs = model(input_ids, attention_mask)
        loss = criterion(outputs, labels)
        
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()
        
        losses.append(loss.item())
    
    return sum(losses) / len(losses)

EPOCHS = 3

for epoch in range(EPOCHS):
    train_loss = train_epoch(model, train_loader, criterion, optimizer, device)
    print(f'Epoch {epoch + 1}/{EPOCHS}, Train Loss: {train_loss}')

# Save the model
torch.save(model.state_dict(), 'tagging_model.pth')

# Load the model for inference
model = WorkItemTagger(n_classes=y.shape[1])
model.load_state_dict(torch.load('tagging_model.pth'))
model = model.to(device)
model.eval()

# Define function to predict tags
def predict_tags(text):
    encoding = tokenizer.encode_plus(
        text,
        add_special_tokens=True,
        max_length=256,
        return_token_type_ids=False,
        padding='max_length',
        return_attention_mask=True,
        return_tensors='pt',
    )
    input_ids = encoding['input_ids'].to(device)
    attention_mask = encoding['attention_mask'].to(device)
    
    with torch.no_grad():
        outputs = model(input_ids, attention_mask)
    
    predictions = torch.sigmoid(outputs).cpu().numpy()
    tags = mlb.inverse_transform(predictions > 0.5)[0]
    return tags

# Integrate with ADO to update tags
def update_tags_for_new_work_items():
    # Example ADO API call to get new work items
    response = requests.get('https://dev.azure.com/your_org/your_project/_apis/wit/workitems')
    work_items = response.json()['value']

    for item in work_items:
        if not item['fields'].get('System.Tags'):
            title = item['fields']['System.Title']
            description = item['fields'].get('System.Description', '')
            text = title + ' ' + description
            
            # Predict tags
            tags = predict_tags(text)
            
            # Update work item with predicted tags
            update_response = requests.patch(
                f'https://dev.azure.com/your_org/your_project/_apis/wit/workitems/{item["id"]}?api-version=6.0',
                json=[{
                    "op": "add",
                    "path": "/fields/System.Tags",
                    "value": ",".join(tags)
                }],
                headers={'Content-Type': 'application/json-patch+json'}
            )
            if update_response.status_code == 200:
                print(f'Successfully updated tags for work item {item["id"]}')
            else:
                print(f'Failed to update tags for work item {item["id"]}')

# Run the function to update tags
update_tags_for_new_work_items()
