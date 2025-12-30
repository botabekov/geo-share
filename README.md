<!doctype html>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Поделиться геолокацией</title>
<style>
  body{font-family:system-ui;margin:24px;max-width:720px}
  button{padding:12px 16px;font-size:16px;margin:8px 0}
  .box{background:#f5f5f5;padding:12px;border-radius:10px}
  input{width:100%;padding:10px;font-size:14px}
</style>

<h2>Поделиться геолокацией</h2>
<p>Нажми кнопку — браузер спросит разрешение.</p>

<button id="get">Определить местоположение</button>

<div id="result" class="box" style="display:none"></div>

<script>
const res = document.getElementById('result');
const getBtn = document.getElementById('get');

function show(html){
  res.style.display = 'block';
  res.innerHTML = html;
}

getBtn.onclick = () => {
  if (!navigator.geolocation) {
    show("Геолокация не поддерживается в этом браузере.");
    return;
  }

  getBtn.disabled = true;
  show("Определяем…");

  navigator.geolocation.getCurrentPosition(
    (pos) => {
      const { latitude, longitude, accuracy } = pos.coords;
      const acc = Math.round(accuracy);
      const ts = new Date(pos.timestamp).toLocaleString();

      const maps = `https://www.google.com/maps?q=${latitude},${longitude}`;
      const text = `Моё местоположение: ${latitude.toFixed(6)}, ${longitude.toFixed(6)} (точность ~${acc}м) • ${ts}\n${maps}`;
      const wa = `https://wa.me/?text=${encodeURIComponent(text)}`;

      show(`
        <p><b>Готово ✅</b></p>
        <p>Координаты: <code>${latitude.toFixed(6)}, ${longitude.toFixed(6)}</code></p>
        <p>Точность: ~<code>${acc} м</code> • ${ts}</p>
        <p><a href="${maps}" target="_blank" rel="noreferrer">Открыть в Google Maps</a></p>
        <button onclick="location.href='${wa}'">Отправить в WhatsApp</button>
        <p>Текст для копирования:</p>
        <input value="${text.replaceAll('"','&quot;')}" readonly onclick="this.select()">
      `);

      getBtn.disabled = false;
    },
    (err) => {
      show("Ошибка: " + err.message + "<br><br>Проверь: включена ли Геолокация и дано ли разрешение браузеру.");
      getBtn.disabled = false;
    },
    { enableHighAccuracy: true, timeout: 15000, maximumAge: 0 }
  );
};
</script>
