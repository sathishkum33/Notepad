<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bootstrap 4 Table with Search and Expand/Collapse</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <link rel="stylesheet" href="https://cdn.datatables.net/1.10.21/css/dataTables.bootstrap4.min.css">
    <style>
        .details-container {
            display: none;
        }
        .details-show {
            display: table-row;
        }
        .no-border td {
            border: none !important;
        }
        .toggle-button {
            cursor: pointer;
            transition: transform 0.3s ease;
            border-radius: 50%;
            padding: 5px;
            background-color: #f0f0f0;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
            display: inline-block;
            width: 24px;
            height: 24px;
            text-align: center;
            line-height: 14px;
        }
        .toggle-button.expanded {
            transform: rotate(90deg);
        }
    </style>
</head>
<body>
<div class="container mt-5">
    <h2 class="mb-4">Build Information Table</h2>
    <table id="buildTable" class="table table-striped table-bordered">
        <thead>
            <tr>
                <th>Release ID</th>
                <th>Build ID</th>
                <th>Build Number</th>
                <th>Branch Name</th>
                <th>Name</th>
                <th></th>
            </tr>
        </thead>
        <tbody>
            <tr data-child='{"environment":["Production", "Staging"],"execution_date":"2024-06-01","execution_time":"12:00 PM","downloads":["link1","link2"]}'>
                <td>1</td>
                <td>1001</td>
                <td>12345</td>
                <td>main</td>
                <td>Build A</td>
                <td class="toggle-button">&gt;</td>
            </tr>
            <tr data-child='{"environment":["Development"],"execution_date":"2024-06-02","execution_time":"1:00 PM","downloads":["link3"]}'>
                <td>2</td>
                <td>1002</td>
                <td>12346</td>
                <td>dev</td>
                <td>Build B</td>
                <td class="toggle-button">&gt;</td>
            </tr>
            <tr data-child='{"environment":["Testing"],"execution_date":"2024-06-03","execution_time":"2:00 PM","downloads":["link4","link5"]}'>
                <td>3</td>
                <td>1003</td>
                <td>12347</td>
                <td>feature-branch</td>
                <td>Build C</td>
                <td class="toggle-button">&gt;</td>
            </tr>
            <!-- Add more rows as needed -->
        </tbody>
    </table>
</div>

<script src="https://code.jquery.com/jquery-3.5.1.js"></script>
<script src="https://cdn.datatables.net/1.10.21/js/jquery.dataTables.min.js"></script>
<script src="https://cdn.datatables.net/1.10.21/js/dataTables.bootstrap4.min.js"></script>
<script>
$(document).ready(function() {
    var table = $('#buildTable').DataTable({
        "order": [],
        "columnDefs": [
            { "orderable": false, "targets": -1 }
        ]
    });

    // Toggle the details
    $('#buildTable').on('click', 'tbody tr', function() {
        var tr = $(this).closest('tr');
        var row = table.row(tr);
        var button = tr.find('.toggle-button');

        if (row.child.isShown()) {
            row.child.hide();
            button.html('&gt;').removeClass('expanded');
        } else {
            var data = tr.data('child');
            var detailsHtml = `
                <div class="no-border">
                    <strong>Environment:</strong>
                    <ul>
                        ${data.environment.map(env => `<li>${env}</li>`).join('')}
                    </ul>
                    <strong>Execution Date:</strong> ${data.execution_date}<br>
                    <strong>Execution Time:</strong> ${data.execution_time}<br>
                    <strong>Downloads:</strong>
                    <ul>
                        ${data.downloads.map(link => `<li><a href="#">${link}</a></li>`).join('')}
                    </ul>
                </div>
            `;
            row.child(detailsHtml).show();
            button.html('&gt;').addClass('expanded');
        }
    });
});
</script>
</body>
</html>
