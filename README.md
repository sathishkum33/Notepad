import requests
from rasa_sdk import Action, Tracker
from rasa_sdk.executor import CollectingDispatcher

class ActionGetOpenTickets(Action):

    def name(self) -> str:
        return "action_get_open_tickets"

    def run(self, dispatcher: CollectingDispatcher, tracker: Tracker, domain: dict):
        # Get the area path from the user's message
        area_path = tracker.get_slot('area_path')

        if not area_path:
            dispatcher.utter_message(text="I couldn't find the area path in your request. Please provide a valid area path.")
            return []

        # Define the Azure DevOps organization, project, and API endpoint
        organization = "your_organization"
        project = "your_project"
        api_version = "6.0"

        # Define the query URL
        url = f"https://dev.azure.com/{organization}/{project}/_apis/wit/wiql?api-version={api_version}"

        # Define the WIQL (Work Item Query Language) query
        wiql_query = {
            "query": f"SELECT [System.Id] FROM workitems WHERE [System.TeamProject] = '{project}' AND [System.AreaPath] = '{area_path}' AND [System.State] = 'Active'"
        }

        # Azure DevOps Personal Access Token (PAT)
        pat = "your_personal_access_token"

        # Make the request to Azure DevOps
        response = requests.post(url, json=wiql_query, auth=('', pat))
        data = response.json()

        # Count the number of open tickets
        open_ticket_count = len(data.get('workItems', []))

        # Send the response back to the user
        dispatcher.utter_message(text=f"The total number of open tickets for the area path {area_path} is {open_ticket_count}.")

        return []