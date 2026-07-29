# Secure Online Voting System

This is a web-based, decentralized voting platform built with Python, Flask, and MySQL. It was developed to solve the friction and security risks of manual elections by allowing any registered user to host isolated, password-protected elections. 

This project demonstrates strong relational database modeling, session management, state transitions, and fundamental web security practices.

## Core Features

* **Isolated Elections:** Any user can act as an Election Commission. Elections are partitioned using unique access passwords, acting like a private meeting room for voters.
* **Double-Vote Prevention:** The database schema completely separates voter identity from the live tally. A dedicated junction table securely records that a user has voted, enforcing a strict one-person-one-vote rule.
* **Blind Voting:** To prevent "bandwagon bias" (where voters pick whoever is currently winning), live tallies are hidden. Results can only be viewed after the Host officially publishes them.
* **Concurrency Safe:** Vote tallying is pushed down to the database engine using atomic updates. If a massive spike of users votes at the exact same millisecond, MySQL's row-level locking ensures zero votes are lost.
* **Enterprise-Grade Security:** 
  * User passwords are cryptographically hashed and salted using Werkzeug.
  * Application secrets and database credentials are fully abstracted using environment variables.
  * All form submissions are secured against Cross-Site Request Forgery (CSRF) using Flask-WTF.

## Tech Stack

* **Backend:** Python, Flask
* **Database:** MySQL (using `flask_mysqldb` and `DictCursor`)
* **Security:** `werkzeug.security` (hashing), `Flask-WTF` (CSRF protection), `python-dotenv` (secrets management)
* **Frontend:** Vanilla HTML, CSS, JavaScript, Bootstrap 5

## Database Schema Overview

The application relies on a normalized relational database containing five core tables:

* **users:** Source of truth for identity and authentication.
* **elections:** Manages election metadata, the host's ID, and the lifecycle state (active vs. published).
* **candidate_details:** The read-model for live vote tallies.
* **participants:** The immutable audit log that maps `user_id` to `election_id` to mathematically prevent double-voting.
* **results:** A historical snapshot table that permanently archives the final tallies of closed elections.

## Local Setup Instructions

To run this project on your local machine, you will need Python 3.x and a running instance of MySQL Server.

### 1. Clone the repository
Navigate to your desired directory in your terminal and clone the project files.

### 2. Install dependencies
It is recommended to use a virtual environment. Install the required Python libraries using the provided requirements file:
```bash
pip install -r requirements.txt
3. Setup the MySQL Database
Log into your local MySQL instance and create a new database. For example:

SQL
CREATE DATABASE project1;
USE project1;
You will need to create the five tables mentioned in the schema overview to match the SQL queries written in app1.py.

4. Configure Environment Variables
Create a file named .env in the root directory of the project (at the same level as app1.py). Add the following configuration, replacing the placeholder values with your actual database credentials:

Code snippet
# Database Configuration
MYSQL_HOST=localhost
MYSQL_USER=root
MYSQL_PASSWORD=your_local_mysql_password
MYSQL_DB=project1

# Flask Configuration
FLASK_DEBUG=true
FLASK_SECRET_KEY=your_generated_secret_key
Note: You must generate a secure random string for the FLASK_SECRET_KEY. You can do this by running the following command in your terminal and pasting the output into your .env file:

Bash
python -c "import secrets; print(secrets.token_hex(32))"
5. Run the Application
Start the Flask development server:

Bash
python app1.py
The application will be accessible in your web browser at http://127.0.0.1:5000.

Application Flow
Register/Login: Users must create an account to either host or participate in an election.

Host an Election: A logged-in user creates an election with a title, a unique password, and up to 4 candidates.

Participate: Voters enter the election password to access the voting booth.

Vote: Voters select a candidate. The system records the vote and locks the user out of voting in that specific election again.

Publish Results: The host goes to their dashboard and publishes the results, permanently locking the election and making the winner visible to all participants.
