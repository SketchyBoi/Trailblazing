
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weekly Goals</title>
    <style>
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }
    body {
        margin: 0;
        padding: 0;
        font-family: 'Arial', sans-serif;
    }
    section {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    background: linear-gradient(to right, #141e30, #243b55);
    padding: 20px;
    width: 100%;
    box-sizing: border-box;
    min-height: 100vh;
}
h2 {
    font-size: 2em;
    color: white;
    margin-bottom: 20px;
    align-self: flex-start; /* Align the h2 to the left within the flex container */
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
    table {
        width: 100%;
        border-collapse: collapse;
        margin-top: 20px;
        background-color: rgba(255, 255, 255, 0.1);
        border: 2px solid rgba(255, 255, 255, 0.5);
        border-radius: 10px;
        overflow: hidden;
    }
    th, td {
        border: 1px solid #ddd;
        padding: 8px;
        text-align: center;
        background-color: rgba(255, 255, 255, 0.05);
        color: white;
    }
    th {
        background-color: #adb3c7;
    }
    .checkbox-col {
        text-align: center;
    }
    .minutes-col select {
        width: 100%;
    }
    #downloadBtn {
    margin-top: 20px;
    width: 20%;
    height: 40px;
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
#downloadBtn:hover {
    background: #2980b9;
}
</style>
</head>

<body>
<h2>Plan Your Week</h2>
<section>
<table>
    <thead>
        <tr>
            <th>Day</th>
            <th>Sunday</th>
            <th>Monday</th>
            <th>Tuesday</th>
            <th>Wednesday</th>
            <th>Thursday</th>
            <th>Friday</th>
            <th>Saturday</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th>Workout?</th>
            <td class="checkbox-col"><input type="checkbox" id="sundayCheckbox"></td>
            <td class="checkbox-col"><input type="checkbox" id="mondayCheckbox"></td>
            <td class="checkbox-col"><input type="checkbox" id="tuesdayCheckbox"></td>
            <td class="checkbox-col"><input type="checkbox" id="wednesdayCheckbox"></td>
            <td class="checkbox-col"><input type="checkbox" id="thursdayCheckbox"></td>
            <td class="checkbox-col"><input type="checkbox" id="fridayCheckbox"></td>
            <td class="checkbox-col"><input type="checkbox" id="saturdayCheckbox"></td>
        </tr>
        <tr>
            <th>Minutes</th>            
            <td class="minutes-col">
                <select id="sundaySelect" disabled>
                    <option value="15">15</option>
                    <option value="30">30</option>
                    <option value="45">45</option>
                    <option value="60">60</option>
                </select>
            </td>
            <td class="minutes-col">
                <select id="mondaySelect" disabled>
                    <option value="15">15</option>
                    <option value="30">30</option>
                    <option value="45">45</option>
                    <option value="60">60</option>
                </select>
            </td>
            <td class="minutes-col">
                <select id="tuesdaySelect" disabled>
                    <option value="15">15</option>
                    <option value="30">30</option>
                    <option value="45">45</option>
                    <option value="60">60</option>
                </select>
            </td>
            <td class="minutes-col">
                <select id="wednesdaySelect" disabled>
                    <option value="15">15</option>
                    <option value="30">30</option>
                    <option value="45">45</option>
                    <option value="60">60</option>
                </select>
            </td>
            <td class="minutes-col">
                <select id="thursdaySelect" disabled>
                    <option value="15">15</option>
                    <option value="30">30</option>
                    <option value="45">45</option>
                    <option value="60">60</option>
                </select>
            </td>
            <td class="minutes-col">
                <select id="fridaySelect" disabled>
                    <option value="15">15</option>
                    <option value="30">30</option>
                    <option value="45">45</option>
                    <option value="60">60</option>
                </select>
            </td>
            <td class="minutes-col">
                <select id="saturdaySelect" disabled>
                    <option value="15">15</option>
                    <option value="30">30</option>
                    <option value="45">45</option>
                    <option value="60">60</option>
                </select>
            </td>
        </tr>
    </tbody>
</table>
<button id="downloadBtn">Download JSON</button>
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
    document.getElementById('downloadBtn').addEventListener('click', function (event) {
        event.preventDefault();
        const data = {};
        const checkboxes = document.querySelectorAll('input[type="checkbox"]');
        checkboxes.forEach(checkbox => {
            const correspondingSelect = document.getElementById(checkbox.id.replace("Checkbox", "Select"));
            data[checkbox.id.replace("Checkbox", "")] = checkbox.checked;
            data[correspondingSelect.id.replace("Select", "") + "Minutes"] = checkbox.checked ? correspondingSelect.value : 0;
        });

        const jsonData = JSON.stringify(data, null, 2);

        const blob = new Blob([jsonData], { type: 'application/json' });
        const link = document.createElement('a');
        link.href = URL.createObjectURL(blob);
        link.download = 'weekly_plan.json';
        link.click();
        const storedEmail = getEmailFromCookie();
        const url = `http://localhost:8084/api/person/setStats?email=${encodeURIComponent(storedEmail)}`;
        fetch(url, {
            method: 'POST',
            mode: 'cors',
            cache: 'no-cache',
            headers: {
                'Content-Type': 'application/json',
            },
            body: jsonData,
        })
            .then(response => response.json())
            .then(data => console.log('Success:', data))
            .catch(error => console.error('Error:', error));
    });

    const checkboxes = document.querySelectorAll('input[type="checkbox"]');
    checkboxes.forEach(checkbox => {
        checkbox.addEventListener('change', function () {
            const correspondingSelect = document.getElementById(this.id.replace("Checkbox", "Select"));
            correspondingSelect.disabled = !this.checked;

            const optionZero = correspondingSelect.querySelector('option[value="0"]');
            if (optionZero) {
                optionZero.remove();
            } else if (!this.checked) {
                correspondingSelect.insertAdjacentHTML('beforeend', '<option value="0">0</option>');
            }
        });
    });
</script>
</body>

</html>