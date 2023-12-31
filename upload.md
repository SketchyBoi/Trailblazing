<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Welcome Page</title>
    <!-- Connect Metamask -->
    <script src="https://cdn.jsdelivr.net/npm/@metamask/detect-provider"></script>
    <script src="https://code.jquery.com/jquery-3.6.4.min.js"></script>
    <script src="https://cdn.ethers.io/lib/ethers-5.0.umd.min.js"></script>
</head>

<body>
     <section>
        <div class="signin-box">
            <h2>Upload your GPS Data</h2>
            <!-- <p id="dayOfWeek" class="day-of-week">Today is </p>
            <div>
                <button id="connectButton" onclick="connect()">Connect to MetaMask</button>
                <button onclick="issueTokens()">Issue Tokens</button>
            </div> -->
            <div class="inputbox">
                <label for="fileInput">Upload Text File:</label>
                <input type="file" id="fileInput" accept=".txt" />
                <button onclick="uploadFile()">Upload File</button>
            </div>
        </div>
    </section>
    <script>
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
        window.onload = function() {
            if (!getEmailFromCookie()) {
                window.location.href = '{{site.baseurl}}/index';
            }
        }
        const storedEmail = getEmailFromCookie();
        function editCheckDay(email, day, value) {
            const apiUrl = `http://localhost:8084/api/person/editCheckDay?email=${encodeURIComponent(email)}&day=${encodeURIComponent(day)}&value=${encodeURIComponent(value.toString())}`;
            fetch(apiUrl, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
            })
            .then(response => {
                if (!response.ok) {
                    throw new Error(`HTTP error! Status: ${response.status}`);
                }
                return response.text();
            })
            .then(data => {
                console.log(data); // Log the response from the backend
            })
            .catch(error => {
                console.error('Error:', error);
            });
        }
    function uploadFile() {
        const fileInput = document.getElementById('fileInput');
        const file = fileInput.files[0];
        if (file) {
            const reader = new FileReader();
            reader.onload = function (e) {
                const fileContent = e.target.result;
                const daysOfWeek = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
                const containsDay = daysOfWeek.find(day => fileContent.includes(day));
                if (containsDay) {
                    console.log(`File contains data for ${containsDay}`);
                    const lastLine = fileContent.trim().split('\n').pop();
                    const elapsedTimeInMinutes = parseFloat(lastLine.split(', ')[5]);
                    const url = `http://localhost:8084/api/person/getStats?email=${encodeURIComponent(storedEmail)}`;
                    // Make the fetch request
                    fetch(url)
                        .then(response => response.json())
                        .then(data => {
                            const dayKey = containsDay.toLowerCase();
                            const isDayTrue = data[dayKey] === 'true';
                            const dayMinutes = parseFloat(data[`${dayKey}Minutes`]);
                            if (isDayTrue && elapsedTimeInMinutes >= dayMinutes) {
                                console.log(`The time elapsed on ${containsDay} is greater than or equal to the associated day's minutes`);
                                editCheckDay(storedEmail, containsDay, true);
                                // Make a FormData object to send the file to the backend
                                const formData = new FormData();
                                formData.append('email', storedEmail);
                                formData.append('file', file);
                                // Make the fetch request to the backend endpoint
                                fetch('http://localhost:8084/api/person/calculateReward', {
                                    method: 'POST',
                                    body: formData,
                                })
                                    .then(response => {
                                        if (!response.ok) {
                                            throw new Error(`HTTP error! Status: ${response.status}`);
                                        }
                                        return response.text();
                                    })
                                    .then(data => {
                                        console.log(data); // Log the response from the backend
                                        // Handle the response as needed (e.g., display a message to the user)
                                    })
                                    .catch(error => {
                                        console.error('Error:', error);
                                    });
                            } else {
                                console.log(`The time elapsed on ${containsDay} does not meet the criteria.`);
                            }
                        })
                        .catch(error => console.error('Error fetching data from the API:', error));
                } else {
                    console.log('The uploaded file does not contain data for any day of the week');
                }
            };
            reader.readAsText(file);
        } else {
            console.log('No file selected');
        }
    }

        // let selectedAccount = 0;
        // let signer = new ethers.providers.Web3Provider(window.ethereum).getSigner();
        // // Check if MetaMask is installed
        // async function connect() {
        //     if (typeof window.ethereum !== 'undefined') {
        //     const connectButton = document.getElementById('connectButton');
        //     // Handle button click event
        //     connectButton.addEventListener('click', async () => {
        //         try {
        //             // Request account access if needed
        //             await window.ethereum.request({ method: 'eth_requestAccounts' });
        //             // Now MetaMask is connected, you can use window.ethereum to interact with the wallet
        //             // For example, you can get the selected account:
        //             const accounts = await window.ethereum.request({ method: 'eth_accounts' });
        //             selectedAccount = accounts[0];
        //             console.log('Connected to MetaMask with account:', selectedAccount);
        //             // You can now perform actions with the connected account or interact with a smart contract
        //         } catch (error) {
        //             console.error('Error connecting to MetaMask:', error);
        //         }
        //     });
        //     } else {
        //         console.error('MetaMask is not installed. Please install it to use this feature.');
        //     }
        // }
        // async function issueTokens() {
        //     const contractAddress = "0xc9cAA4263F8D662182a09a7d364BaE99BBeec719";
        //     // Connect to the deployed contract
        //     const trailblazeContract = new ethers.Contract(
        //         contractAddress,
        //         ['function mintTokens(address to, uint256 amount)'],
        //         signer
        //     );
        //     const toAddress = "0xfb3131da44E49C060fc15FfaA9d8c54412C1468A";
        //     const amount = 1000 * 1000 * 1000; // Assuming 18 decimals
        //     // Send transaction
        //     const tx = await trailblazeContract.mintTokens(toAddress, amount);
        //     // Wait for the transaction to be mined
        //     await tx.wait();
        //     console.log(`${amount} tokens sent to ${toAddress}`);
        // }
</script>
</body>

<style>
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }
    body {
        font-family: 'Arial', sans-serif;
    }
    section {
        display: flex;
        justify-content: center;
        align-items: center;
        min-height: 100vh;
        width: 100%;
        background: linear-gradient(to right, #141e30, #243b55);
    }
    .signin-box {
        position: relative;
        width: 400px;
        height: 450px;
        background: rgba(255, 255, 255, 0.1);
        border: 2px solid rgba(255, 255, 255, 0.5);
        border-radius: 20px;
        backdrop-filter: blur(15px);
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        text-align: center;
    }
    h2 {
        font-size: 2em;
        color: white;
        margin-bottom: 20px;
    }
    .inputbox {
        margin: 20px 0;
        width: 80%;
    }
    .inputbox label {
        color: white;
        font-size: 1em;
        margin-bottom: 8px;
        display: block;
    }
    .inputbox input {
        width: 100%;
        height: 40px;
        background: transparent;
        border: none;
        border-bottom: 2px solid white;
        outline: none;
        font-size: 1em;
        padding: 5px;
        color: white;
    }
    .signin-box button {
        width: 80%;
        height: 40px;
        margin-top: 20px;
        border-radius: 20px;
        background: #3498db;
        border: none;
        outline: none;
        font-size: 1em;
        font-weight: 600;
        color: white;
        cursor: pointer;
        transition: background 0.3s;
    }
    .signin-box button:hover {
        background: #2980b9;
    }
    .register, .goback {
        font-size: 0.9em;
        color: white;
        margin-top: 20px;
    }
    .register p a, .goback p a {
        text-decoration: none;
        color: #3498db;
        font-weight: 600;
    }
    .register p a:hover, .goback p a:hover {
        text-decoration: underline;
    }
    .day-of-week {
        color: white;
        font-size: 1em;
        margin-bottom: 10px;
    }
</style>
</html>
