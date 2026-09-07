# Train Reservation System (Java Swing + JDBC + SQLite)

A GUI desktop application for booking and cancelling train reservations.

## Tech Stack
- Java 17+ (Swing for GUI)
- JDBC with MySQL (`com.mysql:mysql-connector-j`) — requires a MySQL server running locally
- Maven for build/dependency management

> This version connects to MySQL. Before running it, install MySQL
> Community Server (or XAMPP/WAMP, which bundle MySQL) and edit the four
> `DB_*` constants at the top of `DatabaseManager.java` to match your
> local username/password. The app creates the `reservation_db` database
> and its tables automatically the first time it runs.

## Features
- **Login form** — username/password checked against the `users` table (passwords stored as SHA-256 hashes, never plain text). Invalid credentials are rejected with an "Access denied" message.
- **Reservation form** — passenger name, train number, train name (auto-filled by looking up the train number when you tab out of the field), class type, date of journey, source and destination stations.
- **Book button** — validates all fields, saves the booking, and generates a unique 10-digit PNR, then shows a confirmation dialog with the full booking summary.
- **Cancellation form** — enter a PNR and click **Fetch Booking** to load and display the full details (read-only) of that reservation.
- **Cancel button** — asks "Are you sure?" before permanently deleting the booking from the database.
- **Input validation** — no required field can be blank, the journey date must be a real date in `yyyy-MM-dd` format and not in the past, train number and PNR must be numeric, and source/destination can't match.
- All SQL is executed through `PreparedStatement`, which protects against SQL injection.

## Project Structure
```
reservation-system/
├── pom.xml
└── src/main/java/com/reservation/
    ├── Main.java                  # entry point
    ├── db/DatabaseManager.java    # connection + schema + seed data
    ├── model/                     # Reservation, Train
    ├── dao/                       # UserDAO, TrainDAO, ReservationDAO
    ├── util/                      # ValidationUtil, PasswordUtil
    └── gui/                       # LoginFrame, MainMenuFrame,
                                    # ReservationFrame, CancellationFrame
```

## How to Build and Run

You'll need a JDK (17+) and Maven installed locally — the build downloads
the SQLite driver from Maven Central, so run it somewhere with internet
access.

```bash
cd reservation-system
mvn clean package
java -jar target/reservation-system.jar
```

`mvn clean package` produces a single runnable "fat" jar
(`target/reservation-system.jar`) with the SQLite driver bundled in, so
the `java -jar` command above is all you need afterwards.

The first time you run it, a `reservation.db` SQLite file is created
automatically in the working directory, along with:
- Two demo login accounts: **admin / admin123** and **clerk1 / pass123**
- Five demo trains you can book against:

| Train Number | Train Name          |
|--------------|----------------------|
| 12345        | Rajdhani Express     |
| 54321        | Shatabdi Express     |
| 11007        | Deccan Queen         |
| 16031        | Chennai Express      |
| 12621        | Tamil Nadu Express   |

## Using the App
1. **Log in** with one of the demo accounts above.
2. Click **New Reservation**, fill in the form (type one of the demo
   train numbers and tab to the next field to see the train name
   auto-fill), then click **Book Reservation**. Note the PNR shown in
   the confirmation dialog.
3. From the main menu, click **Cancel Reservation (by PNR)**, paste in
   the PNR, click **Fetch Booking** to review the details, then click
   **Cancel Reservation** and confirm to remove it.

## Extending This Project
- Swap SQLite for MySQL by changing the JDBC URL/driver in
  `DatabaseManager` and adding the MySQL Connector/J dependency to
  `pom.xml` — the rest of the code (all written against plain JDBC)
  does not need to change.
- Add a user-registration screen by adding an `insert` method to
  `UserDAO` and a small `RegisterFrame`.
- Add seat availability tracking with a `seats_available` column on the
  `trains` table, decrementing it in `ReservationDAO.insertReservation`.

## Learning Resources
If you want to understand the patterns used here in more depth:
- YouTube: "Java Swing JDBC login form tutorial" — for GUI + DB connection patterns
- YouTube: "SQLite Java JDBC Maven setup" — for SQLite/Maven configuration
- Official Java docs: `JFrame`, `JTextField`, `JComboBox`, `PreparedStatement`

