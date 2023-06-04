from typing import Any, Text, Dict, List
from rasa_sdk import Action, Tracker
from rasa_sdk.executor import CollectingDispatcher
from rasa_sdk.events import SlotSet

class CheckConfidenceAction(Action):

    def name(self) -> Text:
        return "action_check_confidence"

    def run(self, dispatcher: CollectingDispatcher,
            tracker: Tracker,
            domain: Dict[Text, Any]) -> List[Dict[Text, Any]]:

        intent_ranking = tracker.latest_message.intent_ranking
        top_intent = intent_ranking[0]
        confidence = top_intent.get("confidence", 0.0)

        if confidence >= 0.6:
            # Respond with the actual intent's response
            dispatcher.utter_message(text="Sure, I can help with that!")
        else:
            # Respond with a default message
            dispatcher.utter_message(text="I'm sorry, I couldn't understand that. Can you please rephrase?")

        return []
