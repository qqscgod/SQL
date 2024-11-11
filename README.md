CREATE DATABASE log_db;

USE log_db;

CREATE TABLE log_entries (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    status ENUM('in', 'out') NOT NULL,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
CREATE DATABASE door_access;

USE door_access;

CREATE TABLE access_logs (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    action ENUM('in', 'out') NOT NULL,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE
);

<?php
$servername = "localhost"; // Your server name
$username = "root";        // Your database username
$password = "";            // Your database password
$dbname = "door_access";   // Your database name

// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);

// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
?>
<?php
include('db.php'); // Include the database connection

// Check if the form is submitted (assuming you pass a user ID and action)
if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    $user_id = $_POST['user_id'];
    $action = $_POST['action']; // 'in' or 'out'

    // Validate the action (in or out)
    if ($action !== 'in' && $action !== 'out') {
        die('Invalid action');
    }

    // Prepare the SQL query to insert the log entry
    $sql = "INSERT INTO access_logs (user_id, action) VALUES ('$user_id', '$action')";

    if ($conn->query($sql) === TRUE) {
        echo "Log entry recorded successfully!";
    } else {
        echo "Error: " . $sql . "<br>" . $conn->error;
    }

    // Close the database connection
    $conn->close();
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Scan Door Access</title>
</head>
    
<body>
    <h1>Scan Door Access</h1>

    <!-- Form for scanning (user ID and action 'in' or 'out') -->
    <form method="POST" action="scan.php">
        <label for="user_id">User ID:</label>
        <input type="number" id="user_id" name="user_id" required><br><br>

        <label for="action">Action:</label>
        <select name="action" id="action" required>
            <option value="in">In</option>
            <option value="out">Out</option>
        </select><br><br>

        <input type="submit" value="Log Access">
    </form>

    <h2>Recent Logs</h2>
    <table border="1">
        <tr>
            <th>User ID</th>
            <th>Action</th>
            <th>Timestamp</th>
        </tr>

        <?php
        // Fetch and display recent access logs
        $result = $conn->query("SELECT * FROM access_logs ORDER BY timestamp DESC LIMIT 10");

        while ($row = $result->fetch_assoc()) {
            echo "<tr>
                    <td>" . $row['user_id'] . "</td>
                    <td>" . $row['action'] . "</td>
                    <td>" . $row['timestamp'] . "</td>
                  </tr>";
        }

        $conn->close();
        ?>
    </table>
</body>
</html>
