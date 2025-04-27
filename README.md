{
  "nodes": [
    {"id": "web", "type": "aws.ec2.Instance", "label": "Web Server"},
    {"id": "db", "type": "aws.rds.Instance", "label": "Database"},
    {"id": "s3", "type": "aws.storage.S3", "label": "Storage"}
  ],
  "edges": [
    {"from": "web", "to": "db"},
    {"from": "web", "to": "s3"}
  ]
}