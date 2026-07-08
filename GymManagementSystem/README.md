# Gym Management System

A Spring Boot MVC web application for managing gym services, time slots, customer bookings, user roles, and feedback. The system supports two main user roles:

- Admin: manages gym services, slots, bookings, customers, and feedback
- Customer: registers/login, views available slots, books services, and views personal bookings

This project is built with Java, Spring Boot, Spring Security, JPA/Hibernate, MySQL, and JSP.

---

## 1. Project Overview

The Gym Management System helps a gym administration manage its offerings and seat-based slot bookings. It provides a web-based workflow where:

- Admins add and update gym services
- Admins create and manage slot schedules and pricing
- Customers can view available slots and book seats
- Booking counts are tracked per slot-service combination
- Customers can cancel their bookings
- Feedback is collected and displayed for review
- Customer accounts are registered and authenticated securely

---

## 2. Tech Stack

- Java 17
- Spring Boot 2.7.16
- Spring MVC
- Spring Security
- Spring Data JPA
- Hibernate
- MySQL Database
- JSP + JSTL
- Maven
- Tomcat (via Spring Boot / WAR packaging)

---

## 3. Project Architecture

The application follows a layered MVC architecture:

- Controller Layer: handles web requests and returns JSP pages
- Service Layer: contains business logic for item/slot operations and user handling
- DAO Layer: abstracts database access using repositories
- Entity Layer: maps Java classes to database tables
- View Layer: JSP files under WEB-INF/JSP

### Main Packages

- com.sppm.GymManagementSystem.controller
- com.sppm.GymManagementSystem.service
- com.sppm.GymManagementSystem.dao
- com.sppm.GymManagementSystem.bean
- com.sppm.GymManagementSystem.config
- com.sppm.GymManagementSystem.exception

---

## 4. Main Modules and Functionalities

### 4.1 Authentication and User Management

Features:
- User registration
- Login and logout using Spring Security
- Role-based navigation (Admin/Customer)
- Customer list view for admin
- Customer deletion from admin panel

Key components:
- loginController
- SecurityConfig
- GymUserService
- GymUserRepository
- GymUser

### 4.2 Gym Service Management

Features:
- Add new gym services
- View all services in a report page
- Update existing gym item details
- Manage total seat capacity for each service

Main entities:
- GymItem

### 4.3 Slot Management

Features:
- Create gym slots with time and pricing
- View all slots for admin and customers
- Update slot details
- Link services to slots through SlotItem records

Main entities:
- Slot
- SlotItem
- SlotItemEmbed

### 4.4 Booking Management

Features:
- Book a service in a selected slot
- Prevent duplicate booking for the same slot by the same user
- Enforce seat availability
- Display booking success/failure pages
- View customer bookings
- View admin booking reports
- Cancel bookings and release seat count

Main entities:
- GymBook

### 4.5 Feedback Management

Features:
- Submit feedback with username and content
- Store feedback with timestamp
- View all feedback entries

Main entity:
- Feedback

---

## 5. Database Design

The application uses MySQL and JPA/Hibernate to persist data.

### Main Tables / Entities

- GymUser
  - username (primary key)
  - password
  - firstName
  - lastName
  - email
  - type

- GymItem
  - itemId (primary key)
  - itemName
  - totalSeat

- Slot
  - slotId (primary key)
  - slotTime
  - pricing

- SlotItem
  - composite key: slotId + itemId
  - seatBooked

- GymBook
  - bookingId (primary key)
  - slotId
  - itemId
  - userName

- Feedback
  - feedbackId (primary key)
  - username
  - feedbackContent
  - timestamp

### Relationship Summary

- One slot can have many services via SlotItem
- One service can be assigned to many slots
- A booking records a customer booking for one slot-service combination
- Feedback belongs to a user who submitted it

---

## 6. Application Flow

### Customer Flow

1. User registers an account
2. User logs in
3. User sees the dashboard based on role
4. Customer views available slots and services
5. Customer books a seat for a slot-service combination
6. Booking is stored and seat count is updated
7. Customer can view or cancel personal bookings

### Admin Flow

1. Admin logs in
2. Admin adds/updates gym services
3. Admin creates slots and pricing
4. Admin views booking reports and customer records
5. Admin views feedback entries
6. Admin can manage or delete customer records

---

## 7. Web Endpoints / Routes

This project uses MVC controller endpoints that return JSP views rather than a REST JSON API.

### Authentication and Pages

| Method | Path | Purpose |
|---|---|---|
| GET | /register | Show registration page |
| POST | /register | Register a new user |
| GET | /loginpage | Show login page |
| GET | /loginerror | Show login error page |
| POST | /login | Spring Security login processing |
| GET | /logout | Logout endpoint |

### Main Dashboard

| Method | Path | Purpose |
|---|---|---|
| GET | /index | Redirects to admin/customer dashboard |

### Gym Services

| Method | Path | Purpose |
|---|---|---|
| GET | /gymServiceEntryPage | Show form to add gym service |
| POST | /gymService | Save new gym service |
| GET | /gymServiceReport | View all gym services |
| GET | /updateGymItem/{id} | Show update form for a service |
| POST | /updateGymItem | Update details of a service |

### Slots

| Method | Path | Purpose |
|---|---|---|
| GET | /gymSlotEntryPage | Show form to add a new slot |
| POST | /gymSlot | Save a new slot and initialize slot-service mappings |
| GET | /admin-gymSlotReport | View all slots for admin |
| GET | /customer-gymSlotReport | View all slots for customer |
| GET | /slot-item-add/{id} | Add a service to all existing slots |
| GET | /slot-book/{id} | Show booking page for a slot |
| GET | /slot-update/{id} | Show update form for a slot |
| POST | /slot-update | Update slot details |

### Booking

| Method | Path | Purpose |
|---|---|---|
| POST | /slot-book | Book a slot-service combination for current customer |
| POST | /admin-slot-book | Book a slot-service combination as admin |
| GET | /adminBookingDetails | Show all bookings to admin |
| GET | /customerBooking | Show current customer bookings |
| GET | /cancel-booking/{id} | Cancel a customer booking |
| GET | /delete-booking/{id} | Delete booking from admin view |

### Feedback

| Method | Path | Purpose |
|---|---|---|
| GET | /feedback | Show feedback form |
| POST | /feedback | Submit feedback |
| GET | /Feedback-Details | View all feedback |

### Customer Admin Management

| Method | Path | Purpose |
|---|---|---|
| GET | /customer-details | View all customers |
| GET | /deleteCustomer/{username} | Delete a customer |

---

## 8. Core Java Classes

### Controllers

- GymController: handles all gym operations, bookings, reports, customer/admin pages, feedback, and updates
- loginController: handles registration and login-related views

### Services

- GymItemService: builds item lists with seat availability and adds new items to slot mappings
- GymUserService: loads users for authentication and provides user role information

### Repositories / DAOs

- GymItemDao / GymItemDaoImpl
- SlotDao / SlotDaoImpl
- SlotItemDao / SlotItemDaoImpl
- GymBookDao / GymBookDaoImpl
- FeedbackDao / FeedbackDaoImpl
- GymUserRepository

---

## 9. Security Configuration

The project uses Spring Security with form-based login.

Key security behavior:
- Public access is allowed only to registration page
- All other pages require authentication
- Login page is custom: /loginpage
- Login error page: /loginerror
- Passwords are stored using BCrypt hashing

---

## 10. Setup and Installation

### Prerequisites

- Java 17 or higher
- Maven
- MySQL Server
- IDE such as IntelliJ IDEA / Eclipse / VS Code

### Database Setup

1. Create a MySQL database named GymDB
2. Make sure MySQL is running
3. Update credentials in src/main/resources/application.properties if needed

Default configuration:

- URL: jdbc:mysql://localhost:3306/GymDB
- Username: root
- Password: root

### Run the Application

From the project root:

```bash
./mvnw spring-boot:run
```

Or on Windows:

```bash
mvnw.cmd spring-boot:run
```

Then open:

```text
http://localhost:7070/loginpage
```

---

## 11. Project Structure

```text
src/
  main/
    java/
      com/sppm/GymManagementSystem/
        bean/
        config/
        controller/
        dao/
        exception/
        service/
    resources/
      application.properties
    webapp/
      WEB-INF/
        JSP/
```

---

## 12. Notes and Limitations

- The project is primarily a server-rendered MVC web application, not a REST API-based backend
- It uses JSP pages for the UI
- Some parts of the code use direct repository access inside controllers/DAOs rather than a fully decoupled service architecture
- The application currently depends on a MySQL database and local configuration

---

## 13. Future Enhancements

Possible improvements for the project:

- Add proper admin role authorization checks
- Improve exception handling and error pages
- Add REST API support for mobile/web clients
- Add unit and integration tests
- Improve UI with modern frontend technologies
- Add reporting dashboards and charts
- Add payment integration for bookings

---

## 14. Summary

This Gym Management System provides a complete web-based workflow for managing gym services, slots, bookings, customer accounts, and feedback. It is suitable for a basic gym administration system and can be extended into a more advanced booking and membership platform.
