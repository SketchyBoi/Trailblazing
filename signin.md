---
title: SignIn
---
<body>
    <section>
        <div class="signin-box">
            <div class="signin-value">
                <form id="signinForm" autocomplete="off">
                    <h2>Sign In</h2>
                    <div class="inputbox" id="emailField" style="display: block;">
                        <ion-icon name="mail-outline"></ion-icon>
                        <input type="email" id="emailInput" name="email" required>
                        <label for="emailInput">Email</label>
                    </div>
                    <div class="inputbox" id="passwordField" style="display: none;">
                        <ion-icon name="lock-closed-outline"></ion-icon>
                        <input type="password" id="passwordInput" name="password" required>
                        <label for="passwordInput">Password</label>
                    </div>
                    <button onclick="showNextField()">Log In</button>
                    <div class="register">
                        <p>Don't have an account? <a href="{{ site.baseurl }}/pages/signup">Sign Up</a></p>
                    </div>
                    <div class="goback">
                        <p>Want to go back to the homepage? <a href="{{ site.baseurl }}/">Home</a></p>
                    </div>
                </form>
            </div>
        </div>
    </section>
    <script>
        const login_url = "http://localhost:8084/authenticate";
        // prepare URL
        //var url = "https://spring.nighthawkcodingsociety.com/api/person/";
        // localhost testing
        const read_url = "http://localhost:8084/api/usr/";
        const post_url = "http://localhost:8084/api/usr/post";
        const put_url = "http://localhost:8084/api/usr/update";
        let currentField = "emailField";
        function showNextField() {
            const emailField = document.getElementById("emailField");
            const passwordField = document.getElementById("passwordField");
            if (currentField === "emailField") {
                currentField = "passwordField"
                passwordField.style.display = "block";
                document.querySelector("button").textContent = "Log In";
            } else if (currentField === "passwordField") {
                login_user();
            }
        const signinForm = document.getElementById("signinForm");
        signinForm.addEventListener("submit", function (e) {
        e.preventDefault();
        login_user(e);
            })
        }
        function login_user(event) {
            if (event) {
            event.preventDefault();
            // Set body to include login data
            const emailInput = document.getElementById("emailInput");
            const passwordInput = document.getElementById("passwordInput");
            if (currentField === "emailField" && emailInput.value.trim() === "") {
                alert("Please enter your email");
                return;
            } else if (currentField === "passwordField" && passwordInput.value.trim() === "") {
                alert("Please enter your password");
                return;
            }
            // Set Headers to support cross-origin
            const authBody = {
                email: emailInput.value,
                password: passwordInput.value
            };
            const authOptions = {
                method: 'POST',
                mode: 'cors',
                cache: 'no-cache',
                // credentials: 'include',
                body: JSON.stringify(authBody),
                headers: {
                    "Content-Type": "application/json",
                },
            };
            // Fetch JWT 
            fetch(login_url, authOptions)
            .then(response => {
                if (response.status === 401) {
                    alert("Incorrect email or password :( Please try again!");
                    console.log('Authentication failed');
                } else if (response.ok) {
                    // Successfully logged in, now set the email in a cookie
                    setCookie("email", emailInput.value, 1); // Set the email cookie for an hour
                    console.log('Successful fetch');
                    read_pull();
                }
            })
            .catch(error => {
                console.error("Network error:", error);
            });
    }
}
        // Function to set a cookie
        function setCookie(name, value) {
            document.cookie = name + "=" + value + ";" + expires + ";path=/";
        }
        // read
        function read_pull() {
            // Set options for cross-origin header request
            const options = {
                method: 'GET',
                mode: 'cors',
                cache: 'default',
                // credentials: 'include',
                headers: {
                    'Content-Type': 'application/json',
                },
            };
            // Fetch the API
            fetch(read_url, options)
                .then(response => {
                    if (response.status === 401) {
                        alert("Bad password/username input");
                        console.error("Bad password/username input");
                        return;
                    } else if (response.status === 200) {
                        console.log("Success loser!");
                        // Only show the alert if the login was successful
                        alert("Successful Signin!");
                        // Redirect after alert
                        window.location.href = "https://csa-tri-1.github.io/DADDiJkstra-frontend/pages/app";
                        return;
                    } else {
                        const errorMsg = 'Database response error: ' + response.status;
                        alert("Uh oh, something went wrong :(");
                        console.error(errorMsg);
                        return;
                    }
            })
            .catch(error => {
                // Handle network errors or exceptions here
                console.error("Network error:", error);
            });
        }
        function getEmailFromCookie() {
            const name = "email=";
            const decodedCookie = decodeURIComponent(document.cookie);
            const cookieArray = decodedCookie.split(';');
            for (let i = 0; i < cookieArray.length; i++) {
                let cookie = cookieArray[i];
                while (cookie.charAt(0) === ' ') {
                    cookie = cookie.substring(1);
                }
                if (cookie.indexOf(name) === 0) {
                    return cookie.substring(name.length, cookie.length);
                }
            }
            return "";
        }
        // Example of how to use the getEmailFromCookie function
        const storedEmail = getEmailFromCookie();
        if (storedEmail) {
            console.log("Email from cookie:", storedEmail);
        } else {
            console.log("Email cookie not found");
        }
    </script>
    <script type="module" src="https://unpkg.com/ionicons@7.1.0/dist/ionicons/ionicons.esm.js"></script>
    <script nomodule src="https://unpkg.com/ionicons@7.1.0/dist/ionicons/ionicons.js"></script>
</body>

<!-- Styles -->

<style>
*{
    margin: 0;
    padding: 0;
}
</style>