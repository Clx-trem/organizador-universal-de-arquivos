<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>📊 Organizador Universal de Arquivos</title>
    <link rel="icon" href="https://public-frontend-cos.metadl.com/mgx/img/favicon.png" type="image/png">
    <script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/xlsx/dist/xlsx.full.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f5f5f5;
            color: #000;
            margin: 0;
            padding: 0;
        }
        .container {
            max-width: 1200px;
            margin: auto;
            padding: 20px;
        }
        header {
            text-align: center;
            margin-bottom: 20px;
        }
        .tabs {
            display: flex;
            justify-content: center;
            margin-bottom: 20px;
            flex-wrap: wrap;
        }
        .tab-button {
            background: #ddd;
            color: #000;
            border: none;
            padding: 10px 20px;
            cursor: pointer;
            margin: 5px;
            border-radius: 8px;
            transition: background 0.3s;
        }
        .tab-button:hover {
            background: #bbb;
        }
        .tab-button.active {
            background: #007bff;
            color: #fff;
        }
        .tab-button:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }
        .tab-content {
            display: none;
        }
        .tab-content.active {
            display: block;
        }
        .upload-box {
            border: 2px dashed #999;
            border-radius: 10px;
            padding: 30px;
            text-align: center;
            background-color: #fff;
        }
        .btn-primary, .btn-secondary {
            background: #007bff;
            border: none;
            padding: 10px 20px;
            border-radius: 6px;
            color: white;
            cursor: pointer;
            margin: 5px;
        }
        .btn-secondary {
            background: #555;
        }
        .categoria {
            background: #fff;
            padding: 10px;
            border-radius: 8px;
            margin-bottom: 10px;
            border: 1px solid #ccc;
        }
        .categoria-titulo {
            background: #eaeaea;
            padding: 8px;
            border-radius: 6px;
            cursor: pointer;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 10px;
        }
        th, td {
            border: 1px solid #aaa;
            padding: 6px;
            text-align: left;
        }
        input[type="file"] {
            display: none;
        }
        .upload-icon {
            font-size: 40px;
        }
        select, input[type="text"] {
            padding: 8px;
            border-radius: 6px;
            border: 1px solid #ccc;
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>📊 Organizador Universal de Arquivos</h1>
            <p>Carregue qualquer arquivo e organize pelos campos que desejar</p>
        </header>

        <!-- Navegação -->
        <nav class="tabs">
            <button class="tab-button active" onclick="mostrarAba('upload')">📁 Upload</button>
            <button class="tab-button" onclick="mostrarAba('organizacao')" id="tab-organizacao" disabled>⚙️ Organização</button>
            <button class="tab-button" onclick="mostrarAba('visualizacao')" id="tab-visualizacao" disabled>👁️ Visualização</button>
            <button class="tab-button" onclick="mostrarAba('exportacao')" id="tab-exportacao" disabled>💾 Exportação</button>
        </nav>

        <!-- Aba Upload -->
        <div id="aba-upload" class="tab-content active">
            <div class="upload-box">
                <input type="file" id="fileInput" accept=".csv,.xlsx,.xls,.json,.tsv,.txt">
                <label for="fileInput">
                    <div class="upload-icon">📎</div>
                    <h3>Clique para selecionar arquivo</h3>
                    <p>Suporta: CSV, Excel (.xlsx, .xls), JSON, TSV</p>
                </label>
            </div>
            <div id="file-info" style="display: none; margin-top: 15px;">
                <h3>Arquivo Carregado:</h3>
                <div id="file-details"></div>
                <button onclick="processarArquivo()" class="btn-primary">Processar Arquivo</button>
            </div>
        </div>

        <!-- Aba Organização -->
        <div id="aba-organizacao" class="tab-content">
            <h2>Escolha como organizar seus dados</h2>
            <label for="campo-organizacao">Organizar por:</label>
            <select id="campo-organizacao" onchange="atualizarPreview()">
                <option value="">Selecione um campo...</option>
            </select>
            <div id="preview-organizacao" style="margin-top: 15px;"></div>
            <button onclick="aplicarOrganizacao()" class="btn-primary" id="btn-aplicar" disabled>Aplicar Organização</button>
        </div>

        <!-- Aba Visualização -->
        <div id="aba-visualizacao" class="tab-content">
            <input type="text" id="busca-global" placeholder="Buscar..." oninput="buscarGlobal()" style="width:100%;padding:8px;margin-bottom:10px;">
            <button onclick="expandirTodas()" class="btn-secondary">Expandir Todas</button>
            <button onclick="recolherTodas()" class="btn-secondary">Recolher Todas</button>
            <div id="tabelas" style="margin-top:15px;"></div>
        </div>

        <!-- Aba Exportação -->
        <div id="aba-exportacao" class="tab-content">
            <h2>Exportar Dados Organizados</h2>
            <div>
                <button onclick="exportarExcel()" class="btn-primary">📊 Baixar Excel</button>
                <button onclick="exportarCSV()" class="btn-primary">📄 Baixar CSV</button>
                <button onclick="exportarJSON()" class="btn-primary">🔗 Baixar JSON</button>
            </div>
        </div>
    </div>

    <script>
        let dadosOriginais = [];
        let dadosOrganizados = {};
        let nomeArquivo = "";

        function mostrarAba(aba) {
            document.querySelectorAll(".tab-button").forEach(btn => btn.classList.remove("active"));
            document.querySelectorAll(".tab-content").forEach(tab => tab.classList.remove("active"));
            document.querySelector(`[onclick="mostrarAba('${aba}')"]`).classList.add("active");
            document.querySelector(`#aba-${aba}`).classList.add("active");
        }

        document.getElementById("fileInput").addEventListener("change", e => {
            const file = e.target.files[0];
            if (!file) return;
            nomeArquivo = file.name;
            document.getElementById("file-details").innerText = `Nome: ${file.name} (${(file.size / 1024).toFixed(1)} KB)`;
            document.getElementById("file-info").style.display = "block";
        });

        function processarArquivo() {
            const file = document.getElementById("fileInput").files[0];
            if (!file) return alert("Selecione um arquivo primeiro!");
            const extensao = file.name.split(".").pop().toLowerCase();

            if (["csv", "tsv", "txt"].includes(extensao)) {
                Papa.parse(file, {
                    header: true,
                    skipEmptyLines: true,
                    complete: results => {
                        dadosOriginais = results.data;
                        prepararOrganizacao();
                    }
                });
            } else if (["xlsx", "xls"].includes(extensao)) {
                const reader = new FileReader();
                reader.onload = e => {
                    const workbook = XLSX.read(e.target.result, { type: "binary" });
                    const sheet = workbook.Sheets[workbook.SheetNames[0]];
                    dadosOriginais = XLSX.utils.sheet_to_json(sheet);
                    prepararOrganizacao();
                };
                reader.readAsBinaryString(file);
            } else if (extensao === "json") {
                const reader = new FileReader();
                reader.onload = e => {
                    dadosOriginais = JSON.parse(e.target.result);
                    prepararOrganizacao();
                };
                reader.readAsText(file);
            } else {
                alert("Tipo de arquivo não suportado!");
            }
        }

        function prepararOrganizacao() {
            const campos = Object.keys(dadosOriginais[0]);
            const select = document.getElementById("campo-organizacao");
            select.innerHTML = '<option value="">Selecione um campo...</option>';
            campos.forEach(campo => {
                const opt = document.createElement("option");
                opt.value = campo;
                opt.textContent = campo;
                select.appendChild(opt);
            });
            document.getElementById("tab-organizacao").disabled = false;
            mostrarAba("organizacao");
        }

        function atualizarPreview() {
            const campo = document.getElementById("campo-organizacao").value;
            const previewDiv = document.getElementById("preview-organizacao");
            if (!campo) {
                previewDiv.innerHTML = "";
                document.getElementById("btn-aplicar").disabled = true;
                return;
            }
            const categorias = [...new Set(dadosOriginais.map(d => d[campo]))];
            previewDiv.innerHTML = `<h3>Encontradas ${categorias.length} categorias:</h3><ul>${categorias.map(c => `<li>${c}</li>`).join("")}</ul>`;
            document.getElementById("btn-aplicar").disabled = false;
        }

        function aplicarOrganizacao() {
            const campo = document.getElementById("campo-organizacao").value;
            dadosOrganizados = {};
            dadosOriginais.forEach(item => {
                const chave = item[campo] || "Sem categoria";
                if (!dadosOrganizados[chave]) dadosOrganizados[chave] = [];
                dadosOrganizados[chave].push(item);
            });
            document.getElementById("tab-visualizacao").disabled = false;
            document.getElementById("tab-exportacao").disabled = false;
            mostrarAba("visualizacao");
            renderizarVisualizacao();
        }

        function renderizarVisualizacao() {
            const container = document.getElementById("tabelas");
            container.innerHTML = "";
            Object.keys(dadosOrganizados).forEach(categoria => {
                const tabela = document.createElement("div");
                tabela.className = "categoria";
                const dados = dadosOrganizados[categoria];
                const colunas = Object.keys(dados[0]);
                const linhasHTML = dados.map(linha => `<tr>${colunas.map(c => `<td>${linha[c] ?? ""}</td>`).join("")}</tr>`).join("");
                tabela.innerHTML = `
                    <h3 class="categoria-titulo">${categoria} (${dados.length})</h3>
                    <table><thead><tr>${colunas.map(c => `<th>${c}</th>`).join("")}</tr></thead><tbody>${linhasHTML}</tbody></table>
                `;
                container.appendChild(tabela);
            });
        }

        function buscarGlobal() {
            const termo = document.getElementById("busca-global").value.toLowerCase();
            document.querySelectorAll(".categoria").forEach(cat => {
                cat.style.display = cat.innerText.toLowerCase().includes(termo) ? "" : "none";
            });
        }

        function expandirTodas() {
            document.querySelectorAll(".categoria table").forEach(tbl => tbl.style.display = "table");
        }

        function recolherTodas() {
            document.querySelectorAll(".categoria table").forEach(tbl => tbl.style.display = "none");
        }

        function exportarExcel() {
            const wb = XLSX.utils.book_new();
            Object.keys(dadosOrganizados).forEach(cat => {
                const ws = XLSX.utils.json_to_sheet(dadosOrganizados[cat]);
                XLSX.utils.book_append_sheet(wb, ws, cat.slice(0, 31));
            });
            XLSX.writeFile(wb, `${nomeArquivo.split('.')[0]}_organizado.xlsx`);
        }

        function exportarCSV() {
            const todos = Object.values(dadosOrganizados).flat();
            const csv = Papa.unparse(todos);
            const blob = new Blob([csv], { type: "text/csv;charset=utf-8;" });
            const url = URL.createObjectURL(blob);
            const a = document.createElement("a");
            a.href = url;
            a.download = `${nomeArquivo.split('.')[0]}_organizado.csv`;
            a.click();
        }

        function exportarJSON() {
            const blob = new Blob([JSON.stringify(dadosOrganizados, null, 2)], { type: "application/json" });
            const url = URL.createObjectURL(blob);
            const a = document.createElement("a");
            a.href = url;
            a.download = `${nomeArquivo.split('.')[0]}_organizado.json`;
            a.click();
        }
    </script>
</body>
</html>
