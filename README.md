function getCookie(name) {
    let cookieValue = null;
    if (document.cookie && document.cookie !== '') {
        const cookies = document.cookie.split(';');
        for (let i = 0; i < cookies.length; i++) {
            const cookie = cookies[i].trim();
            if (cookie.substring(0, name.length + 1) === (name + '=')) {
                cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                break;
            }
        }
    }
    return cookieValue;
}

function sendDataToFunction(dataToSend) {
    const csrfToken = getCookie('csrftoken'); // Get CSRF token from cookies
    $.ajax({
        url: '/your-view-url/',
        method: 'POST',
        headers: {
            'X-CSRFToken': csrfToken, // Include CSRF token in request headers
        },
        data: {
            data: dataToSend
        },
        success: function(response) {
            // Handle the response from the server
        },
        error: function(error) {
            console.error('Error:', error);
        }
    });
}
