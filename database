CREATE DATABASE CabBooking;
USE CabBooking;

-- =========================
-- 1. Tables (DDL)
-- =========================

-- Base User table (common attributes for customers and drivers)
CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    user_name VARCHAR(100) NOT NULL,
    uphone VARCHAR(15) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    -- keep login and other behaviors at application-level; not in DB
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB;

-- Customer table (extends users)
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,                -- same value as users.user_id
    current_location_id INT NULL,
    drop_location_id INT NULL,
    FOREIGN KEY (customer_id) REFERENCES users(user_id) ON DELETE CASCADE,
    FOREIGN KEY (current_location_id) REFERENCES locations(location_id) ON DELETE SET NULL,
    FOREIGN KEY (drop_location_id) REFERENCES locations(location_id) ON DELETE SET NULL
) ENGINE=InnoDB;

-- Driver table (extends users)
CREATE TABLE drivers (
    driver_id INT PRIMARY KEY,                  -- same value as users.user_id
    car_number VARCHAR(30) NOT NULL UNIQUE,
    -- other driver-specific attributes could go here
    FOREIGN KEY (driver_id) REFERENCES users(user_id) ON DELETE CASCADE
) ENGINE=InnoDB;

-- Location table (extracted from Location class in diagram)
CREATE TABLE locations (
    location_id INT AUTO_INCREMENT PRIMARY KEY,
    pincode INT NOT NULL,
    area VARCHAR(100),
    apartment VARCHAR(100),
    locality VARCHAR(100)
) ENGINE=InnoDB;

-- Booking (Ride) table
CREATE TABLE bookings (
    booking_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT NOT NULL,
    driver_id INT DEFAULT NULL,
    pickup_location_id INT NOT NULL,
    drop_location_id INT NOT NULL,
    date_of_booking DATE NOT NULL,
    time_of_booking TIME NOT NULL,
    eta TIME DEFAULT NULL,
    booking_amount DECIMAL(10,2) DEFAULT 0.0,
    payment_method ENUM('Cash','Card','UPI','Wallet') DEFAULT 'Cash',
    ride_status ENUM('Booked','Assigned','Ongoing','Completed','Cancelled') DEFAULT 'Booked',
    is_shared BOOLEAN DEFAULT FALSE,
    CONSTRAINT fk_booking_customer FOREIGN KEY (customer_id) REFERENCES users(user_id) ON DELETE CASCADE,
    CONSTRAINT fk_booking_driver FOREIGN KEY (driver_id) REFERENCES users(user_id) ON DELETE SET NULL,
    CONSTRAINT fk_booking_pickup FOREIGN KEY (pickup_location_id) REFERENCES locations(location_id) ON DELETE RESTRICT,
    CONSTRAINT fk_booking_drop FOREIGN KEY (drop_location_id) REFERENCES locations(location_id) ON DELETE RESTRICT
) ENGINE=InnoDB;

-- Payment table
CREATE TABLE payments (
    payment_id INT AUTO_INCREMENT PRIMARY KEY,
    booking_id INT UNIQUE,  -- one-to-one with booking (as per diagram: booking has payment)
    amount DECIMAL(10,2) NOT NULL,
    payment_status ENUM('Pending','Completed','Failed') DEFAULT 'Pending',
    payment_method ENUM('Cash','Card','UPI','Wallet') DEFAULT 'Cash',
    processed_at TIMESTAMP NULL,
    CONSTRAINT fk_payment_booking FOREIGN KEY (booking_id) REFERENCES bookings(booking_id) ON DELETE CASCADE
) ENGINE=InnoDB;

-- Feedback table
CREATE TABLE feedback (
    feedback_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT NOT NULL,
    booking_id INT NOT NULL,
    driver_id INT NOT NULL,
    feedback_message VARCHAR(500),
    rating TINYINT UNSIGNED CHECK (rating BETWEEN 1 AND 5),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_feedback_customer FOREIGN KEY (customer_id) REFERENCES users(user_id) ON DELETE CASCADE,
    CONSTRAINT fk_feedback_booking FOREIGN KEY (booking_id) REFERENCES bookings(booking_id) ON DELETE CASCADE,
    CONSTRAINT fk_feedback_driver FOREIGN KEY (driver_id) REFERENCES users(user_id) ON DELETE CASCADE
) ENGINE=InnoDB;

-- =========================
-- 2. Insert sample data (5 records each where applicable)
-- =========================

-- 2.1 Locations (5)
INSERT INTO locations (pincode, area, apartment, locality) VALUES
(110001, 'Connaught Place', 'Apt 101', 'Central Delhi'),
(110003, 'India Gate Area', 'Apt 21', 'Central Delhi'),
(201301, 'Noida Sector 18', 'Tower B', 'Noida'),
(122002, 'Gurgaon Cyber Hub', 'Block A', 'Gurgaon'),
(110037, 'Saket', 'Apt 45', 'South Delhi');

-- 2.2 Users: We'll create 10 users — 5 customers and 5 drivers
INSERT INTO users (user_name, uphone, email) VALUES
('Rahul Sharma', '9876543210', 'rahul@example.com'),
('Ananya Singh', '9123456780', 'ananya@example.com'),
('Amit Verma', '9988776655', 'amit@example.com'),
('Sneha Gupta', '9345678123', 'sneha@example.com'),
('Manish Verma', '9001122334', 'manish@example.com'),
-- drivers (users entries)
('Rakesh Yadav', '8800112233', 'rakesh@example.com'),
('Sunil Kumar', '8700554433', 'sunil@example.com'),
('Vikram Singh', '8600778899', 'vikram@example.com'),
('Arjun Mehta', '8500991122', 'arjun@example.com'),
('Karan Patel', '8400887766', 'karan@example.com');

-- 2.3 Customers (map to first 5 user_ids)
-- Note: We'll explicitly use user_id values by selecting them (but here we assume AUTO_INCREMENT order)
-- For robust scripts, you'd SELECT the ids; for simplicity we assume they are 1..10
INSERT INTO customers (customer_id, current_location_id, drop_location_id) VALUES
(1, 1, 2),
(2, 3, 4),
(3, 1, 5),
(4, 2, 5),
(5, 5, 1);

-- 2.4 Drivers (map to user_ids 6..10)
INSERT INTO drivers (driver_id, car_number) VALUES
(6, 'MH12AB1234'),
(7, 'DL08CD5678'),
(8, 'UP14EF9876'),
(9, 'RJ20GH3344'),
(10,'GJ05IJ7654');

-- 2.5 Bookings (5 sample rides)
INSERT INTO bookings (
    customer_id, driver_id, pickup_location_id, drop_location_id,
    date_of_booking, time_of_booking, eta, booking_amount, payment_method, ride_status, is_shared
) VALUES
-- Completed ride
(1, 6, 1, 2, '2025-09-01', '09:30:00', '00:30:00', 250.00, 'UPI', 'Completed', FALSE),
-- Ongoing ride
(2, 7, 3, 4, '2025-09-02', '13:15:00', '00:45:00', 600.00, 'Card', 'Ongoing', TRUE),
-- Booked (driver assigned but not started)
(3, 8, 1, 5, '2025-09-03', '18:00:00', '00:25:00', 450.00, 'Cash', 'Booked', FALSE),
-- Completed
(4, 9, 2, 5, '2025-08-30', '20:10:00', '00:35:00', 300.00, 'UPI', 'Completed', FALSE),
-- Cancelled
(5, NULL, 5, 1, '2025-09-04', '11:00:00', NULL, 200.00, 'Card', 'Cancelled', FALSE);

-- 2.6 Payments (matching bookings) - one-to-one mapping
INSERT INTO payments (booking_id, amount, payment_status, payment_method, processed_at) VALUES
(1, 250.00, 'Completed', 'UPI', '2025-09-01 10:15:00'),
(2, 600.00, 'Pending', 'Card', NULL),
(3, 450.00, 'Pending', 'Cash', NULL),
(4, 300.00, 'Completed', 'UPI', '2025-08-30 21:00:00'),
(5, 200.00, 'Failed', 'Card', '2025-09-04 11:05:00');

-- 2.7 Feedback (5)
INSERT INTO feedback (customer_id, booking_id, driver_id, feedback_message, rating) VALUES
(1, 1, 6, 'Good ride, polite driver', 5),
(2, 2, 7, 'Driver was on time', 4),
(3, 3, 8, 'Driver was late', 2),
(4, 4, 9, 'Comfortable ride', 5),
(5, 5, NULL, 'Cancelled before starting', 1);

-- Note: last feedback references booking 5 which was cancelled; driver_id might be NULL for cancelled bookings.
-- To allow that, we inserted driver_id NULL in bookings for booking_id=5; feedback then must have driver_id not NULL or we handle differently.
-- For simplicity in this demo, we will update booking 5 to have a placeholder driver (10) and update feedback accordingly.

UPDATE bookings SET driver_id = 10 WHERE booking_id = 5;
UPDATE feedback SET driver_id = 10 WHERE feedback_id = 5;

-- =========================
-- 3. Example Queries
-- =========================

-- 3.1 JOIN: List all bookings with customer name, driver name, pickup & drop area
SELECT
  b.booking_id,
  u_c.user_name AS customer_name,
  u_d.user_name AS driver_name,
  lp.area AS pickup_area,
  ld.area AS drop_area,
  b.ride_status,
  b.booking_amount
FROM bookings b
LEFT JOIN users u_c ON b.customer_id = u_c.user_id
LEFT JOIN users u_d ON b.driver_id = u_d.user_id
LEFT JOIN locations lp ON b.pickup_location_id = lp.location_id
LEFT JOIN locations ld ON b.drop_location_id = ld.location_id
ORDER BY b.booking_id;

-- 3.2 INNER JOIN with payments: Completed payments and rides
SELECT b.booking_id, u_c.user_name AS customer, p.amount, p.payment_status
FROM bookings b
JOIN payments p ON b.booking_id = p.booking_id
JOIN users u_c ON b.customer_id = u_c.user_id
WHERE p.payment_status = 'Completed';

-- 3.3 Subquery: Customers who have at least one ride with amount > 300
SELECT DISTINCT u.user_name
FROM users u
WHERE u.user_id IN (
    SELECT b.customer_id FROM bookings b
    JOIN payments p ON b.booking_id = p.booking_id
    WHERE p.amount > 300
);

-- 3.4 Aggregate: Total earnings (completed payments) by driver
SELECT u.user_name AS driver_name, SUM(p.amount) AS total_earnings
FROM users u
JOIN bookings b ON u.user_id = b.driver_id
JOIN payments p ON b.booking_id = p.booking_id
WHERE p.payment_status = 'Completed'
GROUP BY u.user_id
ORDER BY total_earnings DESC;

-- 3.5 Aggregates & HAVING: Drivers who earned more than 200
SELECT u.user_name AS driver_name, SUM(p.amount) AS total_earnings
FROM users u
JOIN bookings b ON u.user_id = b.driver_id
JOIN payments p ON b.booking_id = p.booking_id
WHERE p.payment_status = 'Completed'
GROUP BY u.user_id
HAVING SUM(p.amount) > 200;

-- 3.6 Nested subquery: Bookings where customer has rated driver 5 previously
SELECT b.booking_id, u.user_name AS customer
FROM bookings b
WHERE b.customer_id IN (
  SELECT f.customer_id FROM feedback f WHERE f.rating = 5
);

-- 3.7 Example of a correlated subquery: latest booking date for each customer
SELECT u.user_name, (
  SELECT MAX(b.date_of_booking) FROM bookings b WHERE b.customer_id = u.user_id
) AS last_booking_date
FROM users u
WHERE u.user_id IN (SELECT customer_id FROM customers);

-- =========================
-- 4. DML Examples (UPDATE / DELETE / INSERT)
-- =========================

-- 4.1 Update: mark payment 2 as completed after processing
UPDATE payments SET payment_status = 'Completed', processed_at = NOW() WHERE payment_id = 2;

-- 4.2 Update: set booking 2 status to Ongoing
UPDATE bookings SET ride_status = 'Ongoing' WHERE booking_id = 2;

-- 4.3 Insert: add a new booking (example)
INSERT INTO bookings (
    customer_id, driver_id, pickup_location_id, drop_location_id, date_of_booking, time_of_booking, eta, booking_amount, payment_method, ride_status, is_shared
) VALUES (1, 6, 3, 4, CURDATE(), '15:30:00', '00:40:00', 350.00, 'Card', 'Booked', FALSE);

-- 4.4 Delete: remove cancelled rides older than a certain date
DELETE FROM bookings WHERE ride_status = 'Cancelled' AND date_of_booking < '2025-01-01';

-- 4.5 Delete: delete a feedback (example)
DELETE FROM feedback WHERE feedback_id = 3;

-- =========================
-- 5. Useful SELECTS for checking data
-- =========================
SELECT COUNT(*) AS total_users FROM users;
SELECT COUNT(*) AS total_customers FROM customers;
SELECT COUNT(*) AS total_drivers FROM drivers;
SELECT COUNT(*) AS total_bookings FROM bookings;
SELECT COUNT(*) AS total_payments FROM payments;
SELECT COUNT(*) AS total_feedback FROM feedback;
