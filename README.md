#!/bin/bash

OUTPUT_FILE="pods_dns_report.csv"

# CSV Header
echo "Namespace,Pod Name,Pod Status,Nameservers" > $OUTPUT_FILE

for i in {1..60}; do
  NAMESPACE="application-$i"

  echo "Processing namespace: $NAMESPACE"

  # Get pod name and status
  kubectl get pods -n "$NAMESPACE" --no-headers 2>/dev/null | while read POD STATUS REST; do

    # Extract nameservers from /etc/resolv.conf
    NAMESERVERS=$(kubectl exec -n "$NAMESPACE" "$POD" -- \
      cat /etc/resolv.conf 2>/dev/null | \
      grep "^nameserver" | awk '{print $2}' | paste -sd "|" -)

    # Handle cases where exec is not allowed
    if [ -z "$NAMESERVERS" ]; then
      NAMESERVERS="N/A"
    fi

    echo "$NAMESPACE,$POD,$STATUS,$NAMESERVERS" >> $OUTPUT_FILE

  done
done

echo "Report generated: $OUTPUT_FILE"