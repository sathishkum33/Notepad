<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Task Management Table</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <table>
        <thead>
            <tr>
                <th>Cust.</th>
                <th>Project</th>
                <th>Assign Date</th>
                <th>Team</th>
                <th>Priority</th>
                <th>Status</th>
                <th>Edit</th>
            </tr>
        </thead>
        <tbody>
            <tr class="main-row" onclick="toggleDetails(this)">
                <td><img src="path_to_avatar1.jpg" alt="Avatar" class="avatar"></td>
                <td>
                    Wordpress Website<br>
                    <span class="assigned-to">Assigned to <strong>Airi Satou</strong></span>
                </td>
                <td>20-02-2018</td>
                <td><img src="team_image.jpg" alt="Team" class="team"></td>
                <td>High</td>
                <td>
                    <span>50%</span>
                    <div class="progress-bar" style="width: 50%; background-color: green;"></div>
                </td>
                <td>
                    <button class="edit-btn">✏️</button>
                    <button class="delete-btn">🗑️</button>
                </td>
            </tr>
            <tr class="details-row">
                <td colspan="7">
                    <div class="details-content">
                        <ul>
                            <li><a href="download_link1">Download Option 1</a></li>
                            <li><a href="download_link2">Download Option 2</a></li>
                            <li><a href="download_link3">Download Option 3</a></li>
                        </ul>
                    </div>
                </td>
            </tr>
            <!-- Add more rows similarly -->
        </tbody>
    </table>
    <script src="script.js"></script>
</body>
</html>
