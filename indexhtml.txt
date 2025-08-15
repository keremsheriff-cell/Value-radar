<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover" />
  <title>Value Radar — Oran Farkı Hesaplayıcı</title>
  <meta name="theme-color" content="#101316" />
  <style>
    /* —— Temiz, mobil odaklı arayüz —— */
    :root{
      --bg:#0d1116; --card:#151b22; --muted:#9aa4af; --text:#e7edf3;
      --ok:#1f9d55; --bad:#d64545; --accent:#4a89ff; --line:#202833;
      --warn:#f59e0b;
      --radius:14px;
    }
    *{box-sizing:border-box}
    body{
      margin:0; font-family:system-ui,-apple-system,"Segoe UI",Roboto,Inter,Arial;
      background:linear-gradient(180deg,#0b0f14 0%, #0e131a 60%, #10161d 100%);
      color:var(--text); -webkit-font-smoothing:antialiased;
    }
    header{
      padding:20px 16px 6px; position:sticky; top:0; z-index:5;
      backdrop-filter: blur(6px);
      background:linear-gradient(180deg, rgba(13,17,22,.85), rgba(13,17,22,.35));
      border-bottom:1px solid var(--line);
    }
    h1{margin:0; font-size:22px; letter-spacing:.2px}
    .sub{color:var(--muted); font-size:13px; margin-top:6px}
    .wrap{padding:14px}
    .card{
      background:var(--card); border:1px solid var(--line);
      border-radius:var(--radius); padding:14px; box-shadow:0 10px 30px rgba(0,0,0,.25);
    }
    .grid{display:grid; gap:10px}
    @media(min-width:720px){ .grid{grid-template-columns:repeat(4,1fr)} }
    label{font-size:12px; color:var(--muted); display:block; margin-bottom:6px}
    input,button,select,textarea{
      width:100%; padding:12px 12px; border-radius:12px; border:1px solid var(--line);
      background:#0f141a; color:var(--text); font-size:16px;
      outline:none;
    }
    input::placeholder{color:#5e6a77}
    button{
      background:var(--accent); border:none; font-weight:600; letter-spacing:.2px;
      box-shadow:0 6px 16px rgba(74,137,255,.25); cursor:pointer;
      transition:.18s transform ease, .18s filter ease;
    }
    button:active{ transform:scale(.98) }
    .btn-ghost{ background:transparent; border:1px solid var(--line) }
    .btn-ok{ background:var(--ok) }
    .btn-bad{ background:var(--bad) }
    .row{display:grid; grid-template-columns:1fr 1fr; gap:8px}
    .row-3{display:grid; grid-template-columns:1fr 1fr 1fr; gap:8px}
    .pill{
      display:inline-flex; align-items:center; gap:6px; padding:6px 10px;
      border-radius:999px; border:1px solid var(--line); color:var(--muted); font-size:12px;
    }
    .list{margin-top:14px}
    table{width:100%; border-collapse:separate; border-spacing:0 10px}
    th,td{font-size:14px; padding:10px 10px; text-align:left}
    th{color:#9fb1c7; font-weight:600}
    tr{
      background:var(--card); border:1px solid var(--line);
    }
    tr td:first-child, tr th:first-child{ border-top-left-radius:12px; border-bottom-left-radius:12px }
    tr td:last-child, tr th:last-child{ border-top-right-radius:12px; border-bottom-right-radius:12px }
    .k{color:#9fb1c7}
    .pct{font-variant-numeric:tabular-nums; font-weight:700}
    .tag{font-size:12px; padding:6px 9px; border-radius:999px; font-weight:700; display:inline-block}
    .yes{ background:rgba(31,157,85,.18); color:#84e1ae; border:1px solid rgba(31,157,85,.35) }
    .no{ background:rgba(214,69,69,.18); color:#ffb3b3; border:1px solid rgba(214,69,69,.35) }
    .mid{ background:rgba(245,158,11,.15); color:#ffd98f; border:1px solid rgba(245,158,11,.35) }
    .tools{display:flex; gap:8px; flex-wrap:wrap}
    .hint{font-size:12px; color:var(--muted); margin-top:6px}
    .footer{color:#6b7683; font-size:12px; text-align:center; margin:26px 0 40px}
    .hr{height:1px; background:var(--line); margin:14px 0}
    .danger{color:#ffbcbc}
  </style>
</head>
<body>
  <header>
    <h1>Value Radar</h1>
    <div class="sub">Asya referansa göre oran farkını hesapla • “Oyna / Pas” sinyali al</div>
  </header>

  <div class="wrap">
    <div class="card">
      <div class="row-3">
        <div>
          <label>Maç / Not</label>
          <input id="match" placeholder="Örn: GS - TS | 1X2 Home" />
        </div>
        <div>
          <label>Piyasa (Asya Oranı)</label>
          <input id="asian" inputmode="decimal" placeholder="Örn: 1.85" />
        </div>
        <div>
          <label>Yerel Oran</label>
          <input id="local" inputmode="decimal" placeholder="Örn: 2.05" />
        </div>
      </div>

      <div class="row" style="margin-top:10px">
        <div>
          <label>Eşik (%)</label>
          <input id="threshold" inputmode="numeric" value="3" />
          <div class="tools" style="margin-top:8px">
            <span class="pill">Kısayol:</span>
            <button class="btn-ghost" type="button" data-th="2">2%</button>
            <button class="btn-ghost" type="button" data-th="3">3%</button>
            <button class="btn-ghost" type="button" data-th="5">5%</button>
            <button class="btn-ghost" type="button" data-th="8">8%</button>
          </div>
        </div>
        <div class="row" style="align-items:end">
          <button id="addBtn" type="button">Hesapla ve Ekle</button>
          <button id="quickYesBtn" type="button" class="btn-ok">Sadece “Oyna”ları Göster</button>
        </div>
      </div>

      <div class="hr"></div>

      <div class="tools">
        <button id="exportBtn" type="button" class="btn-ghost">CSV Olarak Dışa Aktar</button>
        <button id="clearBtn" type="button" class="btn-bad">Tüm Listeyi Temizle</button>
        <span class="hint">Veriler cihazında <b>lokal olarak</b> saklanır (internet gerekmez).</span>
      </div>
    </div>

    <div class="list">
      <table>
        <thead>
          <tr>
            <th>Maç / Not</th>
            <th>Asya</th>
            <th>Yerel</th>
            <th>Fark</th>
            <th>Karar</th>
            <th>Zaman</th>
            <th></th>
          </tr>
        </thead>
        <tbody id="rows"></tbody>
      </table>
    </div>

    <div class="footer">Uzun vadede hedef: <span class="k">CLV &gt; 0</span>. Disiplin = kârın kefili.</div>
  </div>

  <script>
    // —— Yardımcılar ——
    const $ = sel => document.querySelector(sel);
    const rowsEl = $('#rows');
    const stateKey = 'value-radar:v1';

    const fmtPct = x => (x>=0?'+':'') + x.toFixed(2).replace('.',',') + '%';
    const nowStr = () => {
      const d = new Date();
      const pad = n => String(n).padStart(2,'0');
      return `${pad(d.getDate())}.${pad(d.getMonth()+1)}.${d.getFullYear()} ${pad(d.getHours())}:${pad(d.getMinutes())}`;
    };

    function decisionTag(diff, th){
      // diff: yüzde, th: eşik
      if(diff >= th) return `<span class="tag yes">Oyna ✅</span>`;
      if(diff >= 0) return `<span class="tag mid">Sınırda ⚠️</span>`;
      return `<span class="tag no">Pas ❌</span>`;
    }

    function parseNum(v){
      if(!v) return NaN;
      // Türkçe ondalık alışkanlığı: virgülü noktaya çevir
      return Number(String(v).trim().replace(',','.'));
    }

    function save(data){
      localStorage.setItem(stateKey, JSON.stringify(data));
    }
    function load(){
      try { return JSON.parse(localStorage.getItem(stateKey)) || []; }
      catch { return []; }
    }

    function render(list){
      rowsEl.innerHTML = '';
      list.forEach((r, idx) => {
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td>${r.match || '-'}</td>
          <td>${r.asian}</td>
          <td>${r.local}</td>
          <td class="pct">${fmtPct(r.diff)}</td>
          <td>${decisionTag(r.diff, r.th)}</td>
          <td class="k">${r.time}</td>
          <td style="text-align:right;">
            <button data-del="${idx}" class="btn-ghost" title="Sil">Sil</button>
          </td>
        `;
        rowsEl.appendChild(tr);
      });
    }

    function addEntry(){
      const m = $('#match').value.trim();
      const a = parseNum($('#asian').value);
      const l = parseNum($('#local').value);
      const th = parseNum($('#threshold').value);

      if(!isFinite(a) || !isFinite(l) || a<=0 || l<=0){
        alert('Asya ve Yerel oranları doğru gir (örn: 1.85 ve 2.05).');
        return;
      }
      if(!isFinite(th) || th<0 || th>100){
        alert('Eşik %0 ile %100 arasında olmalı.');
        return;
      }
      const diff = (l/a - 1) * 100;
      const item = { match: m, asian: a.toFixed(2), local: l.toFixed(2), diff, th, time: nowStr() };

      const list = load();
      list.unshift(item); // en üstte kalsın
      save(list);
      render(list);

      // Formu hızla yeni girişe hazırla
      $('#local').value = '';
      $('#local').focus();
    }

    function exportCSV(){
      const list = load();
      if(!list.length){ alert('Dışa aktarılacak veri yok.'); return; }
      const head = ['Match','Asian','Local','DiffPercent','Threshold','Time'];
      const lines = [head.join(',')];
      list.forEach(r => {
        lines.push([`"${(r.match||'').replace(/"/g,'""')}"`, r.asian, r.local, r.diff.toFixed(4), r.th, `"${r.time}"`].join(','));
      });
      const blob = new Blob([lines.join('\n')], {type:'text/csv;charset=utf-8;'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url; a.download = `value-radar-${Date.now()}.csv`;
      document.body.appendChild(a); a.click(); a.remove();
      URL.revokeObjectURL(url);
    }

    function clearAll(){
      if(confirm('Tüm listeyi silmek istediğine emin misin?')){
        save([]); render([]);
      }
    }

    // —— Olaylar ——
    $('#addBtn').addEventListener('click', addEntry);
    $('#exportBtn').addEventListener('click', exportCSV);
    $('#clearBtn').addEventListener('click', clearAll);
    $('#quickYesBtn').addEventListener('click', () => {
      const th = parseNum($('#threshold').value) || 3;
      const base = load();
      const yes = base.filter(x => x.diff >= th);
      render(yes);
    });

    // threshold kısayolları
    document.querySelectorAll('[data-th]').forEach(btn=>{
      btn.addEventListener('click', ()=> { $('#threshold').value = btn.dataset.th; });
    });

    // satır silme (event delegation)
    rowsEl.addEventListener('click', (e)=>{
      const del = e.target.getAttribute('data-del');
      if(del==null) return;
      const list = load();
      list.splice(Number(del),1);
      save(list); render(list);
    });

    // Enter ile ekleme (mobil için pratik)
    ['match','asian','local','threshold'].forEach(id=>{
      document.getElementById(id).addEventListener('keydown', e=>{
        if(e.key==='Enter'){ addEntry(); }
      });
    });

    // İlk yükleme
    render(load());
  </script>
</body>
</html>
