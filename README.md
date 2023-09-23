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
