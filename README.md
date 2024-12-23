from datetime import datetime

class GoldLoan:
    def __init__(self, principal, annual_interest_rate):
        self.principal = principal
        self.annual_interest_rate = annual_interest_rate / 100
        self.payment_history = []
        self.start_date = datetime.now()

    def part_payment(self, amount, payment_date):
        self.payment_history.append({"amount": amount, "date": payment_date})
        self.payment_history = sorted(self.payment_history, key=lambda x: x["date"])

    def calculate_interest(self):
        total_interest = 0
        detailed_split = []
        last_date = self.start_date
        balance = self.principal

        for payment in self.payment_history:
            payment_date = payment["date"]
            amount = payment["amount"]

            # Calculate days between payments
            days_diff = (payment_date - last_date).days

            # Calculate interest for the period
            period_interest = (balance * self.annual_interest_rate * days_diff) / 365
            total_interest += period_interest

            # Update balance
            balance -= amount

            # Store split-up details
            detailed_split.append({
                "start_date": last_date,
                "end_date": payment_date,
                "balance_before": balance + amount,
                "days": days_diff,
                "interest": round(period_interest, 2),
                "payment": amount,
                "balance_after": balance
            })

            last_date = payment_date

        # Calculate interest for the remaining period till today
        today = datetime.now()
        days_diff = (today - last_date).days
        period_interest = (balance * self.annual_interest_rate * days_diff) / 365
        total_interest += period_interest

        detailed_split.append({
            "start_date": last_date,
            "end_date": today,
            "balance_before": balance,
            "days": days_diff,
            "interest": round(period_interest, 2),
            "payment": 0,
            "balance_after": balance
        })

        return total_interest, detailed_split

# Example usage
loan = GoldLoan(principal=100000, annual_interest_rate=8.8)

# Part payments
loan.part_payment(10000, datetime(2024, 12, 1))
loan.part_payment(20000, datetime(2024, 12, 15))

# Calculate interest
total_interest, detailed_split = loan.calculate_interest()

print(f"Total Interest: {round(total_interest, 2)}")
print("\nDetailed Split-up:")
for entry in detailed_split:
    print(entry)