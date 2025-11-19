<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Sistema Completo CLX</title>
<style>
 body{font-family:Arial;background:<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Login CLX</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #03040a;
      color: #fff;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
    }
    .login-box {
      background: #0d0f1a;
      padding: 30px;
      border-radius: 15px;
      width: 320px;
      box-shadow: 0 0 15px #00eaff;
    }
    input {
      width: 100%;
      padding: 10px;
      margin: 10px 0;
      border: none;
      border-radius: 5px;
    }
    button {
      width: 100%;
      padding: 12px;
      background: #00eaff;
      border: none;
      margin-top: 10px;
      border-radius: 5px;
      font-size: 16px;
      cursor: pointer;
    }
    /* Página organizador */
    #organizador {
      display: none;
      padding: 20px;
      color: #fff;
    }
    .gear {
      width: 40px;
      cursor: pointer;
      position: fixed;
      top: 20px;
      right: 20px;
      filter: drop-shadow(0 0 5px #00eaff);
    }
    .admin-box {
      display: none;
      position: fixed;
      top: 80px;
      right: 20px;
      background: #0d0f1a;
      padding: 20px;
      border-radius: 10px;
      width: 200px;
      box-shadow: 0 0 15px #00eaff;
    }
  </style>
</head>
<body>

<!-- LOGIN -->
<div class="login-box" id="loginTela">
  <h2>LOGIN</h2>
  <input type="text" id="user" placeholder="Usuário" />
  <input type="password" id="pass" placeholder="Senha" />
  <button onclick="logar()">Entrar</button>
</div>

<!-- ORGANIZADOR -->
<div id="organizador">
  <h1>Organizador CLX</h1>
  <img class="gear" src="https://cdn-icons-png.flaticon.com/512/3524/3524659.png" onclick="toggleAdmin()" />

  <div class="admin-box" id="adminPainel">
    <h3>Painel Admin</h3>
    <p>Configurações do sistema</p>
  </div>
</div>

<script>
function logar() {
  const user = document.getElementById("user").value;
  const pass = document.getElementById("pass").value;

  if (user === "CLX" && pass === "02072007") {
    document.getElementById("loginTela").style.display = "none";
    document.getElementById("organizador").style.display = "block";
  } else {
    alert("Usuário ou senha incorretos!");
  }
}

function toggleAdmin() {
  const box = document.getElementById("adminPainel");
  box.style.display = box.style.display === "block" ? "none" : "block";
}
</script>

</body>
</html>0d1117;color:#fff;margin:0;padding:0}
 .box{max-width:420px;margin:40px auto;background:#161b22;padding:20px;border-radius:10px}
 input,select,button{width:100%;padding:10px;margin:8px 0;border:none;border-radius:6px}
 button{background:#238636;color:#fff;font-weight:bold;cursor:pointer}
 button:hover{opacity:.8}
 .link{color:#58a6ff;cursor:pointer;text-decoration:underline;text-align:center}
 .hidden{display:none}
 table{width:100%;border-collapse:collapse;margin-top:20px}
 td,th{border:1px solid #333;padding:8px;text-align:left}
</style>
</head>
<body>

<div id="login" class="box">
 <h2>Login</h2>
 <input id="loginEmail" placeholder="Email">
 <input id="loginSenha" placeholder="Senha" type="password">
 <button onclick="logar()">Entrar</button>
 <p class="link" onclick="show('cadastro')">Criar conta</p>
 <p class="link" onclick="show('recuperar')">Esqueci a senha</p>
</div>

<div id="cadastro" class="box hidden">
 <h2>Criar Conta</h2>
 <input id="cadNome" placeholder="Nome">
 <input id="cadEmail" placeholder="Email">
 <input id="cadSenha" placeholder="Senha" type="password">
 <!-- Tipo de usuário fixado como comum -->
<input type="hidden" id="cadTipo" value="user">
 <button onclick="criarConta()">Cadastrar</button>
 <p class="link" onclick="show('login')">Voltar</p>
</div>

<div id="recuperar" class="box hidden">
 <h2>Recuperar Senha</h2>
 <input id="recEmail" placeholder="Digite seu email">
 <button onclick="recSenha()">Enviar</button>
 <p class="link" onclick="show('login')">Voltar</p>
</div>

<div id="painel" class="box hidden">
 <h2 id="tituloPainel">Painel</h2>
 <button onclick="show('cadColab')">Cadastrar Colaborador</button>
 <button onclick="show('lista')">Lista de Colaboradores</button>
 <button onclick="logout()">Sair</button>
</div>

<div id="cadColab" class="box hidden">
 <h2>Cadastrar Colaborador</h2>
 <input id="colabNome" placeholder="Nome">
 <input id="colabCargo" placeholder="Cargo">
 <button onclick="salvarColab()">Salvar</button>
 <p class="link" onclick="show('painel')">Voltar</p>
</div>

<div id="lista" class="box hidden">
 <h2>Colaboradores</h2>
 <table id="tabela"></table>
 <p class="link" onclick="show('painel')">Voltar</p>
</div>

<script>
let usuarios = JSON.parse(localStorage.getItem('usuarios')||'[]');

// Criar admin padrão se não existir
if(!usuarios.find(u=>u.email === "carlosfernandolorosa10@gmail.com")){
    usuarios.push({
        nome:"Administrador",
        email:"carlosfernandolorosa10@gmail.com",
        senha:"02072007",
        tipo:"admin"
    });
    localStorage.setItem('usuarios', JSON.stringify(usuarios));
}
let logado = JSON.parse(localStorage.getItem('logado')||'null');
let colaboradores = JSON.parse(localStorage.getItem('colabs')||'[]');

function show(x){
 document.querySelectorAll('.box').forEach(e=>e.classList.add('hidden'));
 document.getElementById(x).classList.remove('hidden');
}

if(logado) abrirPainel();

function salvarLS(){
 localStorage.setItem('usuarios',JSON.stringify(usuarios));
 localStorage.setItem('logado',JSON.stringify(logado));
 localStorage.setItem('colabs',JSON.stringify(colaboradores));
}

function criarConta(){
 let nome= cadNome.value;
 let email= cadEmail.value;
 let senha= cadSenha.value;
 let tipo = "user"; // sempre usuário comum
 if(!nome||!email||!senha) return alert('Preencha tudo');
 if(usuarios.find(u=>u.email===email)) return alert('Email já existe');
 usuarios.push({nome,email,senha,tipo});
 salvarLS();
 alert('Conta criada');
 show('login');
}

function logar(){
 let email= loginEmail.value;
 let senha= loginSenha.value;
 let u= usuarios.find(x=>x.email===email && x.senha===senha);
 if(!u) return alert('Login inválido');
 logado=u;
 salvarLS();
 abrirPainel();
}

function abrirPainel(){
 tituloPainel.innerText = logado.tipo==='admin' ? 'Painel do Administrador' : 'Painel do Usuário';
 show('painel');
}

function logout(){ logado=null; salvarLS(); show('login'); }

function recSenha(){
 let email= recEmail.value;
 let u= usuarios.find(x=>x.email===email);
 if(!u) return alert('Email não cadastrado');
 alert('Senha: '+u.senha);
 show('login');
}

function salvarColab(){
 let nome= colabNome.value;
 let cargo= colabCargo.value;
 if(!nome||!cargo) return alert('Preencha tudo');
 colaboradores.push({nome,cargo});
 salvarLS();
 alert('Colaborador salvo');
 show('painel');
}

function carregar(){
 let t= '<tr><th>Nome</th><th>Cargo</th></tr>';
 colaboradores.forEach(c=>{
   t+=`<tr><td>${c.nome}</td><td>${c.cargo}</td></tr>`;
 });
 tabela.innerHTML=t;
}

show('login');
</script>
<div id="adminUsuarios" class="box hidden">
 <h2>Gerenciar Usuários</h2>
 <table id="tabUsers"></table>
 <p class="link" onclick="show('painel')">Voltar</p>
</div>

<script>
// --- ADMIN FIXO ---
let adminEmail="carlosfernandolorosa10@gmail.com";
let adminSenha="02072007";
if(!usuarios.find(u=>u.email===adminEmail)){
 usuarios.push({nome:"Administrador",email:adminEmail,senha:adminSenha,tipo:"admin"});
 salvarLS();
}

// Adicionar botão ao painel admin
function abrirPainel(){
 tituloPainel.innerText = logado.tipo==='admin' ? 'Painel do Administrador' : 'Painel do Usuário';
 if(logado.tipo==='admin'){
  if(!document.getElementById('btnGerUsers')){
   let b=document.createElement('button');
   b.id='btnGerUsers';
   b.innerText='Gerenciar Usuários';
   b.onclick=()=>{carregarUsers();show('adminUsuarios');}
   document.getElementById('painel').appendChild(b);
  }
 }
 show('painel');
}

// --- GERENCIAR USUÁRIOS ---
function carregarUsers(){
 let h=`<tr><th>Nome</th><th>Email</th><th>Tipo</th><th>Ações</th></tr>`;
 usuarios.forEach((u,i)=>{
  h+=`<tr>
   <td>${u.nome}</td>
   <td>${u.email}</td>
   <td>${u.tipo}</td>
   <td>
    <button onclick="editarUser(${i})">Editar</button>
    <button onclick="resetSenha(${i})">Resetar Senha</button>
    ${u.email!==adminEmail?`<button onclick="toggleAdmin(${i})">${u.tipo==='admin'?'Remover Admin':'Dar Admin'}</button>`:''}
    ${u.email!==adminEmail?`<button onclick="deletarUser(${i})">Excluir</button>`:''}
   </td>
  </tr>`;
 });
 tabUsers.innerHTML=h;
}

function editarUser(i){
 let nome=prompt("Novo nome",usuarios[i].nome);
 if(!nome) return;
 usuarios[i].nome=nome;
 salvarLS();
 carregarUsers();
}

function resetSenha(i){
 let nova=prompt("Nova senha para esse usuário:");
 if(!nova) return;
 usuarios[i].senha=nova;
 salvarLS();
 alert("Senha redefinida!");
}

function toggleAdmin(i){
 usuarios[i].tipo = usuarios[i].tipo==='admin'?'user':'admin';
 salvarLS();
 carregarUsers();
}

function deletarUser(i){
 if(!confirm("Tem certeza?")) return;
 usuarios.splice(i,1);
 salvarLS();
 carregarUsers();
}
</script>
</body>
</html>
