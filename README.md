function toggleDetails(row) {
    const nextRow = row.nextElementSibling;
    if (nextRow && nextRow.classList.contains('details-row')) {
        nextRow.style.display = nextRow.style.display === 'table-row' ? 'none' : 'table-row';
    }
}
