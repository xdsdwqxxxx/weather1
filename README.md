<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>WeatherTM</title>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
}

body {
    background: #0f172a;
    color: white;
    padding: 20px;
}

/* контейнер */
.container {
    max-width: 420px;
    margin: auto;
}

/* заголовок */
.header {
    text-align: center;
    margin-bottom: 25px;
}

.app-name {
    font-size: 18px;
    opacity: 0.6;
}

.city {
    font-size: 28px;
    font-weight: 600;
}

/* текущая погода */
.current {
    background: linear-gradient(135deg, #1e293b, #334155);
    border-radius: 20px;
    padding: 25px;
    text-align: center;
    box-shadow: 0 10px 30px rgba(0,0,0,0.3);
}

.temp {
    font-size: 90px;
    font-weight: 700;
    letter-spacing: -2px;
}

.desc {
    margin-top: 5px;
    opacity: 0.7;
}

.row {
    display: flex;
    justify-content: center;
    gap: 20px;
    margin-top: 10px;
    font-size: 16px;
    opacity: 0.8;
}

/* прогноз */
.forecast {
    margin-top: 25px;
    background: #1e293b;
    border-radius: 20px;
    padding: 10px;
}

.day {
    display: flex;
    justify-content: space-between;
    padding: 14px 10px;
    border-bottom: 1px solid rgba(255,255,255,0.08);
}

.day:last-child {
    border-bottom: none;
}

.day-name {
    font-weight: 600;
    width: 70px;
}

.day-temp {
    font-weight: 500;
}

</style>
</head>

<body>

<div class="container">

    <div class="header">
        <div class="app-name">WeatherTM</div>
        <div class="city">Курган</div>
    </div>

    <div class="current">
        <div class="temp" id="temp">--</div>
        <div class="desc" id="desc">Загрузка...</div>

        <div class="row">
            <div>💨 <span id="wind">--</span> м/с</div>
            <div>🤏 <span id="feels">--</span>°</div>
        </div>
    </div>

    <div class="forecast" id="forecast"></div>

</div>

<script>
const url = "https://api.open-meteo.com/v1/forecast?latitude=55.44&longitude=65.34&current=temperature_2m,apparent_temperature,weather_code,wind_speed_10m&daily=weather_code,temperature_2m_max,temperature_2m_min&timezone=auto";

const codes = {
    0:"Ясно ☀️",1:"Солнечно 🌤",2:"Переменно ⛅️",3:"Облачно ☁️",
    61:"Дождь 🌧",71:"Снег ❄️",95:"Гроза ⛈"
};

const days = ["ВС","ПН","ВТ","СР","ЧТ","ПТ","СБ"];

async function getWeather() {
    try {
        const res = await fetch(url);
        const data = await res.json();

        document.getElementById("temp").innerText =
            Math.round(data.current.temperature_2m) + "°";

        document.getElementById("wind").innerText =
            Math.round(data.current.wind_speed_10m);

        document.getElementById("feels").innerText =
            Math.round(data.current.apparent_temperature);

        document.getElementById("desc").innerText =
            codes[data.current.weather_code] || "Погода";

        const forecast = document.getElementById("forecast");
        forecast.innerHTML = "";

        for (let i = 0; i < 7; i++) {
            const date = new Date(data.daily.time[i]);
            const dayName = days[date.getDay()];

            const el = document.createElement("div");
            el.className = "day";

            el.innerHTML = `
                <div class="day-name">${dayName}</div>
                <div>${codes[data.daily.weather_code[i]] || ""}</div>
                <div class="day-temp">
                    ${Math.round(data.daily.temperature_2m_max[i])}° /
                    ${Math.round(data.daily.temperature_2m_min[i])}°
                </div>
            `;

            forecast.appendChild(el);
        }

    } catch {
        document.getElementById("desc").innerText = "Ошибка загрузки";
    }
}

getWeather();
</script>

</body>
</html>
