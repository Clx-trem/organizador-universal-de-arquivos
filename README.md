<html lang="pt-BR">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Organizador Universal + Login (CLX)</title>
  <script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/xlsx/dist/xlsx.full.min.js"></script>
  <style>
    :root{
      --neon: #00eaff;
      --bg:#03040a;
      --card:#08111a;
      --accent:#0ff;
      --glass: rgba(255,255,255,0.03);
    }
    *{box-sizing:border-box}
    body{
      margin:0;
      font-family:Inter,Arial,Helvetica,sans-serif;
      background: linear-gradient(180deg,var(--bg),#000);
      color:#e6f7ff;
      -webkit-font-smoothing:antialiased;
      -moz-osx-font-smoothing:grayscale;
      min-height:100vh;
    }
    /* LOGIN SCREEN */
    #login-screen{
      height:100vh;
      display:flex;
      align-items:center;
      justify-content:center;
      padding:20px;
    }
    .card{
      width:360px;
      background:linear-gradient(180deg,var(--card),#06101a);
      border-radius:12px;
      padding:24px;
      box-shadow:0 10px 50px rgba(0,234,255,0.06), inset 0 1px 0 rgba(255,255,255,0.02);
      border:1px solid rgba(0,234,255,0.06);
    }
    .card h2{ margin:6px 0 14px; color:var(--neon); text-shadow:0 0 8px rgba(0,234,255,0.08) }
    .field{ width:100%; margin-bottom:10px; display:block; }
    .field input{
      width:100%; padding:12px 10px; border-radius:8px; border:1px solid rgba(255,255,255,0.04);
      background:#07101a; color:#dffaff; outline:none;
    }
    .btn{
      display:inline-block; width:100%; padding:12px; border-radius:10px; border:none;
      background:var(--neon); color:#002028; font-weight:700; cursor:pointer;
      box-shadow:0 8px 30px rgba(0,234,255,0.06);
    }
    .muted{ color: rgba(230,247,255,0.65); font-size:13px; margin-top:8px; text-align:center; cursor:pointer }
    /* MAIN SCREEN */
    #main-screen{ display:none; min-height:100vh; }
    .topbar{
      display:flex; justify-content:space-between; align-items:center;
      padding:14px 22px; background: linear-gradient(90deg,#00121a,#001524);
      border-bottom:1px solid rgba(0,234,255,0.06);
    }
    .brand{
      display:flex; gap:12px; align-items:center;
    }
    .brand h1{ font-size:18px; margin:0; color:var(--neon) }
    .gear{
      background:transparent; border:none; font-size:26px; color:var(--neon); cursor:pointer;
      transition:transform .18s ease;
    }
    .gear:hover{ transform: rotate(14deg) scale(1.04); }
    .wrap{ max-width:1100px; margin:28px auto; padding:0 20px; }
    .upload-box{
      background: linear-gradient(180deg,#07121a,#06111a);
      border:1px solid rgba(0,234,255,0.04);
      padding:28px; border-radius:12px; display:flex; gap:20px; align-items:center;
      justify-content:space-between;
    }
    .upload-left{ display:flex; gap:18px; align-items:center; }
    .upload-icon{ font-size:40px; color:var(--neon) }
    .upload-info h3{ margin:0 0 6px; color:#dffaff }
    .file-input{
      display:inline-block; padding:10px 14px; border-radius:10px; background:rgba(255,255,255,0.03);
      border:1px dashed rgba(0,234,255,0.06); cursor:pointer; color:var(--neon);
    }
    .small{ font-size:13px; color:rgba(230,247,255,0.7) }
    .result{ margin-top:18px; padding:14px; border-radius:10px; background:var(--glass); color:#dffaff; }
    /* ADMIN PANEL modal */
    #admin-panel{
      display:none; position:fixed; inset:0; background:rgba(0,0,0,0.6); align-items:center; justify-content:center;
      z-index:9999;
    }
    .admin-card{
      width:640px; background:linear-gradient(180deg,#07101a,#061018); padding:18px; border-radius:12px;
      border:1px solid rgba(0,234,255,0.06);
    }
    .admin-head{ display:flex; justify-content:space-between; align-items:center; gap:12px; }
    .admin-head h3{ margin:0; color:var(--neon) }
    .close { background:#ff6b6b; color:#fff; border:none; padding:8px 10px; border-radius:8px; cursor:pointer }
    .user-list{ margin-top:12px; max-height:260px; overflow:auto; }
    .user-row{ display:flex; justify-content:space-between; align-items:center; gap:10px; padding:8px; background:#08111a; border-radius:8px; margin-bottom:8px; border:1px solid rgba(255,255,255,0.02) }
    .user-row b{ color:#dffaff }
    .user-row button{ padding:6px 8px; border-radius:8px; border:none; cursor:pointer; background:var(--neon); color:#002028; font-weight:700 }
    /* small responsive */
    @media (max-width:720px){
      .upload-box{ flex-direction:column; align-items:stretch }
      .admin-card{ width:92% }
    }
  </style>
</head>
<body>

  <!-- LOGIN -->
  <section id="login-screen">
    <div class="card" role="dialog" aria-labelledby="login-title">
      <h2 id="login-title">🔐 Acesse sua conta</h2>
      <label class="field"><input id="inputUser" placeholder="Usuário (ex: CLX)" autocomplete="username"></label>
      <label class="field"><input id="inputPass" type="password" placeholder="Senha" autocomplete="current-password"></label>
      <button class="btn" onclick="handleLogin()">Entrar</button>
      <div style="display:flex;gap:8px;margin-top:10px;justify-content:center">
        <div class="muted" onclick="preencherAdmin()">Usar credenciais Admin</div>
        <div class="muted" onclick="limparLocal()">Reset DB (avançado)</div>
      </div>
      <p style="font-size:13px;margin-top:12px;color:rgba(230,247,255,0.65);text-align:center">
        Usuário admin padrão: <b>CLX</b> — senha: <b>02072007</b>
      </p>
    </div>
  </section>

  <!-- MAIN -->
  <main id="main-screen" aria-live="polite">
    <div class="topbar" role="banner">
      <div class="brand">
        <div style="width:44px;height:44px;border-radius:10px;background:linear-gradient(45deg,#00222a,#00303a);display:flex;align-items:center;justify-content:center;margin-right:8px;box-shadow:0 6px 18px rgba(0,234,255,0.04)">
          <span style="font-size:20px;color:var(--neon)">CLX</span>
        </div>
        <div>
          <h1 class="topbar-title">📊 Organizador Universal</h1>
          <div style="font-size:12px;color:rgba(230,247,255,0.6)">Faça upload e organize seus arquivos</div>
        </div>
      </div>
      <div style="display:flex;gap:12px;align-items:center">
        <button id="gearBtn" class="gear" title="Painel Admin" onclick="openAdmin()" style="display:none">⚙️</button>
        <button class="btn" onclick="doLogout()" style="background:transparent;border:1px solid rgba(255,255,255,0.04);color:var(--neon)">Sair</button>
      </div>
    </div>
    <div class="wrap">
      <div class="upload-box" role="region" aria-label="Upload">
        <div class="upload-left">
          <div class="upload-icon">📎</div>
          <div class="upload-info">
            <h3>Selecione um arquivo</h3>
            <div class="small">Suporta CSV, Excel (.xlsx/.xls), JSON, TSV, TXT</div>
          </div>
        </div>
        <div style="display:flex;gap:12px;align-items:center">
          <label class="file-input">
            <input id="fileInput" type="file" accept=".csv,.xlsx,.xls,.json,.txt,.tsv" style="display:none" />
            Escolher arquivo
          </label>
          <button class="btn" onclick="handleProcess()">Processar</button>
        </div>
      </div>
      <div id="resultado" class="result" style="display:none"></div>
    </div>
  </main>

  <!-- ADMIN PANEL -->
  <div id="admin-panel" role="dialog" aria-modal="true">
    <div class="admin-card">
      <div class="admin-head">
        <h3>⚙️ Painel do Administrador</h3>
        <div>
          <button class="close" onclick="closeAdmin()">Fechar</button>
        </div>
      </div>
      <p style="margin-top:8px;color:rgba(230,247,255,0.7)">Gerencie usuários. Apenas <strong>CLX</strong> pode abrir este painel.</p>
      <hr style="border-color:rgba(255,255,255,0.02);margin:12px 0">
      <strong>Usuários cadastrados</strong>
      <div class="user-list" id="userList"></div>
      <hr style="border-color:rgba(255,255,255,0.02);margin:12px 0">
      <strong>Criar novo usuário</strong>
      <div style="display:flex;gap:8px;margin-top:8px">
        <input id="newUser" class="input-dark" placeholder="nome de usuário">
        <input id="newPass" class="input-dark" placeholder="senha">
        <button class="btn" onclick="createUser()">Criar</button>
      </div>
      <div style="margin-top:12px;display:flex;gap:8px">
        <button class="btn" onclick="backupDB()">Fazer backup do DB</button>
        <button class="btn" onclick="exportDB()">Exportar JSON</button>
        <button class="btn" onclick="askDeleteDB()" style="background:#ff6b6b;color:#fff">Excluir DB</button>
      </div>
    </div>
  </div>

<script>
/* ---------- Inicialização do banco local ---------- */
(function initDB(){
  try {
    const raw = localStorage.getItem('usuarios');
    let users = raw ? JSON.parse(raw) : null;
    if (!users || typeof users !== 'object') users = {};
    // garante admin CLX com senha 02072007 sempre presente (não sobrescreve outros existentes)
    if (!users['CLX']) users['CLX'] = '02072007';
    localStorage.setItem('usuarios', JSON.stringify(users));
    console.log('DB usuarios inicializado', users);
  } catch(e) {
    console.error('Erro inicializando DB', e);
    localStorage.setItem('usuarios', JSON.stringify({ 'CLX': '02072007' }));
  }
})();

/* ---------- Funções de login ---------- */
function handleLogin(){
  const u = (document.getElementById('inputUser').value || '').trim();
  const p = (document.getElementById('inputPass').value || '');
  if(!u || !p){ alert('Preencha usuário e senha'); return; }

  let users = JSON.parse(localStorage.getItem('usuarios') || '{}');
  if(!users[u]) {
    alert('Usuário não existe!');
    return;
  }
  if(users[u] !== p){
    alert('Senha incorreta!');
    return;
  }

  // login OK
  localStorage.setItem('logado', u);
  showMainFor(u);
}

function preencherAdmin(){
  document.getElementById('inputUser').value = 'CLX';
  document.getElementById('inputPass').value = '02072007';
}

function showMainFor(user){
  // oculta login, mostra main
  document.getElementById('login-screen').style.display = 'none';
  document.getElementById('main-screen').style.display = 'block';
  // mostra engrenagem somente se admin
  document.getElementById('gearBtn').style.display = user === 'CLX' ? 'inline-block' : 'none';
  // limpa campos
  document.getElementById('inputPass').value = '';
  document.getElementById('inputUser').value = '';
  // informar usuário atual
  console.log('Logado como', user);
}

/* ---------- Logout ---------- */
function doLogout(){
  if(confirm('Deseja sair?')) {
    localStorage.removeItem('logado');
    document.getElementById('main-screen').style.display = 'none';
    document.getElementById('login-screen').style.display = 'flex';
    document.getElementById('resultado').style.display = 'none';
  }
}

/* ---------- Admin panel ---------- */
function openAdmin(){
  const current = localStorage.getItem('logado');
  if(current !== 'CLX'){ alert('Apenas ADMIN (CLX) pode acessar o painel'); return; }
  populateUserList();
  document.getElementById('admin-panel').style.display = 'flex';
}
function closeAdmin(){ document.getElementById('admin-panel').style.display = 'none'; }

function populateUserList(){
  const users = JSON.parse(localStorage.getItem('usuarios') || '{}');
  const cont = document.getElementById('userList');
  cont.innerHTML = '';
  Object.keys(users).sort().forEach(u=>{
    const row = document.createElement('div');
    row.className = 'user-row';
    row.innerHTML = `
      <div>
        <b>${u}</b><div style="font-size:12px;color:rgba(230,247,255,0.6)">senha: ${'*'.repeat((users[u]||'').length)}</div>
      </div>
      <div style="display:flex;gap:8px">
        <button onclick="promptChange('${u}')">Trocar senha</button>
        ${u === 'CLX' ? '<button onclick="cannotDelete()">🔒</button>' : `<button onclick="deleteUser('${u}')">Excluir</button>`}
      </div>
    `;
    cont.appendChild(row);
  });
}

function cannotDelete(){ alert('Admin principal CLX não pode ser deletado.'); }

function createUser(){
  const u = (document.getElementById('newUser').value || '').trim();
  const p = (document.getElementById('newPass').value || '');
  if(!u || !p){ alert('Preencha usuário e senha'); return; }
  if(u.length < 3 || p.length < 3){ alert('Usuário/senha muito curta'); return; }
  const users = JSON.parse(localStorage.getItem('usuarios') || '{}');
  if(users[u]){ alert('Usuário já existe'); return; }
  users[u] = p;
  localStorage.setItem('usuarios', JSON.stringify(users));
  document.getElementById('newUser').value=''; document.getElementById('newPass').value='';
  populateUserList();
  alert('Usuário criado!');
}

function promptChange(u){
  const novo = prompt(`Digite a nova senha para ${u} (min 3 chars)`);
  if(novo === null) return; // cancel
  if(typeof novo !== 'string' || novo.length < 3) return alert('Senha inválida');
  const users = JSON.parse(localStorage.getItem('usuarios') || '{}');
  users[u] = novo;
  localStorage.setItem('usuarios', JSON.stringify(users));
  populateUserList();
  alert('Senha alterada!');
}

function deleteUser(u){
  if(!confirm(`Excluir usuário ${u}? Isso NÃO poderá ser desfeito.`)) return;
  const users = JSON.parse(localStorage.getItem('usuarios') || '{}');
  if(u === 'CLX'){ alert('Não é permitido excluir o admin CLX'); return; }
  delete users[u];
  localStorage.setItem('usuarios', JSON.stringify(users));
  populateUserList();
  alert('Usuário excluído');
}

/* proteção anti-deletar banco: pedir confirmação forte */
function askDeleteDB(){
  const confirm1 = prompt('Para confirmar exclusão do DB digite: DELETE DB');
  if(confirm1 !== 'DELETE DB') return alert('Operação cancelada');
  localStorage.removeItem('usuarios');
  localStorage.removeItem('logado');
  alert('Banco de usuários excluído. Recarregue a página.');
}

/* backups / export */
function backupDB(){
  const users = JSON.parse(localStorage.getItem('usuarios') || '{}');
  const blob = new Blob([JSON.stringify(users,null,2)], {type:'application/json'});
  const a = document.createElement('a');
  a.href = URL.createObjectURL(blob);
  a.download = 'usuarios_backup.json';
  a.click();
}
function exportDB(){ backupDB(); }

/* reset local (avançado) - util para dev */
function limparLocal(){
  if(!confirm('Limpar todo DB local (usuários)?')) return;
  localStorage.removeItem('usuarios');
  localStorage.removeItem('logado');
  // re-inicializa com admin
  localStorage.setItem('usuarios', JSON.stringify({'CLX':'02072007'}));
  alert('DB resetado. Admin CLX criado. Recarregue a página.');
}

/* ---------- Processamento de arquivos (básico) ---------- */
function handleProcess(){
  const f = document.getElementById('fileInput').files[0];
  if(!f) return alert('Selecione um arquivo');
  const name = f.name;
  const ext = name.split('.').pop().toLowerCase();

  if(['csv','tsv','txt'].includes(ext)){
    Papa.parse(f, {
      header:true,
      skipEmptyLines:true,
      complete: (res) => showResult(name, res.data)
    });
    return;
  }
  if(['xlsx','xls'].includes(ext)){
    const reader = new FileReader();
    reader.onload = (e) => {
      const wb = XLSX.read(e.target.result, {type:'binary'});
      const sheet = wb.Sheets[wb.SheetNames[0]];
      const data = XLSX.utils.sheet_to_json(sheet);
      showResult(name, data);
    };
    reader.readAsBinaryString(f);
    return;
  }
  if(ext === 'json'){
    const reader = new FileReader();
    reader.onload = (e) => {
      let data = [];
      try{ data = JSON.parse(e.target.result); }catch(err){ return alert('JSON inválido'); }
      showResult(name, data);
    };
    reader.readAsText(f);
    return;
  }

  alert('Tipo de arquivo não suportado');
}

function showResult(name, data){
  const out = document.getElementById('resultado');
  out.style.display = 'block';
  out.innerHTML = `<h3 style="color:var(--neon)">Arquivo: ${name}</h3>
    <div style="font-size:13px;color:rgba(230,247,255,0.8);margin-top:8px">Registros: ${Array.isArray(data)?data.length:0}</div>
    <pre style="max-height:260px;overflow:auto;background:#06111a;border-radius:8px;padding:10px;margin-top:10px;border:1px solid rgba(255,255,255,0.02)">${escapeHtml(JSON.stringify(data.slice(0,30), null, 2))}${data.length>30?'\n\n... (mostrando 30 primeiros registros)':''}</pre>
  `;
}
/* small helper */
function escapeHtml(str){ return String(str).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;'); }
/* ---------- Auto-login if already logged ---------- */
(function tryAutoLogin(){
  const logged = localStorage.getItem('logado');
  if(logged){
    showMainFor(logged);
  }
})();
</script>
</body>
</html>
