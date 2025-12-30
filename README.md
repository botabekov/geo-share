<!doctype html>
<html lang="ru">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover" />
  <title>Отправить местоположение</title>
  <style>
    :root{
      --bg:#0b1220; --card:rgba(255,255,255,.08); --line:rgba(255,255,255,.12);
      --text:rgba(255,255,255,.92); --muted:rgba(255,255,255,.68);
      --ok:#22c55e; --bad:#ef4444; --warn:#f59e0b; --primary:#3b82f6; --primary2:#2563eb;
      --shadow:0 18px 40px rgba(0,0,0,.35); --radius:18px;
    }
    @media (prefers-color-scheme: light){
      :root{--bg:#f5f7fb; --card:#fff; --line:rgba(15,23,42,.12); --text:#0f172a; --muted:#475569; --shadow:0 16px 35px rgba(2,6,23,.12);}
    }
    *{box-sizing:border-box}
    body{
      margin:0; min-height:100vh;
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Arial;
      background:
        radial-gradient(1000px 420px at 10% 0%, rgba(59,130,246,.25), transparent 60%),
        radial-gradient(900px 420px at 90% 10%, rgba(34,197,94,.18), transparent 55%),
        var(--bg);
      color:var(--text);
      padding:18px 14px;
      display:flex; justify-content:center; align-items:flex-start;
    }
    .wrap{width:min(720px,100%)}
    .head{
      padding:18px;
      border-radius:var(--radius);
      background:linear-gradient(180deg, rgba(255,255,255,.10), rgba(255,255,255,.04));
      border:1px solid var(--line);
      box-shadow:var(--shadow);
      backdrop-filter: blur(10px);
      display:flex; gap:12px; align-items:center;
    }
    .icon{
      width:46px;height:46px;border-radius:14px;display:grid;place-items:center;
      background:linear-gradient(180deg, var(--primary), var(--primary2));
      color:#fff;font-size:22px; flex:0 0 auto;
    }
    h1{margin:0;font-size:18px;line-height:1.2}
    .sub{margin:4px 0 0;color:var(--muted);font-size:13.5px}
    .card{
      margin-top:14px;
      padding:16px;
      border-radius:var(--radius);
      background:var(--card);
      border:1px solid var(--line);
      box-shadow:var(--shadow);
    }
    .btn{
      width:100%;
      border:none; cursor:pointer;
      border-radius:16px;
      padding:16px 16px;
      font-size:17px;
      font-weight:800;
      color:#fff;
      background:linear-gradient(180deg, var(--primary), var(--primary2));
      display:flex; align-items:center; justify-content:center; gap:10px;
      min-height:56px;
      user-select:none;
      transition: transform .08s ease, opacity .2s ease;
    }
    .btn:active{transform:scale(.985)}
    .btn[disabled]{opacity:.6; cursor:not-allowed}
    .status{
      margin-top:12px;
      padding:12px;
      border-radius:14px;
      border:1px solid var(--line);
      background: rgba(0,0,0,.04);
      color:var(--muted);
      font-size:14px;
      display:flex; gap:10px; align-items:flex-start;
    }
    @media (prefers-color-scheme: dark){ .status{background: rgba(255,255,255,.06)} }
    .dot{width:10px;height:10px;border-radius:999px; margin-top:4px; background:var(--warn); box-shadow:0 0 0 4px rgba(245,158,11,.18)}
    .dot.ok{background:var(--ok); box-shadow:0 0 0 4px rgba(34,197,94,.18)}
    .dot.bad{background:var(--bad); box-shadow:0 0 0 4px rgba(239,68,68,.16)}
    .tiny{margin-top:12px;color:var(--muted);font-size:12.5px;line-height:1.35}
    code{padding:2px 6px;border-radius:8px;background:rgba(0,0,0,.08);border:1px solid var(--line)}
    @media (prefers-color-scheme: dark){ code{background:rgba(255,255,255,.06)} }
  </style>
</head>
<body>
  <div class="wrap">
    <div class="head">
      <div class="icon">📍</div>
      <div>
        <h1>Отправить местоположение</h1>
        <p class="sub">Нажми одну кнопку → разреши геолокацию → откроется WhatsApp с готовым сообщением.</p>
      </div>
    </div>

    <div class="card">
      <button id="sendBtn" class="btn">
        Отправить местоположение <span aria-hidden="true">→</span>
      </button>

      <div id="status" class="status">
        <span id="dot" class="dot"></span>
        <div id="statusText">Ожидаю нажатия кнопки.</div>
      </div>

      <div class="tiny">
        Подсказка: ты можешь задать “от кого” и “кому” прямо в ссылке, например:<br>
        <code>?from=Али&to=77011234567</code><br>
        <span style="opacity:.9">to — номер WhatsApp получателя в международном формате (без + и пробелов).</span>
      </div>
    </div>
  </div>

<script>
  const btn = document.getElementById('sendBtn');
  const statusText = document.getElementById('statusText');
  const dot = document.getElementById('dot');

  function setStatus(type, text){
    dot.className = 'dot' + (type ? ' ' + type : '');
    statusText.textContent = text;
  }

  function fmt(n){ return Number(n).toFixed(6); }

  function buildWhatsAppUrl(message, toPhone){
    // Если указан номер — откроется чат сразу с этим номером
    // Если нет — откроется WhatsApp с выбором чата
    if (toPhone) return `https://wa.me/${toPhone}?text=${encodeURIComponent(message)}`;
    return `https://wa.me/?text=${encodeURIComponent(message)}`;
  }

  btn.addEventListener('click', () => {
    const params = new URLSearchParams(location.search);
    const fromName = params.get('from') || 'Ребёнок';
    const toPhoneRaw = (params.get('to') || '').trim();
    // чистим номер: только цифры
    const toPhone = toPhoneRaw.replace(/\D/g,'');

    if (!navigator.geolocation){
      setStatus('bad', 'Геолокация не поддерживается в этом браузере.');
      return;
    }

    btn.disabled = true;
    setStatus('', 'Определяем… Разреши доступ к геолокации, если появится запрос.');

    navigator.geolocation.getCurrentPosition(
      (pos) => {
        const { latitude, longitude, accuracy } = pos.coords;
        const acc = Math.round(accuracy);
        const ts = new Date(pos.timestamp).toLocaleString();

        const mapsUrl = `https://www.google.com/maps?q=${latitude},${longitude}`;

        const message =
`От кого получили: ${fromName}
Отправить местоположение ✅
Координаты: ${fmt(latitude)}, ${fmt(longitude)}
Точность: ~${acc} м
Время: ${ts}
Карта: ${mapsUrl}`;

        setStatus('ok', 'Готово ✅ Открываю WhatsApp…');

        const waUrl = buildWhatsAppUrl(message, toPhone || '');
        // небольшой таймаут, чтобы текст статуса успел обновиться
        setTimeout(() => { window.location.href = waUrl; }, 250);

        // на всякий случай включим кнопку обратно через пару секунд
        setTimeout(() => { btn.disabled = false; }, 2000);
      },
      (err) => {
        let hint = '';
        if (err.code === 1) hint = 'Доступ запрещён. Разреши геолокацию в браузере.';
        if (err.code === 2) hint = 'Нет данных. Проверь, включена ли Геолокация и интернет.';
        if (err.code === 3) hint = 'Таймаут. Попробуй ещё раз или выйди к окну/на улицу.';
        setStatus('bad', `Ошибка: ${err.message}. ${hint}`);
        btn.disabled = false;
      },
      { enableHighAccuracy: true, timeout: 15000, maximumAge: 0 }
    );
  });
</script>
</body>
</html>
