<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>P X GPT Authenticity Scanner</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap" rel="stylesheet">
<style>
:root{--bg:#0b1220;--primary:#7c3aed;--primary-h:#6d28d9;--card:#1e293b;--card-h:#273449;--text:#f1f5f9;--green:#22c55e;--red:#ef4444;--gray:#94a3b8}
*{box-sizing:border-box;margin:0;padding:0;font-family:"Inter",sans-serif}
body{background:var(--bg);color:var(--text);min-height:100vh;display:flex;flex-direction:column}
#header{display:flex;align-items:center;gap:.75rem;padding:1rem 2rem;background:#111827;border-bottom:1px solid #1e293b;position:fixed;top:0;width:100%;z-index:999}
#header img{height:40px;border-radius:8px}
#header h1{font-size:1.2rem;color:var(--text);font-weight:600}
#toolpanel{position:fixed;top:64px;bottom:0;width:240px;background:#111827;padding-top:1rem;transition:.35s cubic-bezier(.4,0,.2,1);box-shadow:0 0 16px rgba(0,0,0,.6);z-index:1000;left:-240px}
#toolpanel.show{left:0}
.toggle{position:fixed;top:1rem;left:1rem;width:48px;height:48px;background:var(--card);border:1px solid var(--gray);border-radius:12px;display:flex;align-items:center;justify-content:center;cursor:pointer;z-index:1001;transition:.25s}
.toggle:hover{background:var(--card-h)}
h3.panel-title{padding-left:1.5rem;margin-bottom:.7rem;font-weight:600;color:var(--gray);text-transform:uppercase;font-size:.8rem;letter-spacing:.05em}
button.menu{display:block;width:100%;background:none;border:none;color:var(--text);font-size:1.05rem;text-align:left;padding:.65rem 1.5rem;cursor:pointer;border-radius:8px;transition:.2s}
button.menu:hover{background:var(--primary-h)}
button.menu.active{background:var(--primary)}
main{margin:auto;padding:6rem 40px 2rem 300px;max-width:900px;width:100%}
.card{background:var(--card);border-radius:1.25rem;padding:2rem 2.2rem;box-shadow:0 6px 22px rgba(0,0,0,.45);display:none;transition:.3s}
.card.active{display:block}
.card h2{font-size:1.4rem;font-weight:600;margin-bottom:1rem}
.input{margin:1.5rem 0;display:flex;gap:1rem;flex-wrap:wrap}
.input input[type=file],.input textarea{flex:1 1 250px;padding:.7rem;border:2px dashed var(--primary);border-radius:.75rem;background:#0f172a;color:var(--text);font-size:.95rem}
textarea{height:120px;resize:vertical}
.scan{background:var(--primary);border:none;padding:.8rem 1.6rem;border-radius:.75rem;font-weight:600;cursor:pointer;transition:.2s}
.scan:hover{background:var(--primary-h);transform:translateY(-2px)}
.result{margin-top:1rem;line-height:1.6;font-size:.95rem}
.success{color:var(--green);font-weight:600}
.danger{color:var(--red);font-weight:600}
.history-item{border-bottom:1px solid var(--card-h);padding:.75rem 0}
.history-item:last-child{border:none}
.history-item time{font-size:.8rem;color:var(--gray)}
.footer{margin-top:2rem;text-align:center;font-size:.8rem;color:var(--gray)}
@media(max-width:768px){#toolpanel{width:200px;left:-200px}main{padding:6rem 20px 2rem 20px}}
@media(max-width:480px){.toggle{width:42px;height:42px}.card{padding:1.2rem 1.4rem}#header h1{font-size:.95rem}}
</style>
</head>
<body>
<div id="header">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/04/ChatGPT_logo.svg/768px-ChatGPT_logo.svg.png" alt="Logo">
  <h1>P X GPT Authenticity Scanner</h1>
</div>
<div id="open-tools" class="toggle" onclick="togglePanel()">☰</div>
<div id="toolpanel">
  <h3 class="panel-title">Tools</h3>
  <button class="menu active" onclick="showTool('image',this)">📷 Image</button>
  <button class="menu" onclick="showTool('video',this)">🎞️ Video</button>
  <button class="menu" onclick="showTool('audio',this)">🔊 Audio</button>
  <button class="menu" onclick="showTool('text',this)">📝 Text</button>
  <button class="menu" onclick="showTool('history',this)">🗂️ History</button>
</div>
<main>
  <section id="card-image" class="card active">
    <h2>Image Authenticity Scanner</h2>
    <div class="input"><input type="file" id="image-file" accept="image/*"><button class="scan" onclick="scanImage()">Scan</button></div>
    <div id="image-result" class="result"></div>
  </section>
  <section id="card-video" class="card"><h2>Video Scanner</h2>
    <div class="input"><input type="file" id="video-file" accept="video/*"><button class="scan" onclick="placeholder('video-result')">Scan</button></div>
    <div id="video-result" class="result"></div>
  </section>
  <section id="card-audio" class="card"><h2>Audio Scanner</h2>
    <div class="input"><input type="file" id="audio-file" accept="audio/*"><button class="scan" onclick="placeholder('audio-result')">Scan</button></div>
    <div id="audio-result" class="result"></div>
  </section>
  <section id="card-text" class="card"><h2>Text Scanner</h2>
    <div class="input"><textarea id="text-input" placeholder="Paste text here"></textarea><button class="scan" onclick="placeholder('text-result')">Scan</button></div>
    <div id="text-result" class="result"></div>
  </section>
  <section id="card-history" class="card"><h2>Scan History</h2>
    <div id="history-list"></div>
  </section>
  <div class="footer">© 2025 • Powered by GPT‑4o‑mini • Built with ❤️</div>
</main>
<script>
function togglePanel(){document.getElementById('toolpanel').classList.toggle('show');}
function showTool(name,btn){
  document.querySelectorAll('#toolpanel .menu').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
  document.querySelectorAll('.card').forEach(c=>c.classList.remove('active'));
  document.getElementById('card-'+name).classList.add('active');
  if(name==='history'){renderHistory();}
}
const apiKey="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";
async function scanImage(){
  const file=document.getElementById('image-file').files[0];
  const out=document.getElementById('image-result');
  if(!file){alert('Please choose image');return;}
  out.innerHTML='<em>🔍 Scanning in progress… please wait</em>';
  const dataUrl=await to64(file);
  const messages=[
    {role:'system',content:'You are an expert image AI detector. First, give a bolded verdict with emoji, % confidence and [ai] or [real]. Then give 3 clear bullet point evidence with links from internet-based reasoning.'},
    {role:'user',content:[{type:'image_url',image_url:{url:dataUrl,detail:'low'}},{type:'text',text:'Please analyse and determine AI or real.'}]}
  ];
  const r=await fetch('https://api.openai.com/v1/chat/completions',{
    method:'POST',
    headers:{'Content-Type':'application/json','Authorization':'Bearer '+apiKey},
    body:JSON.stringify({model:'gpt-4o-mini',messages,temperature:0.3})
  });
  const j=await r.json();
  if(!j.choices){out.textContent='⚠️ Analysis failed';return;}
  let html=j.choices[0].message.content
    .replace(/\n/g,'<br>')
    .replace(/\*\*(.*?)\*\*/g,'<strong>$1</strong>')
    .replace(/\[real\]/gi,'<span class="success">[real]</span>')
    .replace(/\[ai\]/gi,'<span class="danger">[ai]</span>')
    .replace(/\[\/\]/g,'</span>');
  out.innerHTML=html;
  saveHistory({time:new Date().toLocaleString(),html});
}
function placeholder(id){document.getElementById(id).textContent='🚧 Integration coming soon'}
const to64=f=>new Promise((res,rej)=>{const fr=new FileReader();fr.onload=()=>res(fr.result);fr.onerror=rej;fr.readAsDataURL(f);});
function saveHistory(entry){
  const data=JSON.parse(localStorage.getItem('scanHistory')||'[]');
  data.unshift(entry);localStorage.setItem('scanHistory',JSON.stringify(data.slice(0,50)));
}
function renderHistory(){
  const list=document.getElementById('history-list');
  const data=JSON.parse(localStorage.getItem('scanHistory')||'[]');
  if(!data.length){list.innerHTML='<p>No history yet.</p>';return;}
  list.innerHTML=data.map(item=>`<div class="history-item"><time>${item.time}</time><div>${item.html}</div></div>`).join('');
}
</script>
</body>
</html>
