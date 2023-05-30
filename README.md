from typing import Any, Dict, List, Text

from rasa_sdk import Action, Tracker
from rasa_sdk.executor import CollectingDispatcher

class FetchInformationAction(Action):
    def name(self) -> Text:
        return "action_fetch_information"

    def run(
        self,
        dispatcher: CollectingDispatcher,
        tracker: Tracker,
        domain: Dict[Text, Any],
    ) -> List[Dict[Text, Any]]:
        # Extract the hostname entity from the user's message
        hostname = next(tracker.get_latest_entity_values("hostname"), None)

        # Perform the database lookup to fetch information for the hostname
        if hostname:
            # Database lookup and information fetching logic here
            if hostname_exists_in_database(hostname):
                information = fetch_information_from_database(hostname)
                dispatcher.utter_message(text=f"Information for hostname {hostname}: {information}")
            else:
                dispatcher.utter_message(text=f"The hostname {hostname} does not exist in the database.")
        else:
            dispatcher.utter_message(text="Please provide a valid hostname.")

        return []
