# Secure Online Voting System

The Secure Online Voting System is a decentralized voting platform built to solve the friction and security risks of manual elections. Instead of relying on vulnerable paper ballots or easily shared passwords, it allows any registered user to host isolated, strictly access-controlled elections.

Under the hood, it relies on a normalized MySQL database to enforce mathematical accuracy and prevent double-voting, all tied together with a Python Flask backend and a responsive Bootstrap web interface.

## How It's Built

Here is a quick look at how the project is structured:

- **Backend Architecture (`app1.py`)**: A Flask-based monolithic application that handles secure session management, cryptographic password hashing (via Werkzeug), and routes all election traffic.
- **Database (MySQL)**: The core engine of the system. It uses a heavily partitioned relational schema (as mapped out in `image_aec2ba.png`) featuring tables for `users`, `elections`, `allowed_voters`, `candidates`, `participants`, and `results`. It utilizes atomic SQL updates to ensure concurrency safety—meaning zero votes are lost even if hundreds of users vote at the exact same millisecond.
- **Web Dashboard (`/templates`)**: A clean, vanilla HTML/CSS and Bootstrap 5 interface served by Flask. It provides dedicated views for hosting elections, casting blind votes, and viewing historical results.

## What You Need

Before getting started, just make sure you have the following installed on your machine:

- Python 3.x
- MySQL Server (Running locally)
- Git (optional, if you want to clone the repository)

## Running the Project Locally

I've tried to keep the setup as straightforward as possible. Follow these steps to get the environment running.

### 1. Install Dependencies

Open your terminal in the root directory of the project and install the required Python packages:

```bash
pip install -r requirements.txt
```

### 2. Setup the MySQL Database

Log into your local MySQL instance and create a new database for the project:

```sql
CREATE DATABASE project1;
USE project1;
```

> **Note:** You will need to create the tables mentioned in the schema overview (`users`, `elections`, `allowed_voters`, `candidates`, `participants`, `results`) to match the SQL queries written in `app1.py`.

### 3. Configure Environment Variables

Create a file named `.env` in the root directory of the project (at the same level as `app1.py`). Add the following configuration, replacing the placeholder values with your actual local database credentials:

```
# Database Configuration
MYSQL_HOST=localhost
MYSQL_USER=root
MYSQL_PASSWORD=your_local_mysql_password
MYSQL_DB=project1

# Flask Configuration
FLASK_DEBUG=true
FLASK_SECRET_KEY=your_generated_secret_key
```

> **Tip:** You must generate a secure random string for the `FLASK_SECRET_KEY`. You can do this by running the following command in your terminal and pasting the output into your `.env` file:

```bash
python -c "import secrets; print(secrets.token_hex(32))"
```

### 4. Start the Backend Server

Once your database is configured and your secrets are set, you can boot up the Flask backend. From the root directory, run:

```bash
python app1.py
```

If everything went well, the server should now be running locally on `http://localhost:5000`.

## Using the Voting System

With the Flask server running, open your browser and navigate to `http://localhost:5000`.

From the homepage, you can register for an account using your email address. Once logged in, you can choose to either act as a **Host** or a **Participant**.

If you choose to host, you can create a new election, define the candidates, and provision a strict Access List (ACL) by inputting the registered email addresses of authorized voters.

When participants attempt to join an election, the backend cross-references their authenticated session against the host's `allowed_voters` list. Once verified, they can cast their vote securely. To prevent "bandwagon bias," live tallies are completely hidden until the host officially decides to close the election and hit **Publish Results**, permanently locking the historical snapshot for everyone to view.
