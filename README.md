# 🚂 Railway Reservation System with Real-Time Seat Map

> A complete, single-file console-based Railway Reservation System written in **C**, featuring a real-time visual seat map, dynamic booking, Tatkal reservations, cancellation with refund policy, persistent file storage, and an admin dashboard.

[![Language](https://img.shields.io/badge/Language-C-blue.svg)](https://en.wikipedia.org/wiki/C_(programming_language))
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-Linux%20%7C%20Windows%20%7C%20macOS-lightgrey.svg)]()
[![Lines](https://img.shields.io/badge/Code-900%2B%20lines-orange.svg)]()

---

## 📋 Table of Contents

- [Features](#-features)
- [Project Structure](#-project-structure)
- [Data Structures](#-data-structures)
- [Modules](#-modules)
- [Real-Time Seat Map](#-real-time-seat-map)
- [Installation & Compilation](#-installation--compilation)
- [Usage](#-usage)
- [Menu System](#-menu-system)
- [File Storage](#-file-storage)
- [Test Cases](#-test-cases)
- [Key Algorithms](#-key-algorithms)
- [Author](#-author)

---

## ✨ Features

| Feature | Description |
|---|---|
| 🗺️ Real-Time Seat Map | 10×8 grid per coach with colour-coded status (Available / Booked / Waiting) |
| 🎫 Booking System | Regular, Tatkal (1.5×), and Premium bookings |
| ❌ Cancellation + Refund | Tiered refund policy (80% / 50% / 20% / 0%) based on hours before travel |
| 🧾 Receipt Generation | Detailed booking receipt with Transaction ID |
| 💰 GST Billing | 12% GST + Tatkal surcharge auto-calculated |
| 🗃️ File Persistence | Binary `.dat` files for Trains, Bookings, Passengers; text log |
| 📊 Admin Dashboard | Live stats: revenue, occupancy, cancellation count |
| 🔍 Search | Search trains by number or name; search bookings by ID |
| ✅ Input Validation | Phone (10 digits), Age (5–120), Seat (0–79), all text fields |
| 📝 Activity Log | Every action appended to `railway.log` with timestamp |
| 🌱 Sample Data | 3 trains seeded on first run (Rajdhani, Shatabdi, Mail Express) |

---

## 📁 Project Structure

```
railway-reservation-map-c/
├── railway.c          ← Complete single-file implementation (900+ lines)
├── trains.dat         ← Binary: train records (auto-created)
├── bookings.dat       ← Binary: booking records (auto-created)
├── passengers.dat     ← Binary: passenger records (auto-created)
├── railway.log        ← Text: activity log (auto-created)
├── Makefile           ← Build automation
└── README.md          ← This file
```

---

## 🧱 Data Structures

### `Train` (Linked List Node)
```c
typedef struct Train {
    int    trainNumber;
    char   trainName[50];
    char   source[30];
    char   destination[30];
    int    sourceTime;        // HHMM format
    int    destinationTime;
    int    duration;          // hours
    int    totalCoaches;
    int    seatsPerCoach;     // always 80 (10×8)
    int    totalSeats;
    int    availableSeats;
    float  ticketPrice;
    float  tatkalPrice;       // 1.5× regular
    char   trainType[20];     // AC | Sleeper | General
    char   status[20];        // Active | Delayed | Cancelled
    TrainSeats seats;         // embedded seat map
    struct Train *next;
} Train;
```

### `Coach` (Seat Map)
```c
typedef struct Coach {
    int  coachNumber;
    char coachType[10];
    int  totalSeats;          // 80
    int  availableSeats;
    char seatStatus[80];      // 'A'=Available 'B'=Booked 'W'=Waiting
} Coach;
```

### `Booking` (Linked List Node)
```c
typedef struct Booking {
    int    bookingID;
    int    trainNumber;
    char   passengerName[50];
    char   phone[15];
    char   email[50];
    int    age;
    char   gender[10];
    int    coachNumber;
    int    seatNumbers[10];
    int    seatCount;
    float  ticketPrice;
    float  tatkalCharge;
    float  totalAmount;
    char   bookingType[20];   // Regular | Tatkal | Premium
    char   status[20];        // Confirmed | Waiting | Cancelled
    long   transactionID;
    struct Booking *next;
} Booking;
```

### `Passenger` (Linked List Node)
```c
typedef struct Passenger {
    int  passengerID;
    int  bookingID;
    int  trainNumber;
    char name[50];
    int  age;
    char gender[10];
    char phone[15];
    int  coachNumber;
    int  seatNumber;
    char status[20];
    struct Passenger *next;
} Passenger;
```

---

## 🧩 Modules

| Module | Key Functions |
|---|---|
| **Train** | `addTrain` `displayAllTrains` `searchTrainByNumber` `updateTrain` `deleteTrain` `trainsBetweenStations` `trainReport` |
| **Seat** | `initTrainSeats` `displaySeatMap` `selectSeats` `updateSeatStatus` `checkSeatAvailability` `seatUtilization` |
| **Booking** | `bookTicket` `displayBookings` `searchBookingByID` `generateReceipt` `bookingReport` |
| **Cancellation** | `cancelBooking` `calculateRefund` `viewCancellationHistory` |
| **Passenger** | `passengerListByTrain` `displayAllPassengers` `passengerReport` |
| **Billing** | `calculateBill` `processPayment` |
| **Admin** | `adminDashboard` |
| **File I/O** | `saveTrains` `loadTrains` `saveBookings` `loadBookings` `savePassengers` `loadPassengers` |

---

## 🗺️ Real-Time Seat Map

```
============================================
  COACH 0  [AC]   Available: 78 / 80

       C0   C1   C2   C3   C4   C5   C6   C7
       --------------------------------
  R0  |  0    1    2    3    4    5    6    7 |
  R1  |  8    9   10   11  [XX]  13   14   15|
  R2  | 16   17  [XX]  19   20   21   22   23|
  R3  | 24   25   26   27   28   29   30   31|
  ...
  R9  | 72   73   74   75   76   77   78   79|
       --------------------------------

  [##] = Available  [XX] = Booked  [WW] = Waiting List
============================================
```

Seat index formula: `row × 8 + col` (0-indexed, 0–79)

---

## ⚙️ Installation & Compilation

### Prerequisites
- GCC (GNU C Compiler) or any C99-compatible compiler
- Linux / macOS / Windows (WSL recommended on Windows)

### Compile

```bash
# Clone the repo
git clone https://github.com/snehalathaArakkonam/railway-reservation-map-c.git
cd railway-reservation-map-c

# Compile
gcc -Wall -Wextra -o railway railway.c

# Run
./railway
```

### Using the Makefile

```bash
make        # builds ./railway
make run    # builds and runs
make clean  # removes binary and .dat files
```

### Makefile contents

```makefile
CC      = gcc
CFLAGS  = -Wall -Wextra -std=c99
TARGET  = railway
SRC     = railway.c

all: $(TARGET)

$(TARGET): $(SRC)
	$(CC) $(CFLAGS) -o $(TARGET) $(SRC)

run: $(TARGET)
	./$(TARGET)

clean:
	rm -f $(TARGET) trains.dat bookings.dat passengers.dat railway.log
```

---

## 🖥️ Usage

### First Run
On the first run, three sample trains are automatically seeded:

| Number | Name | Route | Price |
|---|---|---|---|
| 12345 | Rajdhani Express | Delhi → Mumbai | ₹500 |
| 11223 | Shatabdi Express | Chennai → Chittoor | ₹300 |
| 10101 | Mail Express | Kolkata → Delhi | ₹400 |

### Typical Workflow

```
1. Main Menu → 1 (Train Management) → 2 (Display All Trains)
2. Main Menu → 2 (Book Ticket) → 1 (Regular)
   Enter Train Number → Choose Coach → View Seat Map → Select Seats
   Enter Passenger Details → Confirm Bill → Pay → Receipt printed
3. Main Menu → 3 (Cancel Ticket) → Enter Booking ID → Enter hours before travel
   Refund calculated and displayed
4. Main Menu → 7 (Admin Dashboard) → View live statistics
```

---

## 📌 Menu System

```
============================================
  RAILWAY RESERVATION SYSTEM
  Real-Time Seat Map  |  v1.0.0
============================================
  1.  Train Management
  2.  Book Ticket
  3.  Cancel Ticket
  4.  View Bookings
  5.  Passenger List
  6.  Seat Map Viewer
  7.  Admin Dashboard
  8.  Passenger Report
  9.  Exit
============================================
```

---

## 💾 File Storage

| File | Type | Contents |
|---|---|---|
| `trains.dat` | Binary | All Train structs (with embedded seat maps) |
| `bookings.dat` | Binary | All Booking structs |
| `passengers.dat` | Binary | All Passenger structs |
| `railway.log` | Text | Timestamped activity log |

Files are created automatically on first save. All data is loaded on startup and saved on every booking, cancellation, and on exit.

---

## 🧪 Test Cases

### Add Train
```
Train Number : 12345
Name         : Rajdhani Express
Source       : Delhi
Destination  : Mumbai
Departure    : 0600
Arrival      : 2000
Duration     : 14 hours
Coaches      : 10
Price        : 500
Type         : AC
→ Train added successfully! Total Trains: 1
```

### Book Ticket
```
Train  : 12345 (Rajdhani Express)
Coach  : 0 (AC)
Seats  : 5, 6, 10
Name   : Rahul Kumar
Phone  : 9876543210
Age    : 35
Gender : Male
→ Base: ₹1500  GST: ₹180  TOTAL: ₹1680
→ Booking ID: 1  |  Status: Confirmed
→ Seat map updated: 5=XX, 6=XX, 10=XX
```

### Tatkal Booking
```
Train  : 12345
Seats  : 2 (seat 20, 21)
Type   : Tatkal
Unit Price : ₹750 (1.5× ₹500)
Base   : ₹1500  Tatkal: ₹750  GST: ₹270  TOTAL: ₹2520
→ Tatkal Booking Confirmed! ID: 2
```

### Cancel Booking
```
Booking ID        : 1
Hours before travel : 36  (24–48 hrs)
Policy            : 50% refund
Original Amount   : ₹1680
Refund            : ₹840
Cancellation Fee  : ₹840
→ Booking Cancelled! Seats 5, 6, 10 released.
```

### Admin Dashboard
```
Total Trains          : 3
Total Bookings        : 2
Confirmed Bookings    : 1
Cancelled Bookings    : 1
Total Passengers      : 5
Total Revenue         : ₹2520.00
Total Seats           : 2400
Available Seats       : 2393
Seat Occupancy        : 0.3%
```

---

## 🔑 Key Algorithms

### Refund Policy
```
Hours before travel  | Refund
> 48 hrs             | 80%
24 – 48 hrs          | 50%
2  – 24 hrs          | 20%
< 2 hrs              | 0%
```

### Bill Calculation
```
Base Amount   = unit_price × num_seats
Tatkal Charge = base × 0.50  (only for Tatkal)
GST           = (base + tatkal) × 0.12
TOTAL         = base + tatkal + GST
```

### Seat Index Formula
```
seat_index = row × 8 + col    (range: 0–79)
row        = seat_index / 8
col        = seat_index % 8
```

### Linked List Search (O(n))
```c
Train *cur = list->head;
while (cur) {
    if (cur->trainNumber == target) return cur;
    cur = cur->next;
}
return NULL;
```

---

## 📚 Concepts Demonstrated

- **2D Arrays** – `seatStatus[80]` mapped as a 10×8 grid
- **Linked Lists** – Train, Booking, Passenger all use singly-linked lists
- **File Handling** – Binary `fread`/`fwrite` with auto-create on first run
- **Dynamic Memory** – `malloc`/`calloc`/`free` for all list nodes
- **Structures & Typedef** – Nested structs (TrainSeats embeds Coach array)
- **Modular Design** – Logical grouping of functions by domain
- **Input Validation** – Phone, age, seat number, text fields
- **ANSI Colours** – Green/Red/Yellow for seat status (degrades gracefully on Windows)

---

## 👩‍💻 Author

**snehalathaArakkonam**  
GitHub: [https://github.com/snehalathaArakkonam/railway-reservation-map-c](https://github.com/snehalathaArakkonam/railway-reservation-map-c)

---

## 📄 License

This project is licensed under the MIT License.
