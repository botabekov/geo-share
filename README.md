<!doctype html>
<html lang="ru">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover" />
  <title>Поделиться геолокацией</title>

  <style>
    :root{
      --bg: #0b1220;
      --card: rgba(255,255,255,.08);
      --card2: rgba(255,255,255,.06);
      --text: rgba(255,255,255,.92);
      --muted: rgba(255,255,255,.68);
      --line: rgba(255,255,255,.12);
      --primary: #3b82f6;
      --primary2:#2563eb;
      --ok:#22c55e;
      --warn:#f59e0b;
      --bad:#ef4444;
      --shadow: 0 18px 40px rgba(0,0,0,.35);
      --radius: 18px;
    }

    /* Светлая тема автоматически, если у пользователя light */
    @media (prefers-color-scheme: light) {
      :root{
        --bg:#f5f7fb;
        --card:#ffffff;
        --card2:#f1f5ff;
        --text:#0f172a;
        --muted:#475569;
        --line:rgba(15,23,42,.12);
        --shadow: 0 16px 35px rgba(2,6,23,.12);
      }
    }

    *{box-sizing:border-box}
    body{
      margin:0;
      min-height:100vh;
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Arial;
      background:
        radial-gradient(1100px 420px at 10% 0%, rgba(59,130,246,.25), transparent 60%),
        radial-gradient(900px 420px at 90% 10%, rgba(34,197,94,.18), transparent 55%),
        var(--bg);
      color: var(--text);
      padding: 18px 14px 22px;
      display:flex;
      justify-content:center;
    }

    .wrap{width:min(720px, 100%);}

    .top{
      display:flex; gap:12px; align-items:center;
      padding: 18px;
      border-radius: var(--radius);
      background: linear-gradient(180deg, rgba(255,255,255,.10), rgba(255,255,255,.04));
      border:1px solid var(--line);
      box-shadow: var(--shadow);
      backdrop-filter: blur(10px);
    }

    .icon{
      width:46px;height:46px;border-radius:14px;
      display:grid;place-items:center;
      background: linear-gradient(180deg, rgba(59,130,246,.95), rgba(37,99,235,.95));
      color:white;font-size:22px;
      flex:0 0 auto;
      box-shadow: 0 14px 24px rgba(37,99,235,.22);
    }
    h1{margin:0;font-size:18px;line-height:1.2}
    .sub{margin:4px 0 0; color:var(--muted); font-size:13.5px}

    .card{
      margin-top:14px;
      padding: 16px;
      border-radius: var(--radius);
      background: var(--card);
      border:1px solid var(--line);
      box-shadow: var(--shadow);
    }

    .row{display:flex; gap:10px; flex-wrap:wrap; margin-top:10px}
    .btn{
      appearance:none; border:none; cursor:pointer;
      border-radius: 16px;
      padding: 14px 16px;
      font-size: 16px;
      font-weight: 700;
      display:flex; align-items:center; justify-content:center;
      gap:10px;
      transition: transform .08s ease, opacity .2s ease, filter .2s ease;
      user-select:none;
      min-height:52px;
    }
    .btn:active{transform: scale(.985)}
    .btn[disabled]{opacity:.55; cursor:not-allowed}

    .btn-primary{
      background: linear-gradient(180deg, var(--primary), var(--primary2));
      color:white;
      flex:1 1 260px;
    }
    .btn-ghost{
      background: var(--card2);
      color: var(--text);
      border:1px solid var(--line);
      flex:1 1 180px;
    }
    .btn-whatsapp{
      background: linear-gradient(180deg, #22c55e, #16a34a);
      color:white;
      flex: 1 1 260px;
    }

    .status{
      margin-top:12px;
      padding: 12px 12px;
      border-radius: 14px;
      border:1px solid var(--line);
      background: rgba(0,0,0,.06);
      color: var(--muted);
      font-size: 14px;
    }
    @media (prefers-color-scheme: dark){
      .status{background: rgba(255,255,255,.06)}
    }

    .pill{
      display:inline-flex; align-items:center; gap:8px;
      padding: 8px 10px;
      border-radius: 999px;
      background: rgba(59,130,246,.12);
      border:1px solid rgba(59,130,246,.22);
      font-size: 13px;
      color: var(--text);
      margin-top:10px;
    }

    .grid{
      display:grid;
      grid-template-columns: 1fr;
      gap:10px;
      margin-top:12px;
    }
    .kv{
      padding: 12px;
      border-radius: 16px;
      border:1px solid var(--line);
      background: rgba(0,0,0,.04);
    }
    @media (prefers-color-scheme: dark){
      .kv{background: rgba(255,255,255,.06)}
    }
    .k{font-size:12.5px;color:var(--muted)}
    .v{margin-top:4px;font-size:15.5px;font-weight:700; word-break:break-word}

    .copy{
      margin-top:10px;
      display:flex; gap:10px; flex-wrap:wrap;
    }
    input{
      width:100%;
      padding: 12px 12px;
      border-radius: 14px;
      border:1px solid var(--line);
      background: transparent;
      color: var(--text);
      outline:none;
      font-size: 13px;
    }
    .tiny{
      margin-top:12px;
      color: var(--muted);
      font-size:12.5px;
      line-height:1.35;
    }

    .dot{
      width:10px;height:10px;border-radius:999px; display:inline-block;
      background: var(--warn);
      box-shadow:0 0 0 4px rgba(245,158,11,.18);
    }
    .dot.ok{
      background: var(--ok);
      box-shadow:0 0 0 4px rgba(34,197,94,.18);
    }
    .dot.bad{
      background: var(--bad);
      box-shadow:0 0 0 4px rgba(239,68,68,.16);
    }
  </style>
</head>

<body>
  <div class="wrap">
    <div class="top">
      <div class="icon">📍</div>
      <div>
        <h1>Поделиться геолокацией</h1>
        <p class="sub">Нажми “Определить” → разреши доступ → “Отправить в WhatsApp”.</p>
      </div>
    </div>

    <div class="card">
      <div class="row">
        <button id="btnGet" class="btn btn-primary">
          <span>Определить местоположение</span> <span aria-hidden="true">➜</span>
        </button>
        <button id="btnRetry" class="btn btn-ghost" disabled>
          Обновить
        </button>
      </div>

      <div id="status" class="status">
        <span class="dot"></span>
        <span id="statusText">Ожидаю нажатия кнопки.</span>
      </div>

      <div id="result" style="display:none">
        <div class="pill">✅ Локация получена</div>

        <div class="grid">
          <div class="kv">
            <div class="k">Координаты</div>
            <div class="v" id="coords">—</div>
          </div>
          <div class="kv">
            <div class="k">Точность</div>
            <div class="v" id="acc">—</div>
          </div>
          <div class="kv">
            <div class="k">Время</div>
            <div class="v" id="time">—</div>
          </div>
        </div>

        <div class="row" style="margin-top:12px">
          <a id="openMaps" class="btn btn-ghost" href="#" target="_blank" rel="noreferrer">Открыть в карте</a>
          <a id="sendWA" class="btn btn-whatsapp" href="#" target="_blank" rel="noreferrer">Отправить в WhatsApp</a>
        </div>

        <div class="copy">
          <input id="msg" readonly value="" onclick="this.select()" />
        </div>

        <div class="tiny">
          ⚠️ Это работает только с согласия: страница должна быть открыта на телефоне ребёнка и он должен разрешить доступ к геолокации.
        </div>
      </div>
    </div>
  </div>

<script>
  const btnGet = document.getElementById('btnGet');
  const btnRetry = document.getElementById('btnRetry');
  const statusText = document.getElementById('statusText');
  const statusBox = document.getElementById('status');
  const dot = statusBox.querySelector('.dot');

  const result = document.getElementById('result');
  const coordsEl = document.getElementById('coords');
  const accEl = document.getElementById('acc');
  const timeEl = document.getElementById('time');
  const msgEl = document.getElementById('msg');
  const openMaps = document.getElementById('openMaps');
  const sendWA = document.getElementById('sendWA');

  function setStatus(type, text){
    dot.className = 'dot' + (type ? ` ${type}` : '');
    statusText.textContent = text;
  }

  function fmt(n){ return Number(n).toFixed(6); }

  function getLocation(){
    if (!navigator.geolocation){
      setStatus('bad', 'Геолокация не поддерживается в этом браузере.');
      return;
    }

    btnGet.disabled = true;
    btnRetry.disabled = true;
    setStatus('', 'Определяем… (разреши доступ, если спросит)');

    navigator.geolocation.getCurrentPosition(
      (pos) => {
        const { latitude, longitude, accuracy } = pos.coords;
        const acc = Math.round(accuracy);
        const ts = new Date(pos.timestamp).toLocaleString();

        const mapsUrl = `https://www.google.com/maps?q=${latitude},${longitude}`;
        const text = `Моё местоположение: ${fmt(latitude)}, ${fmt(longitude)} (точность ~${acc}м) • ${ts}\n${mapsUrl}`;
        const waUrl = `https://wa.me/?text=${encodeURIComponent(text)}`;

        coordsEl.textContent = `${fmt(latitude)}, ${fmt(longitude)}`;
        accEl.textContent = `~ ${acc} м`;
        timeEl.textContent = ts;
        msgEl.value = text;

        openMaps.href = mapsUrl;
        sendWA.href = waUrl;

        result.style.display = 'block';
        setStatus('ok', 'Готово ✅ Можно отправлять в WhatsApp.');

        btnGet.disabled = false;
        btnRetry.disabled = false;
      },
      (err) => {
        let hint = '';
        // Частые причины: запрет, выключена геолокация, нет разрешения
        if (err.code === 1) hint = 'Доступ запрещён. Разреши геолокацию в браузере.';
        if (err.code === 2) hint = 'Нет данных. Проверь, включена ли Геолокация и интернет.';
        if (err.code === 3) hint = 'Таймаут. Попробуй ещё раз или выйди на улицу/к окну.';
        setStatus('bad', `Ошибка: ${err.message}. ${hint}`);
        btnGet.disabled = false;
        btnRetry.disabled = false;
      },
      { enableHighAccuracy: true, timeout: 15000, maximumAge: 0 }
    );
  }

  btnGet.addEventListener('click', getLocation);
  btnRetry.addEventListener('click', getLocation);
</script>
</body>
</html>
