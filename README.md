<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Graph Builder</title>
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
  <style>
    .nested-indent { margin-left: 20px; }
  </style>
</head>
<body class="p-4">
  <div class="container">
    <h3>Create Cluster</h3>
    <form id="cluster-form" class="form-inline mb-3">
      <input type="text" class="form-control mr-2" id="clusterName" placeholder="Cluster Name" required>
      <select class="form-control mr-2" id="parentCluster">
        <option value="">Root Cluster</option>
      </select>
      <button class="btn btn-primary" type="submit">Add Cluster</button>
    </form>

    <h5>Clusters</h5>
    <ul id="cluster-list" class="list-group mb-4"></ul>

    <h3>Add Node</h3>
    <form id="node-form" class="form-inline mb-3">
      <input type="text" class="form-control mr-2" id="nodeId" placeholder="ID" required>
      <input type="text" class="form-control mr-2" id="nodeType" placeholder="Type">
      <input type="text" class="form-control mr-2" id="nodeLabel" placeholder="Label">
      <select class="form-control mr-2" id="nodeCluster" required></select>
      <button class="btn btn-success" type="submit">Add Node</button>
    </form>

    <h5>Nodes</h5>
    <table class="table table-bordered mb-4">
      <thead>
        <tr><th>ID</th><th>Type</th><th>Label</th><th>Cluster</th></tr>
      </thead>
      <tbody id="node-table"></tbody>
    </table>

    <button class="btn btn-warning mb-3" onclick="generateJSON()">Generate JSON</button>
    <pre id="json-output" class="bg-light p-3"></pre>
  </div>

  <script>
    const clusters = [];
    const nodes = [];

    document.getElementById("cluster-form").addEventListener("submit", function (e) {
      e.preventDefault();
      const name = document.getElementById("clusterName").value.trim();
      const parent = document.getElementById("parentCluster").value;
      if (!name) return;

      const newCluster = { name, cluster: [] };
      if (parent) {
        insertIntoParent(clusters, parent, newCluster);
      } else {
        clusters.push(newCluster);
      }

      updateClusterDisplay();
      updateClusterDropdowns();
      e.target.reset();
    });

    function insertIntoParent(clusterList, parentName, newCluster) {
      for (const c of clusterList) {
        if (c.name === parentName) {
          c.cluster = c.cluster || [];
          c.cluster.push(newCluster);
          return true;
        } else if (c.cluster) {
          if (insertIntoParent(c.cluster, parentName, newCluster)) return true;
        }
      }
      return false;
    }

    function updateClusterDisplay() {
      const list = document.getElementById("cluster-list");
      list.innerHTML = "";
      function render(clusters, indent = 0) {
        clusters.forEach(c => {
          const li = document.createElement("li");
          li.className = "list-group-item";
          li.style.marginLeft = indent + "px";
          li.textContent = c.name;
          list.appendChild(li);
          if (c.cluster && c.cluster.length) render(c.cluster, indent + 20);
        });
      }
      render(clusters);
    }

    function updateClusterDropdowns() {
      const select = document.getElementById("parentCluster");
      const nodeSelect = document.getElementById("nodeCluster");
      select.innerHTML = '<option value="">Root Cluster</option>';
      nodeSelect.innerHTML = "";

      function collectNames(clusterList, indent = "") {
        for (const c of clusterList) {
          const option = document.createElement("option");
          option.value = c.name;
          option.text = indent + c.name;
          select.appendChild(option.cloneNode(true));
          nodeSelect.appendChild(option);
          if (c.cluster) collectNames(c.cluster, indent + "--");
        }
      }

      collectNames(clusters);
    }

    document.getElementById("node-form").addEventListener("submit", function (e) {
      e.preventDefault();
      const id = document.getElementById("nodeId").value.trim();
      const type = document.getElementById("nodeType").value.trim();
      const label = document.getElementById("nodeLabel").value.trim();
      const clusterName = document.getElementById("nodeCluster").value;

      if (!id || !clusterName) return;

      nodes.push({ id, type, label, clusterName });
      updateNodeTable();
      e.target.reset();
    });

    function updateNodeTable() {
      const table = document.getElementById("node-table");
      table.innerHTML = "";
      nodes.forEach(n => {
        const row = `<tr><td>${n.id}</td><td>${n.type}</td><td>${n.label}</td><td>${n.clusterName}</td></tr>`;
        table.insertAdjacentHTML("beforeend", row);
      });
    }

    function assignNodesToClusters(clusters, nodeList) {
      for (const cluster of clusters) {
        cluster.nodes = nodeList.filter(n => n.clusterName === cluster.name).map(n => {
          return { id: n.id, type: n.type, label: n.label };
        });
        if (cluster.cluster && cluster.cluster.length) {
          assignNodesToClusters(cluster.cluster, nodeList);
        }
      }
    }

    function generateJSON() {
      const clustersCopy = JSON.parse(JSON.stringify(clusters));
      assignNodesToClusters(clustersCopy, nodes);
      document.getElementById("json-output").textContent = JSON.stringify({ cluster: clustersCopy }, null, 2);
    }
  </script>
</body>
</html>