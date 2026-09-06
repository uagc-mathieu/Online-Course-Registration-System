# Online Course Registration System

A full-stack web application that lets students create an account, browse courses by term, enroll or join a waitlist when a course is full, and drop courses they no longer need — built as the final capstone project for **CST 499: Capstone for Computer Software Technology** at the University of Arizona Global Campus.

## About

Self-service course registration for students, built with PHP, MySQL, and Bootstrap, designed from a formal SRS through five UML models before implementation.

## Overview

This repository contains the complete software development lifecycle for the registration system, from requirements through a working application:

1. **Requirements** — a Software Requirements Specification (SRS) defining eight functional requirements (REQ-1–REQ-8) across three feature areas: registration & authentication, enrollment & waitlisting, and cancellation.
2. **Design** — five UML models (use case, class, sequence, activity, and state diagrams) translating those requirements into a system design.
3. **Implementation** — a PHP/MySQL application built and tested locally with XAMPP: a public landing page, account registration, login, course browsing/registration, and schedule management.
4. **Documentation** — every deliverable below was revised based on instructor feedback and compiled for final submission, alongside a presentation and video walkthrough.

## Features

- Student account registration with server- and database-level duplicate-ID protection
- Secure login with bcrypt password hashing and session-based authentication
- Course browsing by term (Spring, Summer, Fall) with live seat counts
- Automatic waitlisting when a course is full, and automatic promotion of the next waitlisted student when a seat opens
- Schedule management — view enrolled/waitlisted courses and drop a course

## Tech Stack

| Layer | Technology |
|---|---|
| Language | PHP (PDO for all database access) |
| Database | MySQL / MariaDB |
| Front end | HTML, Bootstrap |
| Local environment | XAMPP (Apache + MySQL + PHP) |
| Design | UML (use case, class, sequence, activity, state diagrams) |

## Project Structure

```
CST499-Software-Project/
├── 01_SRS_Document.docx
├── 02_UML_Design_Model.docx
├── 03_Landing_Login_Enrollment_Pages.docx
├── 04_MySQL_Database_and_Class_Registration.docx
├── 05_PHP_Code.docx
├── 06_References.docx
├── 07_Final_Project_Presentation.pptx
├── 08_Video_Presentation_Script.docx
└── htdocs/                # Working application — copy or clone into XAMPP's htdocs
    ├── css/                # Stylesheets for the Bootstrap-based pages
    ├── includes/
    │   ├── config.php      # DB_HOST, DB_PORT, DB_NAME, DB_USER, DB_PASS
    │   ├── Database.php    # Shared PDO connection class (singleton)
    │   ├── header.php      # Shared navigation / session-aware header
    │   └── footer.php      # Shared footer
    ├── sql/                # Database schema (see Database Schema below)
    ├── landing.php         # Public entry point
    ├── register.php        # Account registration (REQ-1, REQ-2)
    ├── login.php           # Authentication (REQ-3)
    ├── logout.php          # Session teardown
    ├── courses.php         # Course browsing & enrollment/waitlist (REQ-4–REQ-6)
    └── schedule.php        # View schedule, drop courses, waitlist promotion (REQ-7, REQ-8)
```

## Database Schema

Three related tables (see `htdocs/sql/` for the full schema file):

```sql
CREATE TABLE users (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id VARCHAR(30) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    full_name VARCHAR(100) NOT NULL,
    phone_number VARCHAR(20) NOT NULL,
    email VARCHAR(150) NOT NULL UNIQUE,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB;

CREATE TABLE courses (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    course_code VARCHAR(10) NOT NULL,
    title VARCHAR(150) NOT NULL,
    term ENUM('Spring', 'Summer', 'Fall') NOT NULL,
    seat_limit INT UNSIGNED NOT NULL
);

CREATE TABLE enrollments (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL,
    course_id INT UNSIGNED NOT NULL,
    status ENUM('enrolled', 'waitlisted') NOT NULL,
    UNIQUE KEY uniq_user_course (user_id, course_id),
    FOREIGN KEY (user_id) REFERENCES users(id),
    FOREIGN KEY (course_id) REFERENCES courses(id)
);
```

## Getting Started

1. Install [XAMPP](https://www.apachefriends.org/) and start the Apache and MySQL modules from the XAMPP Control Panel.
2. Copy the contents of this repository's `htdocs/` folder into XAMPP's own `htdocs` directory (e.g. `C:\xampp\htdocs\course_registration\`).
3. Open phpMyAdmin and run the SQL in `htdocs/sql/` (or the schema above) to create the `course_registration` database and its tables.
4. Update `htdocs/includes/config.php` with your local `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, and `DB_PASS`.
5. Visit `http://localhost/course_registration/landing.php` in your browser.

## Security Notes

- All database queries use PDO prepared statements with emulated prepares disabled, so bound values are sent as real parameters rather than interpolated into SQL — the primary defense against SQL injection.
- Passwords are hashed with bcrypt (`password_hash()` / `password_verify()`) and never stored or compared in plain text.
- Login failures return a single generic message so a login attempt can't be used to discover which IDs are already registered.

## Known Limitation

The confirmation window a waitlisted student would have to accept an open seat before it passes to the next student (REQ-8) is not yet defined in the SRS (see Appendix C, Issues List) and is flagged as an open item for stakeholder input. The current implementation promotes the next waitlisted student immediately rather than holding the seat.

## Documentation

Full project documentation — the SRS, UML design model, implementation papers, compiled PHP/SQL source, APA references, presentation slides, and video script — is included in the repository root as files `01`–`08` above.

## Author

**Mathieu Bilal**
University of Arizona Global Campus
CST 499: Capstone for Computer Software Technology — Dr. Butler
September 2026
