
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Issue Tokens on Sunday</title>
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
        margin-left: 45px;
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
</head>

<body>
<h2>Issue Tokens If Goals Were Met</h2>
<section>
        <div class="signin-box">
            <h2>Get Your Weekly</h2>
            <h2>Rewards</h2>
            <div>
                <button id="connectButton" onclick="connect()">Connect to MetaMask</button>
                <button onclick="equals()">Issue Tokens</button>
            </div>
        </div>
    </section>
</body>
<script src="https://cdn.jsdelivr.net/npm/@metamask/detect-provider"></script>
<script src="https://code.jquery.com/jquery-3.6.4.min.js"></script>
<script src="https://cdn.ethers.io/lib/ethers-5.0.umd.min.js"></script>
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
            const accounts = await window.ethereum.request({ method: 'eth_accounts' });
            selectedAccount = accounts[0];
            console.log('Connected to MetaMask with account:', selectedAccount);
        } catch (error) {
            console.error('Error connecting to MetaMask:', error);
        }
    });
    } else {
        console.error('MetaMask is not installed. Please install it to use this feature.');
    }
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
const storedEmail = getEmailFromCookie();
window.onload = function() {
    if (!getEmailFromCookie()) {
        window.location.href = '{{site.baseurl}}/index';
    }
}
async function equals() {
    const storedEmail = await getEmailFromCookie();
    const statsUrl = `http://localhost:8084/api/person/getStats?email=${encodeURIComponent(storedEmail)}`;
    const checkdayUrl = `http://localhost:8084/api/person/getCheckDay?email=${encodeURIComponent(storedEmail)}`;
    // Make requests to the endpoints
    const responseStats = await fetch(statsUrl);
    const data1 = await responseStats.json();
    const responseCheckday = await fetch(checkdayUrl);
    const data2 = await responseCheckday.json();
    const keys1 = Object.keys(data1);
    const keys2 = Object.keys(data2);
    let flag = true;
    for (const day of keys2) {
        // Ignore keys ending with "Minutes"
        if (day.toLowerCase().endsWith("minutes")) {
            continue;
        }
        const value1 = data1[day];
        console.log(value1);
        // Convert day to lowercase for case-insensitive comparison
        const formattedDay = day.toLowerCase();
        const value2 = data2[formattedDay];
        console.log(value2);
        if (value1 !== value2) {
            console.log(`Values for ${day} are not equal.`);
            flag = false;
            return false;
        }
    }
    if (flag)
    {
        console.log("All values are equal for corresponding days.");
        issueTokens();
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
    // Fetch heldWeeklyTokens from the backend
    const heldWeeklyTokensResponse = await fetch(`http://localhost:8084/api/person/getHeldWeeklyTokens?email=${encodeURIComponent(storedEmail)}`);
     if (heldWeeklyTokensResponse.ok) {
        const heldWeeklyTokens = await heldWeeklyTokensResponse.json();
        // Assuming heldWeeklyTokens is a decimal, multiply by 10^18 (Wei) to convert to the smallest unit
        const amount = heldWeeklyTokens * 1000000000;
        // const amount = ethers.utils.parseUnits(heldWeeklyTokens.toString(), 'ether');
        // Use the fetched amount to issue tokens
        const toAddress = "0xfb3131da44E49C060fc15FfaA9d8c54412C1468A";
        // Send transaction
        const tx = await trailblazeContract.mintTokens(toAddress, amount);
        // Wait for the transaction to be mined
        await tx.wait();
        console.log(`${amount} tokens sent to ${toAddress}`);
    } else {
        console.error('Error fetching heldWeeklyTokens:', heldWeeklyTokensResponse.status);
    }
}

</script>
