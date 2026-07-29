# Election Database Management System

This is a web-based decentralized voting platform built to solve the friction and security risks of manual elections. It allows users to host isolated, password-protected elections, cast votes securely, and calculate results instantly without risking duplicate votes or database race conditions. 

It was developed as a database microproject to demonstrate strict relational data modeling, session management, and state transitions.

---

## The Tech Stack

*   **Backend:** Python, Flask
*   **Database:** MySQL (using `flask_mysqldb`)
*   **Frontend:** Vanilla HTML, CSS, JavaScript
*   **Data Visualization:** Streamlit (used for internal administrative retrieval of voter and candidate records)

---

## Core Features

*   **Isolated Elections:** Any registered user can act as an Election Commission. Elections are partitioned using unique access passwords, acting like a private meeting room for voters.
*   **Double-Vote Prevention:** The database schema completely separates voter identity from the live tally. A dedicated junction table securely records that a user has voted, enforcing a strict one-person-one-vote rule without compromising ballot secrecy.
*   **Blind Voting:** To prevent "bandwagon bias" (where voters pick whoever is currently winning), live tallies are hidden. The database state only allows results to be viewed after the Host officially publishes them.
*   **Concurrency Control:** Vote tallying is pushed down to the database engine using atomic updates. If a massive spike of users votes at the exact same millisecond, MySQL's row-level locking ensures zero votes are lost.

---

## Database Schema Overview

The application relies on a normalized relational database (3NF) containing five core tables:

1.  **users:** Source of truth for identity and authentication.
2.  **elections:** Manages election metadata, the host's ID, and the lifecycle state (active vs. published).
3.  **candidate_details:** The read-model for live vote tallies.
4.  **participants:** The immutable audit log that maps `user_id` to `election_id` to mathematically prevent double-voting.
5.  **results:** A historical snapshot table that permanently archives the final tallies of closed elections.

---

## Local Setup Instructions

To run this project on your local machine, you will need Python 3.x and a running instance of MySQL Server.

### 1. Clone the repository
Navigate to your desired directory in the terminal and clone the project files.

### 2. Install dependencies
Install the required Python libraries. You can do this via pip:
`pip install Flask Flask-MySQLdb Werkzeug streamlit`

### 3. Setup the MySQL Database
Log into your local MySQL instance and create a new database:
`CREATE DATABASE project1;`
`USE project1;`

You will need to create the five tables mentioned in the schema overview. (Ensure your table column names match the SQL queries in `app1.py`).

### 4. Configure Application Secrets
Open `app1.py` in your text editor. Locate the MySQL configuration block at the top of the file and update it with your local database credentials:

`app.config['MYSQL_HOST'] = 'localhost'`
`app.config['MYSQL_USER'] = 'root'`
`app.config['MYSQL_PASSWORD'] = 'your_local_password'`
`app.config['MYSQL_DB'] = 'project1'`

*Note: The current build has hardcoded secrets for local development speed. In a production environment, these must be moved to a `.env` file.*

### 5. Run the Application
Start the Flask development server:
`python app1.py`

The application will be accessible in your web browser at `http://127.0.0.1:5000`.

---

## Development Team
This project was developed over a 3-month timeline by a team of four members, focusing on backend routing, relational data integrity, and seamless data retrieval.
