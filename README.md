// Initialize an array to store selected values
let selectedValues = [];

// Function to open the Bootstrap popup
function openPopup() {
    // Clear the array before showing the popup
    selectedValues = [];
    // Code to open the popup goes here
}

// Function to handle checkbox selection
function handleCheckboxChange(checkbox) {
    if (checkbox.checked) {
        // Add the selected value to the array
        selectedValues.push(checkbox.value);
    } else {
        // Remove the value if the checkbox is unchecked (optional)
        const index = selectedValues.indexOf(checkbox.value);
        if (index !== -1) {
            selectedValues.splice(index, 1);
        }
    }
}

// Function to process the selected values
function processSelectedValues() {
    console.log(selectedValues);
    // Perform any other actions with the selected values
}
