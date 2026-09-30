# School-work-2
 <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Product Search</title>

    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 40px;
        }

        .container {
            max-width: 800px;
            margin: auto;
            background: #ffffff;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
        }

        h2 {
            text-align: center;
            margin-bottom: 25px;
        }

        form {
            display: flex;
            gap: 10px;
            margin-bottom: 25px;
        }

        label {
            display: none;
        }

        input[type="text"] {
            flex: 1;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }

        input[type="submit"] {
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            background-color: #333;
            color: white;
            cursor: pointer;
        }

        input[type="submit"]:hover {
            background-color: #555;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 15px;
        }

        th, td {
            padding: 12px;
            border: 1px solid #ddd;
            text-align: left;
        }

        th {
            background-color: #333;
            color: white;
        }

        tr:nth-child(even) {
            background-color: #f9f9f9;
        }

        .message {
            padding: 12px;
            background-color: #f8f8f8;
            border-radius: 5px;
        }
    </style>
</head>

<body>
    <div class="container">
        <h2>Product Search</h2>

        <form action="product_search.php" method="POST">
            <label for="search">Search Product:</label>
            <input
                type="text"
                id="search"
                name="search"
                placeholder="Enter product name"
                required
            >
            <input type="submit" name="Submit" value="Search">
        </form>

        <?php
        if (isset($_POST['Submit'])) {
            $search_term = trim($_POST['search']);

            // Database connection
            $db_host = "localhost";
            $db_user = "root";
            $db_pass = "";
            $db_name = "mid3";

            $conn = mysqli_connect($db_host, $db_user, $db_pass, $db_name);

            // Check database connection
            if (!$conn) {
                die("Connection failed: " . mysqli_connect_error());
            }

            // Prepare the search query
            $sql = "SELECT name, price, brewer
                    FROM products
                    WHERE name LIKE ?";

            $stmt = mysqli_prepare($conn, $sql);

            if (!$stmt) {
                die("Query preparation failed: " . mysqli_error($conn));
            }

            $search_pattern = "%" . $search_term . "%";
            mysqli_stmt_bind_param($stmt, "s", $search_pattern);
            mysqli_stmt_execute($stmt);

            $result = mysqli_stmt_get_result($stmt);

            // Display results
            if (mysqli_num_rows($result) > 0) {
                echo "<h3>Search Results for '" . htmlspecialchars($search_term) . "'</h3>";
                echo "<table>";
                echo "<tr>
                        <th>Product Name</th>
                        <th>Price (€)</th>
                        <th>Brewer</th>
                      </tr>";

                while ($row = mysqli_fetch_assoc($result)) {
                    echo "<tr>";
                    echo "<td>" . htmlspecialchars($row['name']) . "</td>";
                    echo "<td>€" . number_format($row['price'], 2) . "</td>";
                    echo "<td>" . htmlspecialchars($row['brewer']) . "</td>";
                    echo "</tr>";
                }

                echo "</table>";
            } else {
                echo "<p class='message'>No products found for '" . htmlspecialchars($search_term) . "'.</p>";
            }

            mysqli_stmt_close($stmt);
            mysqli_close($conn);
        }
        ?>
    </div>
</body>
</html>
