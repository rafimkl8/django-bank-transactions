# Bank Account Transaction Management System

A simple Django app for a bank account assignment. Logged-in users can create
one bank account, deposit/withdraw money, and view their transaction history.

## Features

- User registration, login, logout (Django's built-in auth)
- Each user has exactly one bank account, and can only ever see their own
  account/transactions (everything is filtered by `request.user`)
- Deposit and withdraw money, with overdraft prevention on withdrawals
- Dashboard showing current balance, total deposits, total withdrawals, and
  total transaction count (calculated with Django ORM aggregates)
- Transaction history, newest first, with search by type and filter by date
- Pagination on the transaction history (5 per page)
- Bootstrap (via CDN) for styling

## Tech Stack

- Python 3.11
- Django 5.2
- SQLite (default Django database, good enough for this assignment)
- Bootstrap 5 (CDN, no npm/build step)

## Project Structure

```
bank_project/
├── bankproject/        # Django project settings/urls
├── bank/               # the actual app
│   ├── models.py       # BankAccount, Transaction
│   ├── views.py        # all the views (function-based)
│   ├── forms.py        # register form, account form, deposit/withdraw forms
│   ├── urls.py
│   ├── admin.py
│   ├── management/commands/seed_data.py   # seeds demo data
│   └── templates/bank/
├── manage.py
├── requirements.txt
├── screenshots/         # UI screenshots for submission
└── db.sqlite3          # sample database with demo data already in it
```

## Screenshots

See the `screenshots/` folder for UI screenshots (registration, login,
dashboard, deposit, withdraw, transaction history) taken against the seeded
`demo_user` account.

## Models

- **BankAccount** — one-to-one with Django's `User`. Stores account holder
  name, account number, and current balance.
- **Transaction** — foreign key to `BankAccount` (one account, many
  transactions). Stores type (deposit/withdrawal), amount, balance after the
  transaction, and a timestamp.

## Setup Instructions

1. Clone the repo:
   ```
   git clone <repo-url>
   cd bank_project
   ```

2. Create a virtual environment and install dependencies:
   ```
   python -m venv venv
   source venv/bin/activate      # on Windows: venv\Scripts\activate
   pip install -r requirements.txt
   ```

3. Run migrations (the sample `db.sqlite3` is already included, but this is
   safe to run anyway):
   ```
   python manage.py migrate
   ```

4. (Optional) Seed some sample data if you want a fresh demo account:
   ```
   python manage.py seed_data
   ```
   This creates a user `demo_user` with password `DemoPass123`, an account,
   and 8 sample transactions.

5. Run the server:
   ```
   python manage.py runserver
   ```

6. Open `http://127.0.0.1:8000/` in your browser. You'll be redirected to the
   login page. Either log in with the seeded demo account above, or click
   "Register here" to create your own.

### Admin login (for checking the database directly)

If you want to look at the data through Django admin, create a superuser:
```
python manage.py createsuperuser
```
then visit `http://127.0.0.1:8000/admin/`.

## Notes / Assumptions

- Each user gets exactly one bank account (the assignment says "manage their
  own bank account", singular), enforced with a `OneToOneField`.
- Balances and amounts use `DecimalField` instead of floats to avoid rounding
  weirdness with money.
- "Prevent overdraft" is implemented as a simple check in the withdraw view:
  if the withdrawal amount is more than the current balance, it's rejected
  with an error message and nothing is saved.
- The search/filter form on the transaction history page uses GET so the
  filters can be combined with pagination via the URL query string.
