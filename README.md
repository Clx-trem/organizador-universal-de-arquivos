<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Sistema Completo CLX</title>
<style>
 body{font-family:Arial;background:#0d1117;color:#fff;margin:0;padding:0}
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
 <select id="cadTipo">
   <option value="user">Usuário</option>
   <option value="admin">Administrador</option>
 </select>
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
 let tipo= cadTipo.value;
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
</body>
</html>
