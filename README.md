<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DON HERMANO - Gestão e Performance</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    
    <script src="https://www.gstatic.com/firebasejs/9.17.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.17.1/firebase-database-compat.js"></script>

    <style>
        :root { 
            --dark: #000000; --primary: #6a1b9a; --accent: #9c27b0;  
            --light: #f3e5f5; --white: #ffffff; --danger: #ff5252; --success: #2e7d32; --info: #0288d1; --warning: #f57c00;
        }
        
        body { 
            font-family: 'Segoe UI', sans-serif; 
            background: var(--light); margin: 0; padding: 0;
            background-image: url("https://i.ibb.co/ZzHKN3wR/DON-HERMANO.jpg"); background-attachment: fixed; background-position: center;
            background-repeat: no-repeat; background-size: 20%; background-color: rgba(243, 229, 245, 0.96);
            background-blend-mode: overlay;
        }

        #login-screen {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: var(--dark); display: flex; align-items: center; justify-content: center; z-index: 9999;
        }
        .login-box {
            background: var(--white); padding: 40px; border-radius: 15px; width: 100%; max-width: 350px;
            text-align: center; box-shadow: 0 10px 25px rgba(0,0,0,0.5);
        }
        .login-box img { width: 120px; border-radius: 50%; margin-bottom: 20px; border: 3px solid var(--primary); }
        .login-box input { width: 100%; padding: 12px; margin-bottom: 10px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        .login-box button { background: var(--primary); color: white; border: none; padding: 12px; width: 100%; border-radius: 8px; font-weight: bold; cursor: pointer; }

        #main-system { display: none; }
        
        header { background: var(--dark); color: var(--white); padding: 20px 10px; text-align: center; border-bottom: 4px solid var(--primary); }
        .logo-header { height: 60px; border-radius: 50%; border: 2px solid var(--primary); margin-bottom: 10px; }

        nav { background: #111; padding: 5px; display: flex; justify-content: center; gap: 5px; position: sticky; top: 0; z-index: 100; overflow-x: auto; }
        nav button { background: #222; border: 1px solid #333; color: #ccc; padding: 12px 10px; cursor: pointer; font-weight: bold; border-radius: 4px; white-space: nowrap; flex: 1; font-size: 0.75em; }
        nav button.active { background: var(--primary); color: white; }

        .container { max-width: 1100px; margin: 20px auto; padding: 20px; background: rgba(255, 255, 255, 0.98); border-radius: 12px; }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
        
        .form-section { background: #fff; padding: 20px; border-radius: 8px; border-left: 5px solid var(--primary); margin-bottom: 25px; box-shadow: 0 2px 10px rgba(0,0,0,0.05); }
        .form-group { display: grid; grid-template-columns: repeat(auto-fit, minmax(130px, 1fr)); gap: 10px; align-items: end; }
        input, select, button, textarea { width: 100%; padding: 10px; border: 1px solid #ddd; border-radius: 6px; box-sizing: border-box; }
        
        .btn-del { background: var(--danger); color: white; border: none; padding: 5px 10px; border-radius: 4px; cursor: pointer; font-weight: bold; }

        table { width: 100%; border-collapse: collapse; margin-top: 15px; }
        th { background: var(--primary); color: white; padding: 10px; text-align: left; font-size: 13px; }
        td { padding: 10px; border-bottom: 1px solid #eee; font-size: 13px; }

        .dashboard-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 10px; margin-bottom: 20px; }
        .card { background:#fff; padding:15px; border-radius:8px; text-align:center; border: 1px solid #ddd; }
        .card h4 { margin: 0 0 10px 0; font-size: 11px; color: #666; text-transform: uppercase; }
        .card p { margin: 0; font-size: 18px; font-weight: bold; }

        .charts-row { display: grid; grid-template-columns: repeat(auto-fit, minmax(400px, 1fr)); gap: 20px; margin-top: 20px; }
        .chart-container { background: #fff; padding: 15px; border-radius: 8px; border: 1px solid #ddd; height: 300px; }

        #area-holerite { padding: 30px; background: white; color: #000; border: 1px solid #eee; }
        .pdf-header { text-align: center; border-bottom: 2px solid #000; padding-bottom: 15px; margin-bottom: 20px; }
        .pdf-logo-img { width: 120px; height: 120px; border-radius: 50%; border: 2px solid #000; margin-bottom: 10px; }
        .pdf-table { width: 100%; border-collapse: collapse; margin: 20px 0; }
        .pdf-table th { background: #f0f0f0 !important; border: 1px solid #000; padding: 8px; color: black; }
        .pdf-table td { border: 1px solid #000; padding: 8px; }

        @media print { .no-print { display: none !important; } }
    </style>
</head>
<body>

<div id="login-screen">
    <div class="login-box">
        <img src="https://i.ibb.co/ZzHKN3wR/DON-HERMANO.jpg">
        <h3>Acesso Don Hermano</h3>
        <input type="text" id="user" placeholder="Usuário">
        <input type="password" id="pass" placeholder="Senha">
        <button onclick="checkLogin()">ENTRAR</button>
    </div>
</div>

<div id="main-system">
    <header>
        <img src="https://i.ibb.co/ZzHKN3wR/DON-HERMANO.jpg" class="logo-header">
        <h2 style="margin:0;">CORTE DON HERMANO</h2>
    </header>

    <nav class="no-print">
        <button onclick="showTab('vendas')" class="nav-btn active">✂️ VENDAS</button>
        <button onclick="showTab('despesas')" class="nav-btn">💸 GASTOS</button>
        <button onclick="showTab('config_equipe')" class="nav-btn">👤 EQUIPE</button>
        <button onclick="showTab('config_servicos')" class="nav-btn">💰 SERVIÇOS</button>
        <button onclick="showTab('relatorio')" class="nav-btn">📊 DASHBOARD</button>
        <button onclick="showTab('holerite')" class="nav-btn">📑 COMISSÃO</button>
        <button onclick="showTab('pagamentos_notas')" class="nav-btn">💳 PAGAMENTOS</button>
        <button onclick="showTab('atendimentos_anteriores')" class="nav-btn">⏳ ANTERIORES</button>
    </nav>

    <div class="container">
        <div id="vendas" class="tab-content active">
             <div class="form-section no-print">
                <h3>Novo Atendimento</h3>
                <div class="form-group">
                    <div><label>Data</label><input type="date" id="v_data"></div>
                    <div><label>Serviço</label><select id="v_servico"></select></div>
                    <div><label>Barbeiro</label><select id="v_barbeiro"></select></div>
                    <div><label>Qtd</label><input type="number" id="v_qtd" value="1"></div>
                    <button onclick="registrarVenda()" style="background: var(--primary); color: white;">SALVAR</button>
                </div>
            </div>
            <table><thead><tr><th>Data</th><th>Serviço</th><th>Barbeiro</th><th>Qtd</th><th>Total</th><th>Ação</th></tr></thead><tbody id="lista_vendas"></tbody></table>
        </div>

        <div id="despesas" class="tab-content">
            <div class="form-section no-print">
                <h3>Lançar Gasto Loja</h3>
                <div class="form-group">
                    <div><label>Data</label><input type="date" id="d_data"></div>
                    <div><label>Descrição</label><input type="text" id="d_desc"></div>
                    <div><label>Valor</label><input type="number" id="d_valor"></div>
                    <button onclick="registrarDespesa()" style="background:var(--danger); color:white;">SALVAR</button>
                </div>
            </div>
            <table><thead><tr><th>Data</th><th>Descrição</th><th>Valor</th><th>Ação</th></tr></thead><tbody id="lista_despesas"></tbody></table>
        </div>

        <div id="config_equipe" class="tab-content">
            <div class="form-section"><h3>Equipe</h3><div class="form-group"><input type="text" id="b_nome" placeholder="Nome"><button onclick="addBarbeiro()">CADASTRAR</button></div></div>
            <table><thead><tr><th>Nome</th><th>Ação</th></tr></thead><tbody id="lista_barbeiros_ui"></tbody></table>
        </div>

        <div id="config_servicos" class="tab-content">
            <div class="form-section"><h3>Serviços</h3><div class="form-group"><input type="text" id="s_nome" placeholder="Serviço"><input type="number" id="s_preco" placeholder="Preço"><button onclick="addServico()">CADASTRAR</button></div></div>
            <table><thead><tr><th>Serviço</th><th>Preço</th><th>Ação</th></tr></thead><tbody id="lista_servicos_ui"></tbody></table>
        </div>

        <div id="relatorio" class="tab-content">
            <div class="form-section">
                <h3>Dashboard de Performance</h3>
                <div class="form-group">
                    <div><label>Início</label><input type="date" id="rel_ini"></div>
                    <div><label>Fim</label><input type="date" id="rel_fim"></div>
                    <button onclick="renderRelatorio()" style="background:var(--primary); color:white;">FILTRAR</button>
                </div>
            </div>
            <div class="dashboard-grid">
                <div class="card"><h4>Bruto Período</h4><p id="res_bruto" style="color:var(--primary)">R$ 0.00</p></div>
                <div class="card"><h4>💸 Gastos Totais</h4><p id="res_gastos" style="color:var(--danger)">R$ 0.00</p></div>
                <div class="card" style="border: 2px solid var(--success); background: #f1f8e9;">
                    <h4>🚀 Lucro Casa (Bruto - Gastos)</h4>
                    <p id="res_lucro_real" style="color:var(--success)">R$ 0.00</p>
                </div>
            </div>

            <div class="charts-row">
                <div class="chart-container">
                    <h4 style="text-align:center; font-size: 11px; margin-bottom: 5px;">PRODUÇÃO POR BARBEIRO (R$)</h4>
                    <canvas id="chartFaturamento"></canvas>
                </div>
                <div class="chart-container">
                    <h4 style="text-align:center; font-size: 11px; margin-bottom: 5px;">QUANTIDADE DE ATENDIMENTOS</h4>
                    <canvas id="chartQtd"></canvas>
                </div>
            </div>

            <h3 style="margin-top: 30px;">Tabela Comparativa de Atendimentos</h3>
            <table>
                <thead><tr id="head_comparativo"><th>Barbeiro</th></tr></thead>
                <tbody id="body_comparativo"></tbody>
            </table>
        </div>

        <div id="holerite" class="tab-content">
            <div class="form-section no-print">
                <h3>Gerar Relatório de Comissão</h3>
                <div class="form-group">
                    <div><label>Barbeiro</label><select id="sel_barbeiro_h"></select></div>
                    <div><label>Comissão %</label><input type="number" id="h_porcentagem" value="50"></div>
                    <div><label>Início</label><input type="date" id="h_ini"></div>
                    <div><label>Fim</label><input type="date" id="h_fim"></div>
                </div>
                <div class="form-group" style="margin-top: 15px;">
                    <div><label>Caixinha (R$)</label><input type="number" id="h_caixinha" value="0"></div>
                    <div><label>Venda Produtos (R$)</label><input type="number" id="h_produtos" value="0"></div>
                    <div style="grid-column: span 2;"><label>Comissão (Texto)</label><textarea id="h_obs" rows="1"></textarea></div>
                    <button onclick="gerarHolerite()" style="background: var(--primary); color: white;">GERAR</button>
                    <button onclick="baixarPDF()" style="background: var(--success); color: white;">PDF</button>
                </div>
            </div>

            <div id="area-holerite">
                <div class="pdf-header">
                    <img src="https://i.ibb.co/ZzHKN3wR/DON-HERMANO.jpg" class="pdf-logo-img" crossOrigin="anonymous">
                    <h1>COMISSÃO</h1>
                    <p style="font-weight: bold; font-size: 20px;">DON HERMANO BARBEARIA</p>
                </div>
                <p><strong>PROFISSIONAL:</strong> <span id="h_nome_res">---</span></p>
                <p><strong>PERÍODO:</strong> <span id="h_periodo_res">---</span></p>
                <table class="pdf-table">
                    <thead><tr><th>DESCRIÇÃO</th><th align="center">QTD</th><th align="right">BRUTO</th></tr></thead>
                    <tbody id="h_corpo_tabela"></tbody>
                </table>
                <div id="h_obs_container" style="margin-bottom: 10px; font-style: italic; display:none;">
                    <strong>OBS:</strong> <span id="h_obs_res"></span>
                </div>
                <div style="border: 2px solid #000; padding: 15px;">
                    <table style="width: 100%;">
                        <tr><td>Subtotal Bruto Serviços:</td><td align="right"><span id="h_bruto_servicos">R$ 0,00</span></td></tr>
                        <tr><td>Comissão Repasse (<span id="h_perc_res">0</span>%):</td><td align="right"><span id="h_comissao_valor">R$ 0,00</span></td></tr>
                        <tr style="color: green;"><td>(+) Caixinha:</td><td align="right"><span id="h_caixinha_res">R$ 0,00</span></td></tr>
                        <tr style="color: green;"><td>(+) Venda de Produtos/Extras:</td><td align="right"><span id="h_produtos_res">R$ 0,00</span></td></tr>
                        <tr style="font-size: 18px; border-top: 2px solid #000;">
                            <td><strong>LÍQUIDO A RECEBER:</strong></td>
                            <td align="right"><strong><span id="h_total_res">R$ 0,00</span></strong></td>
                        </tr>
                    </table>
                </div>
            </div>
        </div>

        <div id="pagamentos_notas" class="tab-content">
            <div class="form-section">
                <h3>Anotações: Formas de Pagamento</h3>
                <div class="form-group">
                    <div><label>Data</label><input type="date" id="p_data"></div>
                    <div><label>Forma</label><input type="text" id="p_forma"></div>
                    <div><label>Valor</label><input type="number" id="p_valor_nota"></div>
                    <button onclick="registrarNotaPagamento()" style="background: var(--info); color: white;">ANOTAR</button>
                </div>
            </div>
            <table><thead><tr><th>Data</th><th>Forma</th><th>Valor</th><th>Ação</th></tr></thead><tbody id="lista_notas_pagamento"></tbody></table>
        </div>

        <div id="atendimentos_anteriores" class="tab-content">
            <div class="form-section">
                <h3>Anotações: Atendimentos Anteriores</h3>
                <div class="form-group">
                    <div><label>Quinzena</label><input type="text" id="ant_quinzena"></div>
                    <div><label>Valor</label><input type="number" id="ant_valor"></div>
                    <div><label>Forma</label><input type="text" id="ant_forma"></div>
                    <button onclick="registrarAtendimentoAnterior()" style="background: var(--warning); color: white;">SALVAR</button>
                </div>
            </div>
            <table><thead><tr><th>Quinzena</th><th>Valor</th><th>Forma</th><th>Ação</th></tr></thead><tbody id="lista_atendimentos_anteriores"></tbody></table>
        </div>
    </div>
</div>

<script>
    const firebaseConfig = {
        apiKey: "AIzaSyAvyRW2QRemZX7JaMcMLmz6IicqkbnevHk",
        authDomain: "don-hermano.firebaseapp.com",
        projectId: "don-hermano",
        storageBucket: "don-hermano.firebasestorage.app",
        messagingSenderId: "125749350408",
        appId: "1:125749350408:web:bc736f5ef6a4a0f3d881bd",
        databaseURL: "https://don-hermano-default-rtdb.firebaseio.com"
    };

    firebase.initializeApp(firebaseConfig);
    const database = firebase.database();
    let db = { barbeiros: {}, servicos: {}, vendas: {}, despesas: {}, notasPagamento: {}, atendimentosAnteriores: {} };
    let chartFat, chartQtd;

    function carregarDadosNuvem() {
        database.ref('dados_barbearia/').on('value', (snapshot) => {
            const data = snapshot.val();
            db.barbeiros = data?.barbeiros || {};
            db.servicos = data?.servicos || {};
            db.vendas = data?.vendas || {};
            db.despesas = data?.despesas || {};
            db.notasPagamento = data?.notasPagamento || {};
            db.atendimentosAnteriores = data?.atendimentosAnteriores || {};
            renderAll();
        });
    }

    function checkLogin() {
        if(document.getElementById('user').value==='cortedonhermano' && document.getElementById('pass').value==='cortedonhermano') {
            document.getElementById('login-screen').style.display='none';
            document.getElementById('main-system').style.display='block';
            carregarDadosNuvem(); 
        } else alert('Acesso negado');
    }

    function showTab(t) { 
        document.querySelectorAll('.tab-content, .nav-btn').forEach(x=>x.classList.remove('active'));
        document.getElementById(t).classList.add('active');
        if(event) event.currentTarget.classList.add('active');
        if(t==='relatorio') renderRelatorio();
    }

    // AJUSTE: Usando .push() para evitar sobreposição
    function addBarbeiro() { const n=document.getElementById('b_nome').value; if(n){ database.ref('dados_barbearia/barbeiros').push({nome:n}); document.getElementById('b_nome').value=''; }}
    function addServico() { const n=document.getElementById('s_nome').value, p=document.getElementById('s_preco').value; if(n && p){ database.ref('dados_barbearia/servicos').push({nome:n, preco:parseFloat(p)}); document.getElementById('s_nome').value=''; document.getElementById('s_preco').value=''; }}
    function registrarVenda() {
        const d=document.getElementById('v_data').value, sIdx=document.getElementById('v_servico').value, bIdx=document.getElementById('v_barbeiro').value, q=parseInt(document.getElementById('v_qtd').value);
        if(d && sIdx!=="" && bIdx!==""){
            const s = Object.values(db.servicos)[sIdx];
            const b = Object.values(db.barbeiros)[bIdx];
            database.ref('dados_barbearia/vendas').push({data:d, servico:s.nome, qtd:q, valorTotal:(s.preco*q), barbeiro:b.nome});
        }
    }
    function registrarDespesa() {
        const d=document.getElementById('d_data').value, ds=document.getElementById('d_desc').value, v=document.getElementById('d_valor').value;
        if(d&&ds&&v){ database.ref('dados_barbearia/despesas').push({data:d, desc:ds, valor:parseFloat(v)}); document.getElementById('d_desc').value=''; document.getElementById('d_valor').value=''; }
    }
    function registrarNotaPagamento() {
        const d=document.getElementById('p_data').value, f=document.getElementById('p_forma').value, v=document.getElementById('p_valor_nota').value;
        if(d && f){ database.ref('dados_barbearia/notasPagamento').push({data:d, forma:f, valor: parseFloat(v) || 0}); }
    }
    function registrarAtendimentoAnterior() {
        const q=document.getElementById('ant_quinzena').value, v=document.getElementById('ant_valor').value, f=document.getElementById('ant_forma').value;
        if(q && v){ database.ref('dados_barbearia/atendimentosAnteriores').push({quinzena: q, valor: parseFloat(v) || 0, forma: f}); }
    }

    // AJUSTE: Excluir usando a chave do Firebase
    function excluir(pasta, id) { if(confirm("Deseja excluir este registro?")){ database.ref(`dados_barbearia/${pasta}/${id}`).remove(); }}

    function renderAll() {
        const bList = Object.entries(db.barbeiros);
        const sList = Object.entries(db.servicos);
        const vList = Object.entries(db.vendas).sort((a,b)=>new Date(b[1].data)-new Date(a[1].data));
        const dList = Object.entries(db.despesas).sort((a,b)=>new Date(b[1].data)-new Date(a[1].data));
        const nList = Object.entries(db.notasPagamento).sort((a,b)=>new Date(b[1].data)-new Date(a[1].data));
        const aList = Object.entries(db.atendimentosAnteriores);

        document.getElementById('v_barbeiro').innerHTML = '<option value="">...</option>' + bList.map(([id, b], i)=>`<option value="${i}">${b.nome}</option>`).join('');
        document.getElementById('sel_barbeiro_h').innerHTML = '<option value="">...</option>' + bList.map(([id, b], i)=>`<option value="${i}">${b.nome}</option>`).join('');
        document.getElementById('v_servico').innerHTML = '<option value="">...</option>' + sList.map(([id, s], i)=>`<option value="${i}">${s.nome}</option>`).join('');
        
        document.getElementById('lista_barbeiros_ui').innerHTML = bList.map(([id, b])=>`<tr><td>${b.nome}</td><td><button class="btn-del" onclick="excluir('barbeiros', '${id}')">X</button></td></tr>`).join('');
        document.getElementById('lista_servicos_ui').innerHTML = sList.map(([id, s])=>`<tr><td>${s.nome}</td><td>R$ ${s.preco.toFixed(2)}</td><td><button class="btn-del" onclick="excluir('servicos', '${id}')">X</button></td></tr>`).join('');
        document.getElementById('lista_vendas').innerHTML = vList.map(([id, v])=>`<tr><td>${v.data}</td><td>${v.servico}</td><td>${v.barbeiro}</td><td>${v.qtd}</td><td>R$ ${v.valorTotal.toFixed(2)}</td><td><button class="btn-del" onclick="excluir('vendas', '${id}')">X</button></td></tr>`).join('');
        document.getElementById('lista_despesas').innerHTML = dList.map(([id, d])=>`<tr><td>${d.data}</td><td>${d.desc}</td><td>R$ ${d.valor.toFixed(2)}</td><td><button class="btn-del" onclick="excluir('despesas', '${id}')">X</button></td></tr>`).join('');
        document.getElementById('lista_notas_pagamento').innerHTML = nList.map(([id, n])=>`<tr><td>${n.data.split('-').reverse().join('/')}</td><td>${n.forma}</td><td>R$ ${n.valor.toFixed(2)}</td><td><button class="btn-del" onclick="excluir('notasPagamento', '${id}')">X</button></td></tr>`).join('');
        document.getElementById('lista_atendimentos_anteriores').innerHTML = aList.map(([id, a])=>`<tr><td>${a.quinzena}</td><td>R$ ${a.valor.toFixed(2)}</td><td>${a.forma}</td><td><button class="btn-del" onclick="excluir('atendimentosAnteriores', '${id}')">X</button></td></tr>`).join('');
    }

    function renderRelatorio() {
        const ini=document.getElementById('rel_ini').value, fim=document.getElementById('rel_fim').value;
        const vendasArr = Object.values(db.vendas);
        const despArr = Object.values(db.despesas);
        const vF=vendasArr.filter(v=>(!ini||v.data>=ini)&&(!fim||v.data<=fim));
        const dF=despArr.filter(d=>(!ini||d.data>=ini)&&(!fim||d.data<=fim));
        const fat=vF.reduce((a,v)=>a+v.valorTotal,0), gas=dF.reduce((a,d)=>a+d.valor,0);
        document.getElementById('res_bruto').innerText=`R$ ${fat.toFixed(2)}`;
        document.getElementById('res_gastos').innerText=`R$ ${gas.toFixed(2)}`;
        document.getElementById('res_lucro_real').innerText=`R$ ${(fat - gas).toFixed(2)}`;
        const nomesB = Object.values(db.barbeiros).map(b => b.nome);
        if(chartFat) chartFat.destroy(); if(chartQtd) chartQtd.destroy();
        chartFat = new Chart(document.getElementById('chartFaturamento'), { type: 'bar', data: { labels: nomesB, datasets: [{ label: 'R$', data: nomesB.map(n => vF.filter(v=>v.barbeiro===n).reduce((a,v)=>a+v.valorTotal,0)), backgroundColor: '#6a1b9a' }] }, options: { maintainAspectRatio: false } });
        chartQtd = new Chart(document.getElementById('chartQtd'), { type: 'bar', data: { labels: nomesB, datasets: [{ label: 'Atendimentos', data: nomesB.map(n => vF.filter(v=>v.barbeiro===n).reduce((a,v)=>a+v.qtd,0)), backgroundColor: '#2e7d32' }] }, options: { maintainAspectRatio: false } });
        let head='<th>Barbeiro</th>'; Object.values(db.servicos).forEach(s=>head+=`<th>${s.nome}</th>`); document.getElementById('head_comparativo').innerHTML=head;
        let body=""; Object.values(db.barbeiros).forEach(b=>{ body+=`<tr><td><strong>${b.nome}</strong></td>`; Object.values(db.servicos).forEach(s=>{ body+=`<td>${vF.filter(v=>v.barbeiro===b.nome && v.servico===s.nome).reduce((a,v)=>a+v.qtd,0)}</td>`; }); body+=`</tr>`; }); document.getElementById('body_comparativo').innerHTML=body;
    }

    function gerarHolerite() {
        const idx=document.getElementById('sel_barbeiro_h').value, perc=parseFloat(document.getElementById('h_porcentagem').value)/100, ini=document.getElementById('h_ini').value, fim=document.getElementById('h_fim').value;
        const caixinha=parseFloat(document.getElementById('h_caixinha').value)||0, produtos=parseFloat(document.getElementById('h_produtos').value)||0, obs=document.getElementById('h_obs').value;
        if(idx==="" || !ini || !fim) return alert("Filtre profissional e período!");
        const b=Object.values(db.barbeiros)[idx], vF=Object.values(db.vendas).filter(v=>v.barbeiro===b.nome && v.data>=ini && v.data<=fim);
        const res={}; vF.forEach(v=>{res[v.servico]=(res[v.servico]||{q:0, b:0}); res[v.servico].q+=v.qtd; res[v.servico].b+=v.valorTotal;});
        let hT="", totalBS=0; for(let s in res){totalBS+=res[s].b; hT+=`<tr><td>${s.toUpperCase()}</td><td align="center">${res[s].q}</td><td align="right">R$ ${res[s].b.toFixed(2)}</td></tr>`;}
        const valCom = totalBS*perc;
        document.getElementById('h_corpo_tabela').innerHTML=hT||"<tr><td colspan='3'>Sem vendas</td></tr>";
        document.getElementById('h_nome_res').innerText=b.nome.toUpperCase();
        document.getElementById('h_periodo_res').innerText=`${ini.split('-').reverse().join('/')} a ${fim.split('-').reverse().join('/')}`;
        document.getElementById('h_bruto_servicos').innerText=`R$ ${totalBS.toFixed(2)}`;
        document.getElementById('h_perc_res').innerText=(perc*100);
        document.getElementById('h_comissao_valor').innerText=`R$ ${valCom.toFixed(2)}`;
        document.getElementById('h_caixinha_res').innerText=`R$ ${caixinha.toFixed(2)}`;
        document.getElementById('h_produtos_res').innerText=`R$ ${produtos.toFixed(2)}`;
        document.getElementById('h_total_res').innerText=`R$ ${(valCom+caixinha+produtos).toFixed(2)}`;
        document.getElementById('h_obs_container').style.display=obs?'block':'none'; document.getElementById('h_obs_res').innerText=obs;
    }

    function baixarPDF() {
        const el = document.getElementById('area-holerite');
        const nome = document.getElementById('h_nome_res').innerText;
        const opt = { margin: 10, filename: `Comissao_${nome}.pdf`, image: { type: 'jpeg', quality: 0.98 }, html2canvas: { scale: 2, useCORS: true }, jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' } };
        html2pdf().set(opt).from(el).save();
    }
    
    document.getElementById('v_data').valueAsDate = new Date();
    document.getElementById('p_data').valueAsDate = new Date();
</script>
</body>
</html>
