<!DOCTYPE html>
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
    <div>
        <button id="connectButton" onclick="connect()">Connect to MetaMask</button>
        <button onclick="issueTokens()">Issue Tokens</button>
        <label for="fileInput">Upload Text File:</label>
        <input type="file" id="fileInput" accept=".txt" />
        <button onclick="uploadFile()">Upload File</button>
    </div>
    <script>
        let selectedAccount = 0;
        let signer = new ethers.providers.Web3Provider(window.ethereum).getSigner();
        // Check if MetaMask is installed
        async function connect() {
            if (typeof window.ethereum !== 'undefined') {
            const connectButton = document.getElementById('connectButton');
            // Handle button click event
            connectButton.addEventListener('click', async () => {
                try {
                    // Request account access if needed
                    await window.ethereum.request({ method: 'eth_requestAccounts' });
                    // Now MetaMask is connected, you can use window.ethereum to interact with the wallet
                    // For example, you can get the selected account:
                    const accounts = await window.ethereum.request({ method: 'eth_accounts' });
                    selectedAccount = accounts[0];
                    console.log('Connected to MetaMask with account:', selectedAccount);
                    // You can now perform actions with the connected account or interact with a smart contract
                } catch (error) {
                    console.error('Error connecting to MetaMask:', error);
                }
            });
            } else {
                console.error('MetaMask is not installed. Please install it to use this feature.');
            }
        }
        async function issueTokens() {
            const contractAddress = "0xc9cAA4263F8D662182a09a7d364BaE99BBeec719";
            // Connect to the deployed contract
            const trailblazeContract = new ethers.Contract(
                contractAddress,
                ['function mintTokens(address to, uint256 amount)'],
                signer
            );
            const toAddress = "0xfb3131da44E49C060fc15FfaA9d8c54412C1468A";
            const amount = 1; // Assuming 18 decimals
            // Send transaction
            const tx = await trailblazeContract.mintTokens(toAddress, amount);
            // Wait for the transaction to be mined
            await tx.wait();
            console.log(`${amount} tokens sent to ${toAddress}`);
        }

</script>
</body>

</html>
