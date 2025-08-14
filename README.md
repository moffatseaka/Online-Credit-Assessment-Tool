# Online-Credit-Assessment-Tool
Users input their income, expenses, and debt details via a form. The app calculates an affordable repayment plan based on these inputs.
# Ideal first step should be to input client ID and check client #credit bureau score if client has good credit score and rating. #For that, we should be linking to the credit bureau first.
# --- Online Credit Assessment Tool (OCAT) ---
from tabulate import tabulate
import matplotlib.pyplot as plt
# Mock credit bureau database (in real life, this would be an API call)
credit_bureau = {
"YEAR123": {"score": 650, "rating": "Good"},
"YEAR456": {"score": 550, "rating": "Poor"},
"YEAR789": {"score": 700, "rating": "Excellent"}
}
def check_credit_bureau(client_id):
    if client_id not in credit_bureau:
        print(f"❌ Client ID {client_id} not found in credit bureau.")
        return None  # Indicate failure

    score = credit_bureau[client_id]["score"]
    rating = credit_bureau[client_id]["rating"]

    if score < 600:
        print(f"❌ Credit score {score} ({rating}) is too low to proceed.")
        return None  # Indicate failure

    print(f"✅ Credit check passed: Score {score}, Rating {rating}")
    return score, rating

# Stop if credit check fails

# --- Input Validation Functions ---
def get_float_input(prompt):
    while True:
        try:
            value = float(input(prompt))
            if value < 0:
                raise ValueError
            return value
        except ValueError:
            print("❌ Please enter a valid positive number.")

# --- 1. Income Section ---
print("=== Welcome to the Online Credit Assessment Tool ===")

gross_income = get_float_input("Enter your gross monthly salary: R ")
deductions = get_float_input("Enter total monthly deductions from payslip: R ")
net_income = gross_income - deductions

print(f"\n💰 Your net monthly income: R {net_income:.2f}")

# Qualification Rule: Net income must be >= R 7,500
min_required_income = 7500
if net_income < min_required_income:
    print(f"❌ Unfortunately, you do not qualify. Minimum required net income is R {min_required_income:.2f}")
    exit()

# --- 2. Expenses Section ---
expenses = {}
while True:
    expense_name = input("\nEnter household expense name (or 'done' to finish): ")
    if expense_name.lower() == 'done':
        break
    expense_amount = get_float_input(f"Enter amount for {expense_name}: R ")
    expenses[expense_name] = expense_amount

# --- 3. Debts Section (Name + Installment Only) ---
debts = {}
while True:
    debt_name = input("Enter debt name (or 'done' to finish): ")
    if debt_name.lower() == 'done':
        break
    installment_amount = get_float_input(f"Enter monthly installment for {debt_name}: R ")
    debts[debt_name] = installment_amount
    print("Debt installment added successfully!")


# --- 4. Calculations ---
total_expenses = sum(expenses.values())
total_debt_installments = sum(debts.values())
disposable_income = net_income - (total_expenses + total_debt_installments)
affordable_payment = max(disposable_income * 0.6, 0)  # 30% rule inour case we are using 60% rule at a higher interest rate we charge for the risk of defaulting on the loan.
if disposable_income < 500:
    print("❌ Unfortunately, you do not qualify. Minimum required disposable income is: ", disposable_income, "\nDisposable Income  must be > 500.00")
    exit()
    

# --- 5. Summary of Inputs ---
print("\n📋 Summary of Inputs:")
print(f"Gross Income: R {gross_income:.2f}")
print(f"Deductions: R {deductions:.2f}")
print(f"Net Income: R {net_income:.2f}")

if expenses:
    expense_table = [[name, f"R {amount:.2f}"] for name, amount in expenses.items()]
    print("\nExpenses:")
    print(tabulate(expense_table, headers=["Expense", "Amount"], tablefmt="grid"))
else:
    print("\nNo expenses entered.")

if debts:
    debt_table = [[name, f"R {amount:.2f}"] for name, amount in debts.items()]
    print("\nDebts:")
    print(tabulate(debt_table, headers=["Debt", "Installment"], tablefmt="grid"))
else:
    print("\nNo debts entered.")

# --- 6. Display Calculations ---
print(f"\n💵 Disposable Income: R {disposable_income:.2f}")
print(f"📌 Affordable Monthly Repayment (30-60% rule): R {affordable_payment:.2f}")

# --- 7. Pie Chart Visualization ---
labels = ["Expenses", "Debt Installments", "Disposable Income"]
sizes = [total_expenses, total_debt_installments, max(disposable_income, 0)]
colors = ["#FF9999", "#FFCC99", "#99FF99"]

plt.pie(sizes, labels=labels, colors=colors, autopct="%1.1f%%")
plt.title("Income Allocation")
plt.show()
