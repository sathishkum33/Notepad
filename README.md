from typing import Any, Text, Dict, List
from rasa_sdk import Action, Tracker
from rasa_sdk.executor import CollectingDispatcher

class CheckConfidenceAction(Action):
    def name(self) -> Text:
        return "custom_actions.CheckConfidenceAction"

    def run(self, dispatcher: CollectingDispatcher, tracker: Tracker, domain: Dict[Text, Any]) -> List[Dict[Text, Any]]:
        confidence_threshold = 0.6

        last_user_message = tracker.latest_message.get("text")
        last_intent_confidence = tracker.latest_message.intent.get("confidence")

        if last_intent_confidence < confidence_threshold:
            # Respond with default message
            dispatcher.utter_message("I'm sorry, I didn't understand that.")
        else:
            # Respond with the matched intent
            dispatcher.utter_message(f"You said: {last_user_message}. I understood it correctly!")

        return []
