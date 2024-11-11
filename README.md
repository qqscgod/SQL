CREATE DATABASE log_db;

USE log_db;

CREATE TABLE log_entries (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    status ENUM('in', 'out') NOT NULL,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

