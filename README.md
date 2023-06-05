from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, String

from rasa_sdk import Action, Tracker
from rasa_sdk.executor import CollectingDispatcher


class CheckHostnameAction(Action):
    def name(self) -> Text:
        return "action_check_hostname"


def run(self, dispatcher: CollectingDispatcher,
        tracker: Tracker,
        domain: Dict[Text, Any]) -> List[Dict[Text, Any]]:

    # Get the hostname entity from the user's message
    hostname = next(tracker.get_latest_entity_values("hostname"), None)

    if hostname:
        # Establish a connection to the PostgreSQL database
        engine = create_engine("postgresql://username:password@localhost/database_name")
        Session = sessionmaker(bind=engine)
        session = Session()

        # Declare the SQLAlchemy base
        Base = declarative_base()

        # Define a model for your hostname table
        class Hostname(Base):
            __tablename__ = "hostname_table"
            hostname = Column(String, primary_key=True)

        # Query the database to check if the hostname exists
        result = session.query(Hostname).filter(Hostname.hostname == hostname).first()

        if result:
            # Hostname exists in the inventory
            dispatcher.utter_message(f"Yes, {hostname} is present in the inventory.")
        else:
            # Hostname does not exist in the inventory
            dispatcher.utter_message(f"No, {hostname} is not present in the inventory.")
        
        # Close the database session
        session.close()
    else:
        # No hostname provided by the user
        dispatcher.utter_message("Please provide a hostname to check.")

    return []


