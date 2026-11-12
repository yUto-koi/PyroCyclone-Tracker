# PyroCyclone Tracker

**A Myanmar disaster-monitoring dashboard for wildfires, cyclones, and community-reported hazards.**

PyroCyclone Tracker is an educational web application that helps users view disaster-related events across Myanmar on an interactive map. It combines NASA EONET event data with community-submitted hazard reports and provides tools for browsing incidents by region and reviewing reports.

> Created as part of the **7 Day Warm Up Program**.

## Features

- Interactive map displaying disaster and hazard events around Myanmar
- Region-based browsing for Myanmar states and regions
- NASA EONET API integration, with fallback sample events when the API is unavailable
- Community hazard reporting with location, category, risk level, and description
- User registration, login, and event bookmarking
- Admin tools for reviewing reports and updating their status

## Tech Stack

- **Backend:** Java Servlets, JSP, Maven
- **Frontend:** JSP, HTML, CSS, JavaScript, Bootstrap, Leaflet
- **Database:** MySQL
- **External data:** NASA EONET API
- **Application server:** Apache Tomcat (use a version compatible with the project's Servlet API and Java version)

## Project Contributors

- **Frontend:** **Kyaw Nyein Chan Htwe** — frontend development and dashboard interface
- **Backend:** **Lwin Ko Ko Aung** — backend development and server-side functionality

## Database Setup

Create the database, then select it before running the schema below. These tables cover user accounts, cached NASA events and their map coordinates, bookmarks, and community-reported hazards.

```sql
CREATE DATABASE pyrocyclone_tracker;
USE pyrocyclone_tracker;

-- 1. User accounts
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    role ENUM('user', 'admin') NOT NULL DEFAULT 'user',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 2. Events table (local cache for NASA EONET data)
CREATE TABLE events (
    id VARCHAR(50) PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    category VARCHAR(50) NOT NULL,
    status VARCHAR(20) NOT NULL,
    source_link VARCHAR(500),
    event_date TIMESTAMP NOT NULL,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

-- 3. Event geometries (coordinates and mapping points)
CREATE TABLE event_geometries (
    id INT AUTO_INCREMENT PRIMARY KEY,
    event_id VARCHAR(50) NOT NULL,
    latitude DECIMAL(10, 8) NOT NULL,
    longitude DECIMAL(11, 8) NOT NULL,
    geometry_date TIMESTAMP NOT NULL,
    FOREIGN KEY (event_id) REFERENCES events(id) ON DELETE CASCADE
);

-- 4. User bookmarks (saved events)
CREATE TABLE user_bookmarks (
    user_id INT NOT NULL,
    event_id VARCHAR(50) NOT NULL,
    saved_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (user_id, event_id),
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (event_id) REFERENCES events(id) ON DELETE CASCADE
);

-- 5. Community-submitted hazard reports
CREATE TABLE reported_disasters (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    category VARCHAR(50) NOT NULL,
    risk_level VARCHAR(50) NOT NULL,
    description TEXT,
    latitude DECIMAL(10, 8) NOT NULL,
    longitude DECIMAL(11, 8) NOT NULL,
    recorded_time DATETIME NOT NULL,
    reporter_name VARCHAR(100) DEFAULT 'Anonymous Operator',
    status VARCHAR(20) DEFAULT 'Pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

The bookmarks table references events stored in `events`; ensure an event exists in that table before inserting a bookmark. The application's NASA integration may retrieve events directly from the API, so implement or enable event-cache synchronization if you intend to use `events` and `event_geometries` as a persistent cache.

## Getting Started

### Requirements

- Java 17 or a compatible JDK
- Maven
- MySQL
- Apache Tomcat compatible with the project's Servlet API and Java version

### Run locally

1. Clone or download the project and open a terminal in the project directory.
2. Create the MySQL database and tables using the SQL schema above.
3. Configure the application to connect to your local database. Keep database credentials in secure, environment-specific configuration and out of source control.
4. Build and run the application using the Maven/Tomcat setup configured for your environment.
5. Open the local application URL in your browser.

> The original project README describes `mvn clean tomcat7:run`. Check that the configured Tomcat plugin and Java version are compatible in your environment before running the command.

## Data and Disclaimer

NASA EONET provides event data through its API. If the API cannot be reached, the application may display fallback sample events. **Fallback/sample events are for demonstration only and must not be treated as verified, real-time emergency information.** Confirm hazards and safety instructions through official local authorities and trusted emergency services.

Community-submitted reports may require administrative review before being shown as verified. This educational project is not a replacement for official disaster alerts or emergency response systems.


## Program

Built during the **7 Day Warm Up Program** as a collaborative project to explore disaster tracking, interactive mapping, and full-stack web development.