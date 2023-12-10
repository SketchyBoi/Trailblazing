<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Token Issuer</title>
</head>
<body>
    <h1>Token Issuer</h1>
    <button onclick="issueTokens()">Issue Tokens</button>
    <script src="https://cdn.jsdelivr.net/npm/web3@1.3.6/dist/web3.min.js"></script>
    <script>
        // Connect to the Mumbai testnet using Web3.js
        async function connectToBlockchain() {
            if (window.ethereum) {
                window.web3 = new Web3(window.ethereum);
                try {
                    // Switch to the Mumbai testnet
                    await window.ethereum.request({
                        method: 'wallet_addEthereumChain',
                        params: [
                            {
                                chainId: '0x13881', // Mumbai testnet chain ID
                                chainName: 'Mumbai Testnet',
                                nativeCurrency: {
                                    name: 'Matic',
                                    symbol: 'MATIC',
                                    decimals: 18,
                                },
                                rpcUrls: ['https://rpc-mumbai.maticvigil.com/'], // Mumbai testnet RPC endpoint
                                blockExplorerUrls: ['https://mumbai.polygonscan.com/'], // Mumbai testnet block explorer
                            },
                        ],
                    });
                    // Request account access if needed
                    await window.ethereum.enable();
                    console.log("Connected to blockchain successfully!");
                    return true;
                } catch (error) {
                    console.error("User denied account access or error connecting to Mumbai testnet", error);
                    return false;
                }
            } else if (window.web3) {
                window.web3 = new Web3(window.web3.currentProvider);
                return true;
            } else {
                console.error("No Ethereum provider detected");
                return false;
            }
        }
        async function fetchContractABI() {
            try {
                const response = await fetch('./abi.json');
                const abi = await response.json(); // Parse the JSON string
                return abi;
            } catch (error) {
                console.error("Error fetching ABI:", error);
                return null;
            }
        }
        async function getConnectedAccount() {
            const account = await getConnectedAccount();
            console.log("Connected account:", account);
            return accounts[0];
        }
        // Issue tokens using the smart contract
        async function issueTokens() {
            console.log("Check");
            const isConnected = await connectToBlockchain();
            if (!isConnected) return;
            const contractABI = await fetchContractABI();
            if (!contractABI) return;
            const contractAddress = 0x5FbDB2315678afecb367f032d93F642f64180aa3;
            // Create a contract instance
            const contract = new web3.eth.Contract(contractABI, contractAddress);
            // Replace with the connected Metamask account
            const account = await getConnectedAccount(); //HERE IS THE ERROR OR THE GETCONNECTEDACCOUNT METHOD
            // Replace with the amount of tokens to issue
            const amount = 1;
            // Call the issueTokens method on the smart contract
            try {
                const result = await contract.methods.issueTokens(account, amount).send({ from: account });
                console.log("Tokens issued successfully:", result);
            } catch (error) {
                console.error("Error issuing tokens:", error);
            }
        }
    </script>
</body>
</html>
