---
title: Home
---

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Welcome Page</title>
    
</head>
<body>
    <h1>Welcome to Our Website!</h1>
    <p>This is a simple welcome message. Feel free to explore our content.</p>
    <div id="counter">Counter: <span id="count">0</span></div>
    <script>
        // JavaScript code to update the counter
        document.addEventListener('DOMContentLoaded', function () {
            // Fetch the current user's ID from the API
            fetch('/api/person/currentUserId')
                .then(response => response.json())
                .then(userId => {
                    // Display the current user's ID (for demonstration purposes)
                    console.log('Current User ID:', userId);
                    // Fetch the "counter" variable for the current user from the API
                    fetch(`/api/person/${userId}`)
                        .then(response => response.json())
                        .then(person => {
                            // Display the "counter" variable on the page
                            const counterElement = document.getElementById('count');
                            counterElement.textContent = person.counter;
                        })
                        .catch(error => console.error('Error fetching person:', error));
                })
                .catch(error => console.error('Error fetching currentUserId:', error));
        })
    </script>
</body>
</html>
