---
title: Home
---

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
    <h1>Welcome to Our Website!</h1>
    <p>This is a simple welcome message. Feel free to explore our content.</p>
    <div id="counter">Counter: <span id="count">0</span></div>
    <div>
        <!-- Add a button to connect to MetaMask -->
        <button onclick="connectToMetaMask()">Connect to MetaMask</button>
        <label for="fileInput">Upload Text File:</label>
        <input type="file" id="fileInput" accept=".txt">
        <button onclick="uploadFile()">Upload</button>
    </div>
    <div>
        <button onclick="issueTokens()">Issue Tokens</button>
    </div>
    <script>
        async function issueTokens() {
        try {
            // Check if MetaMask is connected
            const provider = await detectEthereumProvider();
            if (!provider) {
                console.error('MetaMask not detected. Please connect to MetaMask first.');
                alert('MetaMask not detected. Please connect to MetaMask first.');
                return;
            }
            // Request the user's address
            const accounts = await ethereum.request({ method: 'eth_requestAccounts' });
            const userAddress = accounts[0];
            // Switch MetaMask to Mumbai test network
            await ethereum.request({
                method: 'wallet_switchEthereumChain',
                params: [{ chainId: '0x13881' }], // Chain ID for Mumbai test network
            });
            // Call your test token contract to issue tokens
            const contractAddress = '0x2381208bE53E4C8688447a4Abc1A691c89d8E89f'; // Replace with your test token contract address
            const abiResponse = await fetch('./abi.json'); // Updated path to abi.json
            const contractABI = await abiResponse.json();
            const contract = new ethers.Contract(contractAddress, contractABI, ethereum.selectedAddress);
            // Set the data for the contract method call
            const data = contract.interface.encodeFunctionData('issueToken', [userAddress, tokens]); // Make sure to adjust the function and parameters
            const transactionParameters = {
                to: contractAddress,
                from: ethereum.selectedAddress,
                gas: '200000', // gas limit
                gasPrice: '1000000000', // gas price (example value, replace with your own)
                data: data,
            };
            // Send the transaction
            const transactionHash = await ethereum.request({
                method: 'eth_sendTransaction',
                params: [transactionParameters],
            });
            console.log('Transaction sent:', transactionHash);
            // You may want to wait for the transaction to be mined or handle it accordingly
        } catch (error) {
            console.error('Error issuing tokens:', error);
            alert('Error issuing tokens. Please try again.');
        }
    }
    //
        document.addEventListener('DOMContentLoaded', function () {
            // Check if the user is logged in
            const storedEmail = getEmailFromCookie();
            if (storedEmail) {
                // Fetch the counter value for the logged-in user
                fetch(`http://localhost:8084/api/person/counter/${storedEmail}`)
                    .then(response => {
                        if (response.ok) {
                            return response.json();
                        } else {
                            console.error("Error fetching counter value:", response.status);
                            throw new Error("Error fetching counter value");
                        }
                    })
                    .then(counter => {
                        // Update the counter value on the page
                        const counterElement = document.getElementById('count');
                        counterElement.textContent = counter;
                    })
                    .catch(error => {
                        console.error("Error:", error);
                        alert("Error fetching counter value. Please try again.");
                    });
            }
        });
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
        async function connectToMetaMask() {
            try {
                // Detect MetaMask provider
                const provider = await detectEthereumProvider();
                if (provider) {
                    // Request user to connect their MetaMask wallet
                    await provider.request({ method: 'eth_requestAccounts' });
                    console.log('Connected to MetaMask');
                } else {
                    console.error('MetaMask not detected');
                    alert('MetaMask not detected. Please install MetaMask and try again.');
                }
            } catch (error) {
                console.error('Error connecting to MetaMask:', error);
                alert('Error connecting to MetaMask. Please try again.');
            }
        }
    </script>
</body>
</html>
