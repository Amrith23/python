# python
def bill_splitter():
    print("=== Advanced Bill Splitter with Payment Tracking ===")

    try:
        # Get the total bill amount
        total_bill = float(input("Enter the total bill amount: $"))
        if total_bill <= 0:
            print("Bill amount must be positive.")
            return

        # Get the number of people
        num_people = int(input("Enter the number of people: "))
        if num_people < 1:
            print("Number of people must be at least 1.")
            return

        # Get names for each person
        names = []
        for i in range(num_people):
            name = input(f"Enter name for person {i+1}: ").strip()
            names.append(name if name else f"Person {i+1}")

        # Get splitting type
        split_type = input("Choose splitting type (even/uneven): ").lower()
        while split_type not in ['even', 'uneven']:
            print("Invalid choice. Please enter 'even' or 'uneven'.")
            split_type = input("Choose splitting type (even/uneven): ").lower()

        if split_type == 'uneven':
            print(f"Enter percentages for {num_people} people (must sum to 100%):")
            percentages = []
            for i in range(num_people):
                percent = float(input(f"Percentage for {names[i]}: "))
                percentages.append(percent)

            if abs(sum(percentages) - 100) > 0.01:  # Allow for floating point precision
                print("Error: Percentages must sum to 100%")
                return

        # Get optional tax percentage
        tax_percent = 0
        tax_option = input("Add tax? (yes/no): ").lower()
        if tax_option == 'yes':
            tax_percent = float(input("Enter tax percentage (e.g., 8 for 8%): "))
            if tax_percent < 0:
                print("Tax percentage cannot be negative.")
                return

        # Get optional tip percentage
        tip_percent = 0
        tip_option = input("Add tip? (yes/no): ").lower()
        if tip_option == 'yes':
            tip_percent = float(input("Enter tip percentage (e.g., 15 for 15%): "))
            if tip_percent < 0:
                print("Tip percentage cannot be negative.")
                return

        # Calculate total with tax and tip
        tax_amount = total_bill * (tax_percent / 100)
        subtotal_with_tax = total_bill + tax_amount
        tip_amount = subtotal_with_tax * (tip_percent / 100)
        grand_total = subtotal_with_tax + tip_amount

        # Calculate amounts per person
        if split_type == 'even':
            per_person = grand_total / num_people
            amounts = {name: per_person for name in names}
        else:
            amounts = {names[i]: grand_total * (percentages[i]/100) for i in range(num_people)}

        # Initialize payment tracking
        payments = {name: False for name in names}

        # Display results
        print("\n=== Bill Summary ===")
        print(f"Subtotal: ${total_bill:.2f}")
        if tax_percent > 0:
            print(f"Tax ({tax_percent}%): ${tax_amount:.2f}")
            print(f"Subtotal with tax: ${subtotal_with_tax:.2f}")
        if tip_percent > 0:
            print(f"Tip ({tip_percent}%): ${tip_amount:.2f}")
        print(f"Grand total: ${grand_total:.2f}")

        # Payment tracking loop
        while True:
            print("\n=== Payment Status ===")
            for name in names:
                status = "Paid" if payments[name] else "Pending"
                print(f"{name}: ${amounts[name]:.2f} - {status}")

            all_paid = all(payments.values())
            if all_paid:
                print("\nAll payments completed!")
                break

            print("\nOptions:")
            print("1. Record a payment")
            print("2. Exit")
            choice = input("Enter your choice (1-2): ")

            if choice == '1':
                print("\nWho has paid?")
                for i, name in enumerate(names, 1):
                    if not payments[name]:
                        print(f"{i}. {name}")
                try:
                    payer_num = int(input("Enter number: "))
                    if 1 <= payer_num <= len(names):
                        payer = names[payer_num-1]
                        if payments[payer]:
                            print(f"{payer} has already paid!")
                        else:
                            payments[payer] = True
                            print(f"Recorded payment for {payer}")
                    else:
                        print("Invalid number")
                except ValueError:
                    print("Please enter a valid number")
            elif choice == '2':
                print("\nExiting payment tracker. Remaining payments:")
                for name in names:
                    if not payments[name]:
                        print(f"- {name}: ${amounts[name]:.2f}")
                break
            else:
                print("Invalid choice")

    except ValueError:
        print("Invalid input. Please enter numbers only.")

# Run the bill splitter
if __name__ == "__main__":
    bill_splitter()
