<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Cuentas Bancarias</title>
<style>
  *{box-sizing:border-box;margin:0;padding:0}
  body{font-family:Arial,sans-serif;font-size:14px;background:#f5f5f5;color:#1a1a1a}
  header{background:#185FA5;color:white;padding:16px 24px;display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:10px}
  header h1{font-size:20px;font-weight:600}
  #save-info{font-size:12px;opacity:.85;margin-top:2px;display:flex;align-items:center;gap:6px}
  .dot{width:8px;height:8px;border-radius:50%;background:#34a853;display:inline-block;flex-shrink:0}
  .dot.syncing{background:#fbbc04;animation:pulse 1s infinite}
  .dot.error{background:#ea4335}
  @keyframes pulse{0%,100%{opacity:1}50%{opacity:.3}}
  .header-btns{display:flex;gap:8px;flex-wrap:wrap}
  .btn{font-size:13px;padding:8px 16px;border-radius:8px;border:1px solid #ccc;background:white;color:#1a1a1a;cursor:pointer}
  .btn:hover{background:#f0f0f0}
  .btn-white{background:rgba(255,255,255,.15);color:white;border-color:rgba(255,255,255,.4)}
  .btn-white:hover{background:rgba(255,255,255,.28)}
  .btn-sm{padding:4px 10px;font-size:12px}
  .btn-primary{background:#185FA5;color:white;border-color:#185FA5}
  .btn-primary:hover{background:#0C447C}
  .btn-danger{color:#A32D2D;border-color:#F09595}
  .btn-danger:hover{background:#FCEBEB}
  .container{max-width:1200px;margin:0 auto;padding:20px 16px}
  .stats{display:flex;gap:12px;flex-wrap:wrap;margin-bottom:16px}
  .stat{background:white;border-radius:8px;padding:12px 20px;border:1px solid #e0e0e0;min-width:100px}
  .stat-label{font-size:11px;color:#666}
  .stat-value{font-size:24px;font-weight:600;color:#185FA5}
  .filters{display:flex;gap:8px;flex-wrap:wrap;margin-bottom:16px;align-items:center}
  .filters input,.filters select{font-size:13px;padding:8px 12px;border-radius:8px;border:1px solid #ccc;background:white;color:#1a1a1a;outline:none}
  .filters input{flex:1;min-width:200px}
  .filters input:focus{border-color:#185FA5;box-shadow:0 0 0 2px rgba(24,95,165,.15)}
  .grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(290px,1fr));gap:14px}
  .card{background:white;border:1px solid #e0e0e0;border-radius:12px;overflow:hidden}
  .card-head{padding:14px 16px;display:flex;align-items:flex-start;justify-content:space-between;gap:8px;border-bottom:1px solid #f0f0f0}
  .card-head-left{display:flex;align-items:center;gap:10px}
  .avatar{width:40px;height:40px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:13px;font-weight:600;flex-shrink:0}
  .card-name{font-weight:600;font-size:14px}
  .tag{display:inline-block;font-size:11px;padding:2px 8px;border-radius:4px;font-weight:500;margin-top:3px}
  .card-body{padding:12px 16px;display:flex;flex-direction:column;gap:8px}
  .field{display:flex;justify-content:space-between;align-items:flex-start;gap:8px}
  .field-label{font-size:11px;color:#777;white-space:nowrap;padding-top:1px;min-width:70px}
  .field-value{font-size:12px;text-align:right;word-break:break-all;font-family:'Courier New',monospace}
  .notes{font-size:11px;color:#666;padding:6px 16px 12px;font-style:italic;line-height:1.6;border-top:1px solid #f5f5f5}
  .card-footer{padding:10px 16px;border-top:1px solid #f0f0f0;display:flex;gap:6px;justify-content:flex-end}
  .empty-state,.loading-state{text-align:center;padding:3rem;color:#888;grid-column:1/-1;font-size:15px}
  .loading-state{color:#185FA5}
  .modal-overlay{display:none;position:fixed;inset:0;background:rgba(0,0,0,.5);z-index:200;align-items:center;justify-content:center;padding:1rem}
  .modal-overlay.open{display:flex}
  .modal{background:white;border-radius:12px;width:100%;max-width:520px;max-height:90vh;overflow-y:auto;padding:24px}
  .modal h3{font-size:17px;font-weight:600;margin-bottom:16px;color:#185FA5}
  .form-row{display:grid;grid-template-columns:1fr 1fr;gap:12px;margin-bottom:12px}
  .form-row.full{grid-template-columns:1fr}
  .fg{display:flex;flex-direction:column;gap:4px}
  .fg label{font-size:11px;font-weight:600;color:#555;text-transform:uppercase;letter-spacing:.04em}
  .fg input,.fg textarea{font-size:13px;padding:8px 10px;border-radius:8px;border:1px solid #ccc;background:white;color:#1a1a1a;width:100%;outline:none;font-family:Arial,sans-serif}
  .fg input:focus,.fg textarea:focus{border-color:#185FA5;box-shadow:0 0 0 2px rgba(24,95,165,.15)}
  .fg textarea{min-height:70px;resize:vertical}
  .sep{font-size:11px;font-weight:600;color:#185FA5;text-transform:uppercase;letter-spacing:.06em;margin:14px 0 8px;border-bottom:1px solid #e8f1fb;padding-bottom:4px}
  .modal-footer{display:flex;justify-content:flex-end;gap:8px;margin-top:16px;padding-top:14px;border-top:1px solid #eee}
  .toast{position:fixed;bottom:24px;right:24px;color:white;padding:10px 18px;border-radius:8px;font-size:13px;z-index:999;opacity:0;transition:opacity .3s;pointer-events:none;background:#1a5c2a}
  .toast.show{opacity:1}
  .toast.err{background:#A32D2D}
  .confirm-overlay{display:none;position:fixed;inset:0;background:rgba(0,0,0,.45);z-index:300;align-items:center;justify-content:center;padding:1rem}
  .confirm-overlay.open{display:flex}
  .confirm-box{background:white;border-radius:12px;padding:24px;max-width:360px;width:100%}
  .confirm-box h4{font-size:16px;margin-bottom:10px}
  .confirm-box p{font-size:13px;color:#555;margin-bottom:20px;line-height:1.5}
  .confirm-btns{display:flex;justify-content:flex-end;gap:8px}
</style>
</head>
<body>
<header>
  <div>
    <h1>&#127970; Cuentas Bancarias</h1>
    <p id="save-info"><span class="dot syncing" id="dot"></span><span id="sync-txt">Conectando con Google Drive...</span></p>
  </div>
  <div class="header-btns">
    <button class="btn btn-white" onclick="syncFromSheet()">&#8635; Actualizar</button>
    <button class="btn btn-white" onclick="openModal(null)">+ Nueva cuenta</button>
  </div>
</header>
<div class="container">
  <div class="stats" id="stats"></div>
  <div class="filters">
    <input type="text" id="q" placeholder="&#128269;  Buscar por nombre, banco, cuenta, email..." oninput="render()">
    <select id="fb" onchange="render()"><option value="">Todos los bancos</option></select>
    <select id="fp" onchange="render()"><option value="">Todas las personas</option></select>
    <button class="btn" onclick="clearFilters()">Limpiar</button>
  </div>
  <div class="grid" id="grid"><div class="loading-state">Cargando datos desde Google Drive...</div></div>
</div>
<div class="modal-overlay" id="modal-overlay" onclick="if(event.target===this)closeModal()">
  <div class="modal" id="modal-body"></div>
</div>
<div class="confirm-overlay" id="confirm-overlay">
  <div class="confirm-box">
    <h4 id="c-title"></h4><p id="c-msg"></p>
    <div class="confirm-btns">
      <button class="btn" onclick="closeConfirm()">Cancelar</button>
      <button class="btn btn-danger" id="c-ok">Confirmar</button>
    </div>
  </div>
</div>
<div class="toast" id="toast"></div>

<script>
const URL_SCRIPT = 'https://script.google.com/macros/s/AKfycbx_RLlnJhNxqm_ji9EIUk4KyQpwXC4l2YSicVUmQTjeQAeta21qYvwdWu1Stxw9cy6atQ/exec';
const HEADERS = ['id','banco','titular','usuario','contrasena','cuenta','pin','pinApp','email','notas'];
const COLORS = [
  {bg:'#E6F1FB',fg:'#185FA5'},{bg:'#E1F5EE',fg:'#0F6E56'},{bg:'#FAEEDA',fg:'#854F0B'},
  {bg:'#FBEAF0',fg:'#993556'},{bg:'#EEEDFE',fg:'#534AB7'},{bg:'#FAECE7',fg:'#993C1D'},
  {bg:'#EAF3DE',fg:'#3B6D11'},{bg:'#FCEBEB',fg:'#A32D2D'},{bg:'#F1EFE8',fg:'#5F5E5A'},
  {bg:'#dff5f0',fg:'#085041'},{bg:'#fff3cd',fg:'#633806'}
];
const ORIGINAL = [
  {id:1,banco:'GUAYAQUIL',titular:'TONY',usuario:'Tonitocuba1234',contrasena:'Tonito12',cuenta:'16783531',pin:'3333',pinApp:'7903',email:'antoniolopezfuentes1979@gmail.com',notas:''},
  {id:2,banco:'GUAYAQUIL',titular:'GUIDO',usuario:'GuidoGuillermo1973',contrasena:'Federacion12',cuenta:'0021290500',pin:'8888',pinApp:'',email:'',notas:'EXT DANIEL: 8888'},
  {id:3,banco:'GUAYAQUIL',titular:'LILY',usuario:'Melisa2014',contrasena:'Federacion12',cuenta:'0021291808',pin:'7777',pinApp:'',email:'',notas:'PIN CRED: 2222'},
  {id:4,banco:'GUAYAQUIL',titular:'YESSICA',usuario:'Yessica12',contrasena:'Guantanamo12',cuenta:'0012661644',pin:'2222',pinApp:'',email:'',notas:''},
  {id:5,banco:'GUAYAQUIL',titular:'WILMER',usuario:'Wilmerguaya23',contrasena:'Habana12',cuenta:'48815230',pin:'8888',pinApp:'',email:'wilmercuba43@gmail.com',notas:''},
  {id:6,banco:'GUAYAQUIL',titular:'LEO',usuario:'Leovecino12',contrasena:'Habana12',cuenta:'35522225',pin:'2222',pinApp:'',email:'leovecino58@gmail.com',notas:''},
  {id:7,banco:'GUAYAQUIL',titular:'NELSON',usuario:'Nelsonelorito23',contrasena:'Caballo12',cuenta:'23750495',pin:'9999',pinApp:'',email:'nh1386634@gmail.com',notas:''},
  {id:8,banco:'PRODUBANCO',titular:'LILY',usuario:'LilibethLopez',contrasena:'Habana12@',cuenta:'18059109526',pin:'6666',pinApp:'',email:'',notas:''},
  {id:9,banco:'PRODUBANCO',titular:'NELSON',usuario:'NELSON1234',contrasena:'Romero21',cuenta:'18059940209',pin:'',pinApp:'',email:'',notas:'Tarjeta: 4665'},
  {id:10,banco:'JEP',titular:'GUIDO',usuario:'GuidoGuillermo1973',contrasena:'Caballo12@',cuenta:'406068464907',pin:'8888',pinApp:'753357',email:'',notas:'La Isla, Nelson, Rojo, Barcelona, USA'},
  {id:11,banco:'JEP',titular:'LILY',usuario:'LilibethLopez1983',contrasena:'Habana12@',cuenta:'406068928008',pin:'8888',pinApp:'466546',email:'',notas:'Guantanamo, Guido, Principito, Avila, Huevo'},
  {id:12,banco:'JEP',titular:'TONY',usuario:'Guantanamo1979',contrasena:'Panzita3030@',cuenta:'406104662508',pin:'7777',pinApp:'',email:'',notas:''},
  {id:13,banco:'PACIFICO',titular:'LILY',usuario:'02melany',contrasena:'Revolucion12',cuenta:'1060852399',pin:'9999',pinApp:'',email:'',notas:'PIN BANCA TEL: 7315 | PIN CRED: 2020 | guantanamo, santa fe, chiquitico, victoria'},
  {id:14,banco:'PACIFICO',titular:'TONY',usuario:'anlopezf79',contrasena:'Guantanamo12',cuenta:'1065031253',pin:'7777',pinApp:'',email:'',notas:'Imagen: Colores | CAIMANERA GUANTANAMO CARNE | VILLENA NENE cod: dPc_H6MA3BFU'},
  {id:15,banco:'PACIFICO',titular:'GUIDO',usuario:'America2020',contrasena:'Guantanamo12',cuenta:'1057991839',pin:'8888',pinApp:'1111',email:'guidogpascual@gmail.com',notas:'PIN CRED: 4665 | Habana12'},
  {id:16,banco:'INTERNACIONAL',titular:'GUIDO',usuario:'GUIDO1973',contrasena:'Guantanamo12',cuenta:'2593207',pin:'7777',pinApp:'4665',email:'',notas:'IMAG: AVION | FRASE: CUBAVARADERO | HYUNDAI, DENIRO'},
  {id:17,banco:'BOLIVARIANO',titular:'GUIDO',usuario:'GuidoGuillermo19',contrasena:'Habana12@',cuenta:'1631186237',pin:'6666',pinApp:'1472',email:'',notas:'PIN CRED: 4665'},
  {id:18,banco:'MUTUALISTA PICHINCHA',titular:'GUIDO',usuario:'GuidoGuillermo1973',contrasena:'Guantanamo12',cuenta:'128053508',pin:'9999',pinApp:'',email:'',notas:'MUPIPASS: Cara21 | IMAGEN: AVION'},
  {id:19,banco:'LOJA',titular:'GUIDO',usuario:'guidopascual1973',contrasena:'Revolucion12',cuenta:'2902953611',pin:'2222',pinApp:'',email:'',notas:'comercial, carne, helado, cuca | IMAGEN: billete'},
  {id:20,banco:'PROCREDIT',titular:'GUIDO',usuario:'GuidoGuille1973@',contrasena:'Revolucion12#',cuenta:'019037879855',pin:'',pinApp:'',email:'',notas:'IMAGEN: TORRE PISA'},
  {id:21,banco:'AUSTRO',titular:'GUIDO',usuario:'Guantanamo20',contrasena:'GgPW09061973*',cuenta:'0017679457',pin:'9999',pinApp:'',email:'',notas:'PIN debito: 9999 | PIN credito: 4665'},
  {id:22,banco:'BANITSMO PANAMA',titular:'GUIDO',usuario:'guantanamo12',contrasena:'Habana12@',cuenta:'0119226521',pin:'9870',pinApp:'',email:'',notas:'Token: Camila | Frase: Cami | Imagen: Bebe'},
  {id:23,banco:'BANCO PICHINCHA',titular:'GUIDO',usuario:'America2020',contrasena:'Caballo21',cuenta:'2205398604',pin:'7777',pinApp:'25',email:'guidogpascual@gmail.com',notas:'EXT MARCO: 5555 | Habana12'},
  {id:24,banco:'BANCO PICHINCHA',titular:'LILY',usuario:'Lilibeth1973',contrasena:'Caballo21',cuenta:'2205623671',pin:'8888',pinApp:'52',email:'lilibethlopez8309@gmail.com',notas:'TARJ 4304 CTA 4532 PIN 2222 | Lily2409'},
  {id:25,banco:'BANCO PICHINCHA',titular:'ANABEL',usuario:'Caballo2019',contrasena:'Computadora11',cuenta:'2205659922',pin:'6666',pinApp:'30',email:'anabelalvarez631@gmail.com',notas:'Habana12'},
  {id:26,banco:'BANCO PICHINCHA',titular:'TERE',usuario:'Socialismo13',contrasena:'Guantanamo12',cuenta:'2205133030',pin:'7777',pinApp:'25',email:'tereelenapascual@gmail.com',notas:'EXT LUIS E: 6666 | Yessica7855'},
  {id:27,banco:'BANCO PICHINCHA',titular:'MARIA EUGENIA',usuario:'Mariaeug12',contrasena:'Habana12',cuenta:'2214991317',pin:'9999',pinApp:'45',email:'mariaeugeniacotorro@gmail.com',notas:'20-12-67 | connect miles | num: 292520062 | Habana12'},
  {id:28,banco:'BANCO PICHINCHA',titular:'TONY',usuario:'Guantanamo20',contrasena:'Caballo3030',cuenta:'2206258858',pin:'2222',pinApp:'37',email:'antoniolopezfuentes1979@gmail.com',notas:'PIN CRED: 4665 | Habana12'},
  {id:29,banco:'BANCO PICHINCHA',titular:'KIKI',usuario:'Kiki2020',contrasena:'Tamara3232',cuenta:'2205784650',pin:'7777',pinApp:'35',email:'juanenripola@gmail.com',notas:'Habana12'},
  {id:30,banco:'BANCO PICHINCHA',titular:'ELSIE',usuario:'elsieendry33',contrasena:'Guantanamo12',cuenta:'2212625241',pin:'9999',pinApp:'57',email:'elsieluisacastroromero@gmail.com',notas:'Habana12'},
  {id:31,banco:'BANCO PICHINCHA',titular:'JORGE',usuario:'Janypapa1',contrasena:'Habana12',cuenta:'2209776474',pin:'7777',pinApp:'35',email:'mereildeyadira@gmail.com',notas:'Habana12'},
  {id:32,banco:'BANCO PICHINCHA',titular:'NELSON',usuario:'Eloritonel12',contrasena:'Habana12',cuenta:'2210334309',pin:'9999',pinApp:'60',email:'nh1386634@gmail.com',notas:'Habana12'},
  {id:33,banco:'BANCO PICHINCHA',titular:'NOEMI',usuario:'Ramiro40',contrasena:'Federacion12',cuenta:'2205660313',pin:'2020',pinApp:'43',email:'noemialar328@gmail.com',notas:'Habana12'},
  {id:34,banco:'BANCO PICHINCHA',titular:'ANA',usuario:'LuisFrancisco08',contrasena:'Guantanamo12',cuenta:'2205952788',pin:'9999',pinApp:'28',email:'anaanabel673@gmail.com',notas:'Habana12'},
  {id:35,banco:'BANCO PICHINCHA',titular:'YOHANDI',usuario:'yohandi8643',contrasena:'Guantanamo12',cuenta:'2206175130',pin:'6666',pinApp:'51',email:'maikelinfrom@gmail.com',notas:'Habana12'},
  {id:36,banco:'BANCO PICHINCHA',titular:'ZOILA',usuario:'Caballo2140',contrasena:'Federacion12',cuenta:'2207032832',pin:'9999',pinApp:'73',email:'cobiellazoila@gmail.com',notas:'Habana12'},
  {id:37,banco:'BANCO PICHINCHA',titular:'LEO',usuario:'Leoveci123',contrasena:'Universidad13',cuenta:'2207370989',pin:'',pinApp:'49',email:'leovecino58@gmail.com',notas:'Habana12'},
  {id:38,banco:'SOLIDARIO',titular:'GUIDO',usuario:'GUIDITO1973',contrasena:'FEDERACION12',cuenta:'',pin:'565656',pinApp:'',email:'',notas:''},
];

const bankColor={};let ci=0;
function getColor(b){if(!bankColor[b]){bankColor[b]=COLORS[ci%COLORS.length];ci++;}return bankColor[b];}
function initials(n){return n.split(' ').slice(0,2).map(w=>w[0]||'').join('').toUpperCase();}

let db=[],nextId=1,showId={},confirmCb=null;

function setSync(status,txt){
  const d=document.getElementById('dot'),t=document.getElementById('sync-txt');
  d.className='dot'+(status==='syncing'?' syncing':status==='error'?' error':'');
  t.textContent=txt;
}
function toast(msg,err){
  const t=document.getElementById('toast');
  t.textContent=msg;t.className='toast show'+(err?' err':'');
  setTimeout(()=>t.className='toast',2800);
}
function showConfirm(title,msg,cb){
  document.getElementById('c-title').textContent=title;
  document.getElementById('c-msg').textContent=msg;
  confirmCb=cb;document.getElementById('confirm-overlay').classList.add('open');
}
function closeConfirm(){document.getElementById('confirm-overlay').classList.remove('open');confirmCb=null;}
document.getElementById('c-ok').onclick=()=>{if(confirmCb)confirmCb();closeConfirm();};

function rowsToDb(rows){
  if(!rows||rows.length<2)return[];
  return rows.slice(1).filter(r=>r[0]).map(r=>({
    id:Number(r[0]),banco:r[1]||'',titular:r[2]||'',usuario:r[3]||'',
    contrasena:r[4]||'',cuenta:r[5]||'',pin:r[6]||'',pinApp:r[7]||'',
    email:r[8]||'',notas:r[9]||''
  }));
}
function dbToRows(){
  return [HEADERS,...db.map(r=>HEADERS.map(h=>r[h]!==undefined?r[h]:''))];
}

async function syncFromSheet(){
  setSync('syncing','Cargando desde Google Drive...');
  try{
    const res=await fetch(URL_SCRIPT+'?action=read');
    const rows=await res.json();
    if(rows&&rows.length>1){
      db=rowsToDb(rows);
      nextId=db.length?Math.max(...db.map(r=>r.id))+1:1;
      setSync('ok','Sincronizado con Google Drive ✓');
    } else {
      db=JSON.parse(JSON.stringify(ORIGINAL));
      nextId=39;
      await saveToSheet();
    }
    render();
  }catch(e){
    setSync('error','Sin conexión — datos en memoria');
    if(!db.length){db=JSON.parse(JSON.stringify(ORIGINAL));nextId=39;render();}
    toast('No se pudo conectar a Google Drive',true);
  }
}

async function saveToSheet(){
  setSync('syncing','Guardando en Google Drive...');
  try{
    await fetch(URL_SCRIPT,{method:'POST',body:JSON.stringify({action:'write',rows:dbToRows()})});
    const now=new Date();
    const h=now.getHours().toString().padStart(2,'0'),m=now.getMinutes().toString().padStart(2,'0');
    setSync('ok','Guardado en Google Drive a las '+h+':'+m+' ✓');
  }catch(e){
    setSync('error','Error al guardar — revisa tu conexión');
    toast('Error al guardar en Google Drive',true);
  }
}

function filtered(){
  const q=(document.getElementById('q').value||'').toLowerCase();
  const fb=document.getElementById('fb').value,fp=document.getElementById('fp').value;
  return db.filter(r=>{
    if(fb&&r.banco!==fb)return false;
    if(fp&&r.titular!==fp)return false;
    if(q){const h=[r.banco,r.titular,r.usuario,r.cuenta,r.email,r.notas,r.pin].join(' ').toLowerCase();if(!h.includes(q))return false;}
    return true;
  });
}
function populateFilters(){
  const bancos=[...new Set(db.map(r=>r.banco))].sort();
  const personas=[...new Set(db.map(r=>r.titular))].sort();
  const fb=document.getElementById('fb'),fp=document.getElementById('fp');
  const fbv=fb.value,fpv=fp.value;
  fb.innerHTML='<option value="">Todos los bancos</option>'+bancos.map(b=>`<option value="${b}">${b}</option>`).join('');
  fp.innerHTML='<option value="">Todas las personas</option>'+personas.map(p=>`<option value="${p}">${p}</option>`).join('');
  fb.value=fbv;fp.value=fpv;
}
function renderStats(){
  const bancos=new Set(db.map(r=>r.banco)).size,personas=new Set(db.map(r=>r.titular)).size;
  document.getElementById('stats').innerHTML=`
    <div class="stat"><div class="stat-label">Total cuentas</div><div class="stat-value">${db.length}</div></div>
    <div class="stat"><div class="stat-label">Bancos</div><div class="stat-value">${bancos}</div></div>
    <div class="stat"><div class="stat-label">Personas</div><div class="stat-value">${personas}</div></div>`;
}
function render(){
  populateFilters();renderStats();
  const rows=filtered(),grid=document.getElementById('grid');
  if(!rows.length){grid.innerHTML='<div class="empty-state">No se encontraron resultados.</div>';return;}
  grid.innerHTML=rows.map(r=>{
    const c=getColor(r.banco),show=showId[r.id],mask=v=>v?'••••••••':'';
    return`<div class="card">
      <div class="card-head">
        <div class="card-head-left">
          <div class="avatar" style="background:${c.bg};color:${c.fg}">${initials(r.titular)}</div>
          <div><div class="card-name">${r.titular}</div><span class="tag" style="background:${c.bg};color:${c.fg}">${r.banco}</span></div>
        </div>
        <button class="btn btn-sm" onclick="toggleShow(${r.id})">${show?'&#128275; Ocultar':'&#128274; Ver'}</button>
      </div>
      <div class="card-body">
        ${r.usuario?`<div class="field"><span class="field-label">Usuario</span><span class="field-value">${show?r.usuario:mask(r.usuario)}</span></div>`:''}
        ${r.contrasena?`<div class="field"><span class="field-label">Contraseña</span><span class="field-value">${show?r.contrasena:'••••••••'}</span></div>`:''}
        ${r.cuenta?`<div class="field"><span class="field-label">N° cuenta</span><span class="field-value">${show?r.cuenta:mask(r.cuenta)}</span></div>`:''}
        ${r.pin?`<div class="field"><span class="field-label">PIN</span><span class="field-value">${show?r.pin:'••••'}</span></div>`:''}
        ${r.pinApp?`<div class="field"><span class="field-label">PIN App</span><span class="field-value">${show?r.pinApp:'••••'}</span></div>`:''}
        ${r.email?`<div class="field"><span class="field-label">Email</span><span class="field-value" style="font-size:11px;font-family:Arial">${r.email}</span></div>`:''}
      </div>
      ${r.notas?`<div class="notes">${r.notas}</div>`:''}
      <div class="card-footer">
        <button class="btn btn-sm" onclick="openModal(${r.id})">&#9998; Editar</button>
        <button class="btn btn-sm btn-danger" onclick="del(${r.id})">&#128465; Eliminar</button>
      </div>
    </div>`;
  }).join('');
}
function toggleShow(id){showId[id]=!showId[id];render();}
function clearFilters(){document.getElementById('q').value='';document.getElementById('fb').value='';document.getElementById('fp').value='';render();}

function del(id){
  const r=db.find(x=>x.id===id);if(!r)return;
  showConfirm('Eliminar cuenta','¿Eliminar la cuenta de '+r.titular+' en '+r.banco+'? No se puede deshacer.',async()=>{
    db=db.filter(x=>x.id!==id);render();await saveToSheet();toast('✓ Cuenta eliminada');
  });
}
function openModal(id){
  const r=id?db.find(x=>x.id===id):null,isNew=!r;
  const bancos=[...new Set(db.map(x=>x.banco))].sort();
  document.getElementById('modal-body').innerHTML=`
    <h3>${isNew?'&#10133; Nueva cuenta':'&#9998; Editar cuenta'}</h3>
    <div class="sep">Identificación</div>
    <div class="form-row">
      <div class="fg"><label>Banco *</label><input id="m-banco" list="bl" value="${r?r.banco:''}" placeholder="Banco"><datalist id="bl">${bancos.map(b=>`<option value="${b}">`).join('')}</datalist></div>
      <div class="fg"><label>Titular *</label><input id="m-titular" value="${r?r.titular:''}" placeholder="Nombre"></div>
    </div>
    <div class="sep">Acceso digital</div>
    <div class="form-row">
      <div class="fg"><label>Usuario</label><input id="m-usuario" value="${r?r.usuario:''}" placeholder="Usuario"></div>
      <div class="fg"><label>Contraseña</label><input id="m-contrasena" value="${r?r.contrasena:''}" placeholder="Contraseña"></div>
    </div>
    <div class="sep">Datos de cuenta</div>
    <div class="form-row">
      <div class="fg"><label>N° de cuenta</label><input id="m-cuenta" value="${r?r.cuenta:''}" placeholder="Número"></div>
      <div class="fg"><label>PIN tarjeta</label><input id="m-pin" value="${r?r.pin:''}" placeholder="PIN"></div>
    </div>
    <div class="form-row">
      <div class="fg"><label>PIN App / Token</label><input id="m-pinApp" value="${r?r.pinApp:''}" placeholder="PIN App"></div>
      <div class="fg"><label>Email asociado</label><input id="m-email" value="${r?r.email:''}" placeholder="Email"></div>
    </div>
    <div class="sep">Notas</div>
    <div class="form-row full"><div class="fg"><label>Notas adicionales</label><textarea id="m-notas">${r?r.notas:''}</textarea></div></div>
    <div class="modal-footer">
      <button class="btn" onclick="closeModal()">Cancelar</button>
      <button class="btn btn-primary" onclick="save(${id||'null'})">${isNew?'Agregar':'Guardar cambios'}</button>
    </div>`;
  document.getElementById('modal-overlay').classList.add('open');
}
function closeModal(){document.getElementById('modal-overlay').classList.remove('open');}
async function save(id){
  const banco=document.getElementById('m-banco').value.trim(),titular=document.getElementById('m-titular').value.trim();
  if(!banco||!titular){alert('Banco y titular son obligatorios');return;}
  const rec={banco,titular,usuario:document.getElementById('m-usuario').value.trim(),contrasena:document.getElementById('m-contrasena').value.trim(),cuenta:document.getElementById('m-cuenta').value.trim(),pin:document.getElementById('m-pin').value.trim(),pinApp:document.getElementById('m-pinApp').value.trim(),email:document.getElementById('m-email').value.trim(),notas:document.getElementById('m-notas').value.trim()};
  if(id){db[db.findIndex(x=>x.id===id)]={...rec,id};}else{db.push({...rec,id:nextId++});}
  closeModal();render();await saveToSheet();toast('✓ Guardado en Google Drive');
}
document.addEventListener('keydown',e=>{if(e.key==='Escape'){closeModal();closeConfirm();}});
syncFromSheet();
</script>
</body>
</html>
