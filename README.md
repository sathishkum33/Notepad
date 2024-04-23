import pandas as pd
from openpyxl import Workbook
from openpyxl.styles import Border, Side, PatternFill, Font

# Define your data
data = [
    {"app": "cib", "env": "dev", "status": "active", "version": "1.0", "ip": "192.168.1.1"},
    {"app": "cib", "env": "uat", "status": "inactive", "version": "1.1", "ip": "192.168.1.2"},
    {"app": "cib", "env": "sit", "status": "active", "version": "1.2", "ip": "192.168.1.3"}
]

# Convert the data into a Pandas DataFrame
df = pd.DataFrame(data)

# Reorder columns
df = df[['app', 'env', 'status', 'version']]

# Rename columns
df.columns = ['APPLICATION', 'ENVIRONMENT', 'STATUS', 'VERSION']

# Create a new Excel workbook
wb = Workbook()

# Add a worksheet with a specific name
ws = wb.create_sheet(title='Application Data')

# Convert DataFrame to worksheet
for r in dataframe_to_rows(df, index=False, header=True):
    ws.append(r)

# Add black border around filled cells
for row in ws.iter_rows(min_row=1, max_row=1, max_col=ws.max_column):
    for cell in row:
        cell.border = Border(left=Side(style='thin', color='000000'),
                             right=Side(style='thin', color='000000'),
                             top=Side(style='thin', color='000000'),
                             bottom=Side(style='thin', color='000000'))
        cell.fill = PatternFill(start_color="0070C0", end_color="0070C0", fill_type="solid")  # Blue background
        cell.font = Font(color="FFFFFF", bold=True)  # White text and bold font

# Add black border around filled cells
for row in ws.iter_rows(min_row=2, min_col=1, max_row=ws.max_row, max_col=ws.max_column):
    for cell in row:
        cell.border = Border(left=Side(style='thin', color='000000'),
                             right=Side(style='thin', color='000000'),
                             top=Side(style='thin', color='000000'),
                             bottom=Side(style='thin', color='000000'))

# Adjust column width based on content size
for col in ws.columns:
    max_length = 0
    for cell in col:
        try:
            if len(str(cell.value)) > max_length:
                max_length = len(cell.value)
        except:
            pass
    adjusted_width = (max_length + 2) * 1.2
    ws.column_dimensions[col[0].column_letter].width = adjusted_width

# Save the workbook
wb.save('output.xlsx')

print("Excel file generated successfully with formatted header, black borders, adjusted column widths, and specified sheet name.")
