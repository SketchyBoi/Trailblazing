
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weekly Goals</title>
    <style>
    table {
        width: 100%;
        border-collapse: collapse;
        margin-top: 20px;
    }
    th, td {
        border: 1px solid #ddd;
        padding: 8px;
        text-align: center;
    }
    th {
        background-color: #f2f2f2;
    }
    .checkbox-col {
        text-align: center;
    }
    .minutes-col select {
        width: 100%;
    }
    #downloadBtn {
        margin-top: 20px;
    }
    </style>
</head>

<body>
<h2>Plan Your Week</h2>

<table>
    <thead>
        <tr>
            <th>Day</th>
            <th>Monday</th>
            <th>Tuesday</th>
            <th>Wednesday</th>
            <th>Thursday</th>
            <th>Friday</th>
            <th>Saturday</th>
            <th>Sunday</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th>Workout?</th>
            <td class="checkbox-col"><input type="checkbox" id="mondayCheckbox"></td>
            <td class="checkbox-col"><input type="checkbox" id="tuesdayCheckbox"></td>
            <td class="checkbox-col"><input type="checkbox" id="wednesdayCheckbox"></td>
            <td class="checkbox-col"><input type="checkbox" id="thursdayCheckbox"></td>
            <td class="checkbox-col"><input type="checkbox" id="fridayCheckbox"></td>
            <td class="checkbox-col"><input type="checkbox" id="saturdayCheckbox"></td>
            <td class="checkbox-col"><input type="checkbox" id="sundayCheckbox"></td>
        </tr>
        <tr>
            <th>Minutes</th>
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
            <td class="minutes-col">
                <select id="sundaySelect" disabled>
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

<script>
    document.getElementById('downloadBtn').addEventListener('click', function () {
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