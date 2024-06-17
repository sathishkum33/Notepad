body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f4;
    margin: 0;
    padding: 20px;
}

table {
    width: 100%;
    border-collapse: collapse;
    background-color: #fff;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

th, td {
    padding: 15px;
    text-align: left;
    border-bottom: 1px solid #ddd;
}

th {
    background-color: #f9f9f9;
    font-weight: bold;
}

td img.avatar {
    width: 40px;
    height: 40px;
    border-radius: 50%;
}

td img.team {
    width: 100px;
    height: auto;
}

.assigned-to {
    color: gray;
}

.progress-bar {
    height: 10px;
    background-color: lightgray;
    border-radius: 5px;
    margin-top: 5px;
}

.edit-btn, .delete-btn {
    background: none;
    border: none;
    cursor: pointer;
    font-size: 1.2em;
}

.edit-btn:hover, .delete-btn:hover {
    opacity: 0.7;
}

.details-row {
    display: none;
}

.details-content {
    padding: 10px;
    background-color: #f9f9f9;
    border-top: 1px solid #ddd;
}

.details-content ul {
    list-style-type: none;
    padding: 0;
    margin: 0;
}

.details-content li {
    margin: 5px 0;
}

.details-content a {
    text-decoration: none;
    color: #007BFF;
}

.details-content a:hover {
    text-decoration: underline;
}
