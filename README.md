# Fiszki - Spaced Repetition Flashcard Application

[![Java CI with Maven](https://github.com/beowoolf/flashcards/actions/workflows/ci.yml/badge.svg)](https://github.com/beowoolf/flashcards/actions/workflows/ci.yml)
[![Java Version](https://img.shields.io/badge/Java-25-orange.svg)](https://openjdk.org/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.5.16-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![Database](https://img.shields.io/badge/Database-MariaDB%2010.6-blue.svg)](https://mariadb.org/)
[![Docker](https://img.shields.io/badge/Docker-Ready-2496ED.svg?logo=docker&logoColor=white)](https://www.docker.com/)
[![License](https://img.shields.io/badge/License-Unspecified-lightgrey.svg)](#8-license)

> Modern web application for effective learning through intelligent flashcards, powered by the **SuperMemo-2 (SM-2)** spaced repetition algorithm. Built with **Spring Boot** and **Java 25**.

---

## Table of Contents

- [1. Project Name](#1-project-name)
- [2. Project Description](#2-project-description)
  - [Core Concept](#core-concept)
  - [Key Features](#key-features)
  - [System Architecture](#system-architecture)
- [3. Tech Stack](#3-tech-stack)
- [4. Getting Started Locally](#4-getting-started-locally)
  - [Prerequisites](#prerequisites)
  - [Option A: Running with Docker Compose (Recommended)](#option-a-running-with-docker-compose-recommended)
  - [Option B: Running Standalone on Host Machine](#option-b-running-standalone-on-host-machine)
- [5. Available Scripts](#5-available-scripts)
  - [Maven Commands (Host)](#maven-commands-host)
  - [Docker & Docker Compose Commands](#docker--docker-compose-commands)
- [6. Project Scope](#6-project-scope)
  - [Implemented Modules & Endpoints](#implemented-modules--endpoints)
  - [Database Schema](#database-schema)
  - [Future Roadmap](#future-roadmap)
- [7. Project Status](#7-project-status)
- [8. License](#8-license)

---

## 1. Project Name

**Fiszki** (Polish for *Flashcards*) — Spaced Repetition Learning System.

*Repository:* [`beowoolf/flashcards`](https://github.com/beowoolf/flashcards)

---

## 2. Project Description

**Fiszki** is a web-based learning platform developed to facilitate long-term knowledge retention. By leveraging the scientifically proven **SuperMemo-2 (SM-2)** spaced repetition algorithm, the application calculates optimal review intervals for each flashcard based on individual recall difficulty.

### Core Concept

When studying with flashcards, remembering items is not binary. The SM-2 algorithm evaluates recall quality on a 1–5 scale:
- **1 (Very Bad) / 2 (Bad):** The card interval resets to 1 day, and the ease factor is reduced.
- **3 (Passable) / 4 (Good) / 5 (Perfect):** The card advances to longer intervals (1 day $\rightarrow$ 6 days $\rightarrow$ $n \times \text{Ease Factor}$), optimizing study time and preventing forgetting.

### Key Features

- 🔐 **Authentication & Authorization:** Secure user registration and login handled by Spring Security with BCrypt password hashing.
- 🗂️ **Flashcard Management:** Complete CRUD operations (create, view, edit, delete) tied to individual user accounts for total privacy.
- 🧠 **Adaptive Review Session:** Dynamic queue displaying only flashcards due for review based on their scheduled `nextReviewDate`.
- 🎓 **Learned Cards Tracking:** Dedicated view for mastered cards, allowing manual status management.
- 📊 **Learning Dashboard & Statistics:** Live metrics detailing total flashcards, cards ready for review, learned cards, and cumulative review count.
- 📱 **Clean Responsive UI:** Server-side rendered views using Thymeleaf layouts and Bootstrap 5.3.

### System Architecture

The application adopts the classic layered Spring MVC architecture:
- **Presentation Layer:** Spring MVC controllers (`FlashcardController`, `AuthController`) serving server-rendered Thymeleaf templates.
- **Service Layer:** Business domain logic (`FlashcardService`, `UserService`), orchestrating SM-2 interval calculations and user transactions.
- **Data Access Layer:** Spring Data JPA repositories with Hibernate ORM (`FlashcardRepository`, `UserRepository`).
- **Database:** MariaDB 10.6 for relational data persistence.

---

## 3. Tech Stack

| Category | Technology / Library | Version | Description |
| :--- | :--- | :--- | :--- |
| **Language** | Java | `25` | Modern OpenJDK LTS |
| **Framework** | Spring Boot | `3.5.16` | Core backend framework |
| **Security** | Spring Security | `6.x` | Form login, BCrypt password encoder, CSRF |
| **Persistence** | Spring Data JPA / Hibernate | Included in Boot | Object-relational mapping and repository abstraction |
| **Database** | MariaDB | `10.6` | Relational SQL database |
| **Database Driver** | MariaDB Java Client | `3.x` | JDBC driver for MariaDB |
| **Frontend / Templates**| Thymeleaf + Bootstrap | `5.3` | Server-side templating engine with modern CSS framework |
| **Utilities** | Project Lombok | `1.18.30` | Annotation processor for boilerplate reduction |
| **Testing** | JUnit 5 & Mockito | Included in Boot | Unit and slice testing |
| **Integration Testing**| Testcontainers | `1.19.3` | Containerized MariaDB instances for integration tests |
| **Containerization** | Docker & Docker Compose | Multi-stage | Optimized container deployment (`temurin-25`) |
| **CI / CD** | GitHub Actions | Workflows | Automated Maven compilation and test suite |

---

## 4. Getting Started Locally

### Prerequisites

Ensure you have the following installed on your system:
- **Git** (for version control)
- **Java Development Kit (JDK) 25** (if running without Docker)
- **Docker & Docker Compose** (recommended for containerized execution)

---

### Option A: Running with Docker Compose (Recommended)

Running via Docker Compose sets up the MariaDB database and the application without requiring local JDK or database installations.

1. **Clone the repository:**
   ```bash
   git clone https://github.com/beowoolf/flashcards.git
   cd flashcards
   ```

2. **Start the services:**
   ```bash
   docker-compose up --build -d
   ```

3. **Verify running containers:**
   ```bash
   docker-compose ps
   ```

4. **Access the application:**
   Open your browser and navigate to:
   ```
   http://localhost:8080
   ```

5. **Stop the services:**
   ```bash
   docker-compose down
   ```
   *(To wipe stored database data as well, add the `-v` flag: `docker-compose down -v`)*

---

### Option B: Running Standalone on Host Machine

If you prefer running the Spring Boot application directly on your workstation:

1. **Start a MariaDB instance:**
   You can quickly spin up just the database container:
   ```bash
   docker run -d --name fiszki-mariadb \
     -p 3306:3306 \
     -e MARIADB_DATABASE=fiszki \
     -e MARIADB_USER=fiszki \
     -e MARIADB_PASSWORD=fiszki \
     -e MARIADB_ROOT_PASSWORD=root \
     mariadb:10.6
   ```

2. **Configure Database Connection (Optional):**
   Default settings are configured in `src/main/resources/application.properties`:
   ```properties
   spring.datasource.url=jdbc:mariadb://localhost:3306/fiszki
   spring.datasource.username=fiszki
   spring.datasource.password=fiszki
   ```

3. **Build and start the application:**
   - **Linux / macOS:**
     ```bash
     ./mvnw clean spring-boot:run
     ```
   - **Windows:**
     ```powershell
     .\mvnw.cmd clean spring-boot:run
     ```

4. **Visit the application:**
   Navigate to `http://localhost:8080` in your web browser.

---

## 5. Available Scripts

The project includes the Maven Wrapper (`./mvnw` and `mvnw.cmd`) and Docker orchestration files. Below are the most common commands:

### Maven Commands (Host)

| Command (Linux/macOS) | Command (Windows) | Description |
| :--- | :--- | :--- |
| `./mvnw clean compile` | `.\mvnw.cmd clean compile` | Compiles source files and processes Lombok annotations |
| `./mvnw test` | `.\mvnw.cmd test` | Executes all unit and Testcontainers integration tests |
| `./mvnw clean package` | `.\mvnw.cmd clean package` | Builds an executable JAR file in `target/` |
| `./mvnw clean package -DskipTests` | `.\mvnw.cmd clean package -DskipTests` | Packages the application skipping test suite |
| `./mvnw spring-boot:run` | `.\mvnw.cmd spring-boot:run` | Boots up the development server on port 8080 |

### Docker & Docker Compose Commands

| Command | Description |
| :--- | :--- |
| `docker-compose up -d` | Starts MariaDB and application containers in the background |
| `docker-compose up --build` | Rebuilds the application Docker image and starts the stack |
| `docker-compose logs -f app` | Tails live application logs |
| `docker-compose down` | Stops and removes stack containers and networks |
| `docker build -t fiszki:latest .` | Manually builds the multi-stage Docker image |

---

## 6. Project Scope

### Implemented Modules & Endpoints

| URL Path | HTTP Method | Access | Description |
| :--- | :---: | :---: | :--- |
| `/auth/login` | `GET` | Public | Displays the user login form |
| `/auth/login` | `POST` | Public | Authenticates credentials and starts user session |
| `/auth/register` | `GET` | Public | Displays registration form |
| `/auth/register` | `POST` | Public | Creates a new user account with BCrypt password hashing |
| `/auth/logout` | `POST` | Authenticated | Terminates active session and redirects to login |
| `/flashcards` | `GET` | Authenticated | Main dashboard: flashcard list and study statistics |
| `/flashcards/new` | `GET` | Authenticated | Form to create a new flashcard |
| `/flashcards` | `POST` | Authenticated | Persists new flashcard assigned to logged-in user |
| `/flashcards/{id}/edit` | `GET` | Authenticated | Edit form for an existing flashcard |
| `/flashcards/{id}` | `POST` | Authenticated | Updates flashcard front/back and learning status |
| `/flashcards/{id}/delete` | `POST` | Authenticated | Deletes specified flashcard |
| `/flashcards/{id}/learned` | `POST` | Authenticated | Marks card as learned and advances next review date |
| `/flashcards/review` | `GET` | Authenticated | Review interface showing cards due for revision |
| `/flashcards/{id}/review` | `POST` | Authenticated | Submits grade (1–5) and updates SM-2 interval |
| `/flashcards/learned` | `GET` | Authenticated | Displays collection of mastered flashcards |

### Database Schema

- **`users` Table:**
  - `id` (BIGINT, PK, Auto-Increment)
  - `username` (VARCHAR, Unique, Not Null)
  - `password` (VARCHAR, BCrypt encoded, Not Null)
  - `email` (VARCHAR, Not Null)
- **`flashcards` Table:**
  - `id` (BIGINT, PK, Auto-Increment)
  - `front` (VARCHAR, Not Null)
  - `back` (VARCHAR, Not Null)
  - `user_id` (BIGINT, FK $\rightarrow$ `users.id`)
  - `is_learned` (BOOLEAN, default `false`)
  - `next_review_date` (DATETIME, default `NOW()`)
  - `review_count` (INT, default `0`)
  - `last_review_date` (DATETIME)
  - `ease_factor` (DOUBLE, default `2.5`)
  - `interval_days` (INT, default `0`)

### Future Roadmap

- [ ] Category / Deck tagging system for organizing flashcards by topic.
- [ ] Rich text, Markdown, or media (image/audio) support on card sides.
- [ ] Export & import functionality (e.g. Anki `.apkg` or CSV files).
- [ ] RESTful API endpoints with JWT tokens for standalone frontend / mobile clients.
- [ ] Dark mode toggle in the navigation bar.

---

## 7. Project Status

- **Status:** Active development / Stable Prototype
- **Current Version:** `0.0.1-SNAPSHOT`
- **Origin:** Created as part of the **10xDevs** training program to demonstrate clean Spring Boot architecture, automated testing with Testcontainers, and modern Java best practices.
- **CI / Pipeline:** Continuous Integration active via GitHub Actions on `main` branch.

---

## 8. License

This project is currently distributed without an explicit open-source license. All rights are reserved by the original author and contributors. 

If you wish to use, modify, or redistribute this software, please contact the repository maintainer at [beowoolf/flashcards](https://github.com/beowoolf/flashcards).