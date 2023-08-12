function verifySyntax() {
    $.ajax({
        url: '/verify_syntax/',
        method: 'GET',
        success: function(data) {
            if (data.status === 'success') {
                $('#syntax-status').text('Syntax Verified');
                verifyDB();
            } else {
                $('#syntax-status').text('Syntax Verification Failed');
            }
        },
        error: function(error) {
            console.error('Error:', error);
        }
    });
}

function verifyDB() {
    $.ajax({
        url: '/verify_db/',
        method: 'GET',
        success: function(data) {
            if (data.status === 'success') {
                $('#db-status').text('DB Details Verified');
                $('#progress-text').text('Operations Complete!');
            } else {
                $('#db-status').text('DB Details Verification Failed');
            }
        },
        error: function(error) {
            console.error('Error:', error);
        }
    });
}

// Call the first function when the page loads
verifySyntax();
