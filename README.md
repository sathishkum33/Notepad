# actions.py
import requests
from rasa_sdk import Action, Tracker
from rasa_sdk.executor import CollectingDispatcher

class ActionGetCpuUsage(Action):
    def name(self):
        return "action_get_cpu_usage"

    def run(self, dispatcher, tracker, domain):
        prometheus_url = "http://your-prometheus-endpoint/api/v1/query"
        query = "100 - (avg by (instance) (irate(node_cpu_seconds_total{mode='idle'}[5m])) * 100)"

        response = requests.get(prometheus_url, params={'query': query})

        if response.status_code == 200:
            data = response.json()
            cpu_usage = data['data']['result'][0]['value'][1]
            dispatcher.utter_message(text=f"Current CPU Usage: {cpu_usage}%")
        else:
            dispatcher.utter_message(text="Error querying Prometheus API for CPU usage.")

class ActionGetRamUsage(Action):
    def name(self):
        return "action_get_ram_usage"

    def run(self, dispatcher, tracker, domain):
        prometheus_url = "http://your-prometheus-endpoint/api/v1/query"
        query = "sum(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / sum(node_memory_MemTotal_bytes) * 100"

        response = requests.get(prometheus_url, params={'query': query})

        if response.status_code == 200:
            data = response.json()
            ram_usage = data['data']['result'][0]['value'][1]
            dispatcher.utter_message(text=f"Current RAM Usage: {ram_usage}%")
        else:
            dispatcher.utter_message(text="Error querying Prometheus API for RAM usage.")