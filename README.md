<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Fila Inteligente - Refeitório</title>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700;800&display=swap" rel="stylesheet">
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Poppins', sans-serif; }
    body { background-color: #f1f5f9; color: #1e293b; min-height: 100vh; }
    
    header { background: linear-gradient(135deg, #0284c7, #0f766e); color: white; padding: 15px; text-align: center; box-shadow: 0 4px 12px rgba(0,0,0,0.15); }
    header h1 { font-size: 1.5rem; margin-bottom: 8px; font-weight: 800; letter-spacing: 0.5px; }
    .nav-links { display: flex; justify-content: center; gap: 8px; flex-wrap: wrap; }
    .nav-btn { background: rgba(255,255,255,0.2); color: white; border: none; padding: 8px 16px; border-radius: 20px; font-size: 0.85rem; font-weight: 600; cursor: pointer; transition: 0.2s; }
    .nav-btn.active { background: white; color: #0284c7; font-weight: 800; }

    .status-bar { background: #0f172a; color: #38bdf8; text-align: center; font-size: 0.8rem; padding: 6px; font-weight: 600; }

    /* Estilo Telemovel */
    .container { max-width: 600px; margin: 20px auto; padding: 0 15px; }
    .banner-chamada { background: linear-gradient(135deg, #0284c7, #0369a1); color: white; text-align: center; padding: 18px; border-radius: 16px; margin-bottom: 20px; box-shadow: 0 4px 12px rgba(2,132,199,0.3); }
    .banner-chamada .valor { font-size: 2.2rem; font-weight: 800; color: #facc15; margin-top: 4px; word-break: break-word; }

    .grid-turmas { display: grid; grid-template-columns: repeat(auto-fill, minmax(120px, 1fr)); gap: 10px; }
    .btn-turma { background: white; border: 2px solid #0284c7; color: #0284c7; font-size: 1rem; font-weight: 700; padding: 15px 8px; border-radius: 12px; cursor: pointer; transition: 0.15s; }
    .btn-turma:active { background: #0284c7; color: white; transform: scale(0.95); }

    .acoes-rapidas { display: flex; gap: 10px; margin-top: 15px; }
    .btn-acao { flex: 1; border: none; padding: 12px; border-radius: 10px; font-weight: 700; color: white; cursor: pointer; font-size: 0.9rem; }
    .btn-cancelar { background: #ef4444; }

    .card-box { background: white; border-radius: 14px; padding: 16px; margin-top: 20px; box-shadow: 0 2px 8px rgba(0,0,0,0.05); }
    .lista-historico { list-style: none; margin-top: 8px; }
    .lista-historico li { padding: 8px 0; border-bottom: 1px solid #f1f5f9; font-weight: 600; font-size: 0.9rem; }

    /* Estilo Edicao */
    .form-group { display: flex; gap: 8px; margin-bottom: 15px; }
    .input-turma { flex: 1; padding: 10px 14px; border: 2px solid #cbd5e1; border-radius: 10px; font-size: 0.95rem; font-weight: 600; }
    .btn-add { background: #10b981; color: white; border: none; padding: 10px 16px; border-radius: 10px; font-weight: 700; cursor: pointer; }
    .lista-edicao { list-style: none; }
    .item-edicao { display: flex; justify-content: space-between; align-items: center; padding: 10px; background: #f8fafc; border-radius: 8px; margin-bottom: 6px; border: 1px solid #e2e8f0; font-weight: 700; }
    .btn-del { background: #ef4444; color: white; border: none; padding: 6px 12px; border-radius: 6px; cursor: pointer; font-size: 0.8rem; font-weight: 700; }

    /* Estilo TV */
    body.tv-mode { background: #090d16; color: white; }
    .tv-wrapper { display: flex; flex-direction: column; justify-content: space-between; min-height: 85vh; text-align: center; padding: 20px; max-width: 1000px; margin: 0 auto; }
    .tv-header { display: flex; justify-content: space-between; align-items: center; background: #161e2e; padding: 15px 25px; border-radius: 16px; border: 1px solid #233044; }
    .tv-titulo { font-size: 1.5rem; font-weight: 800; color: #38bdf8; text-transform: uppercase; }
    .tv-display { background: #161e2e; border: 4px solid #0284c7; border-radius: 24px; padding: 40px 20px; margin: 20px 0; box-shadow: 0 0 35px rgba(2,132,199,0.35); }
    .tv-turma-nome { font-size: 5.5rem; font-weight: 800; color: #4ade80; margin: 15px 0; min-height: 130px; word-break: break-word; }
    .audio-alert { background: #f59e0b; color: #000; padding: 12px; border-radius: 10px; font-weight: 800; cursor: pointer; margin-bottom: 15px; }
  </style>
</head>
<body>

  <header>
    <h1>🍽️ Fila Inteligente</h1>
    <div class="nav-links">
      <button class="nav-btn" id="btnMobile" onclick="mudarModo('mobile')">📱 Celular</button>
      <button class="nav-btn" id="btnTV" onclick="mudarModo('tv')">📺 TV</button>
      <button class="nav-btn" id="btnAdmin" onclick="mudarModo('admin')">✏️ Editar Turmas</button>
    </div>
  </header>

  <div class="status-bar">
    Conectado: <strong id="txtCanal">fila-inteligente-refeitorio</strong>
  </div>

  <div id="app"></div>

  <script>
    // Código adaptado em JavaScript nativo (ES5) para compatibilidade total com Smart TVs antigas
    var canal = "fila-inteligente-refeitorio";
    var turmaAtual = "Aguardando...";
    var historico = [];

    var turmasPadrao = [
      "1º ADM", "2º ADM", "3º ADM", 
      "1º INFO", "2º INFO", "3º INFO", 
      "6º ANO A", "7º ANO A", "8º ANO A"
    ];

    var turmas = turmasPadrao;
    try {
      var salvas = localStorage.getItem('filainteligente_turmas');
      if (salvas) { turmas = JSON.parse(salvas); }
    } catch(e) {}

    function getParam(param) {
      var match = RegExp('[?&]' + param + '=([^&]*)').exec(window.location.search);
      return match && decodeURIComponent(match[1].replace(/\+/g, ' '));
    }

    var modo = getParam('modo') || 'mobile';

    function mudarModo(m) {
      modo = m;
      if (window.history && window.history.pushState) {
        var base = window.location.protocol + "//" + window.location.host + window.location.pathname;
        if (m === 'tv') { base += '?modo=tv'; }
        else if (m === 'admin') { base += '?modo=admin'; }
        window.history.pushState({}, '', base);
      }
      render();
    }

    var bc = null;
    if (window.BroadcastChannel) {
      try {
        bc = new BroadcastChannel('filainteligente_channel_local');
        bc.onmessage = function(e) { processarSinal(e.data); };
      } catch(e) {}
    }

    // Conexão SSE
    var es = null;
    function conectar() {
      if (es) { try { es.close(); } catch(e){} }
      if (window.EventSource) {
        try {
          es = new EventSource('https://ntfy.sh/' + canal + '/sse');
          es.onmessage = function(e) {
            try {
              var data = JSON.parse(e.data);
              if (data.event === 'message' && data.message) {
                processarSinal(data.message);
              }
            } catch(err) {}
          };
        } catch(e) {}
      }
    }

    // Polling HTTP (usando XMLHttpRequest legado para a TV antiga)
    setInterval(function() {
      try {
        var xhr = new XMLHttpRequest();
        xhr.open('GET', 'https://ntfy.sh/' + canal + '/json?poll=1&since=10s', true);
        xhr.onload = function() {
          if (xhr.status >= 200 && xhr.status < 400) {
            var lines = xhr.responseText.trim().split("\n");
            for (var i = 0; i < lines.length; i++) {
              if (lines[i]) {
                try {
                  var data = JSON.parse(lines[i]);
                  if (data.message) { processarSinal(data.message); }
                } catch(e) {}
              }
            }
          }
        };
        xhr.send();
      } catch(err) {}
    }, 2000);

    function enviarRede(texto) {
      processarSinal(texto);
      if (bc) { try { bc.postMessage(texto); } catch(e){} }
      try {
        var xhr = new XMLHttpRequest();
        xhr.open('POST', 'https://ntfy.sh/' + canal, true);
        xhr.send(texto);
      } catch(e) {}
    }

    function processarSinal(texto) {
      if (texto.indexOf("CFG_TURMAS:") === 0) {
        var listaStr = texto.replace("CFG_TURMAS:", "");
        turmas = listaStr.split("|");
        try { localStorage.setItem('filainteligente_turmas', JSON.stringify(turmas)); } catch(e){}
        if (modo !== 'tv') render();
        return;
      }

      if (turmaAtual === texto) return;

      turmaAtual = texto;

      if (texto !== "Aguardando..." && texto !== "Chamada Cancelada") {
        var agora = new Date();
        var hrs = ("0" + agora.getHours()).slice(-2);
        var mins = ("0" + agora.getMinutes()).slice(-2);
        var hora = hrs + ":" + mins;
        historico.unshift(texto + " (" + hora + ")");
        if (historico.length > 5) historico.pop();
      }

      var elTv = document.getElementById('tvTurma');
      if (elTv) elTv.innerText = turmaAtual;

      var elMob = document.getElementById('mobTurma');
      if (elMob) elMob.innerText = turmaAtual;

      if (modo === 'tv' && texto !== "Aguardando...") {
        falarNaTV(texto);
      }

      atualizarHistoricoUI();
    }

    function falarNaTV(texto) {
      try {
        var AudioCtx = window.AudioContext || window.webkitAudioContext;
        if (AudioCtx) {
          var ctx = new AudioCtx();
          var osc = ctx.createOscillator();
          var gain = ctx.createGain();
          osc.frequency.setValueAtTime(523.25, ctx.currentTime);
          gain.gain.setValueAtTime(0.3, ctx.currentTime);
          osc.connect(gain);
          gain.connect(ctx.destination);
          osc.start(0);
          osc.stop(ctx.currentTime + 0.4);
        }

        setTimeout(function() {
          if ('speechSynthesis' in window && texto && texto.indexOf('Cancelada') === -1) {
            window.speechSynthesis.cancel();
            var msg = new SpeechSynthesisUtterance("Atenção, turma " + texto + ", dirigir-se ao refeitório.");
            msg.lang = 'pt-BR';
            window.speechSynthesis.speak(msg);
          }
        }, 500);
      } catch(e) {}
    }

    function render() {
      var container = document.getElementById('app');
      document.body.className = '';
      document.getElementById('btnMobile').className = 'nav-btn';
      document.getElementById('btnTV').className = 'nav-btn';
      document.getElementById('btnAdmin').className = 'nav-btn';

      if (modo === 'tv') {
        document.body.className = 'tv-mode';
        document.getElementById('btnTV').className = 'nav-btn active';

        container.innerHTML = 
          '<div class="tv-wrapper">' +
            '<div class="audio-alert" onclick="this.style.display=\'none\'; falarNaTV(\'\');">' +
              '🔊 CLIQUE AQUI UMA VEZ NA TV PARA ATIVAR O SOM E VOZ' +
            '</div>' +
            '<div class="tv-header">' +
              '<div class="tv-titulo">🍽️ FILA INTELIGENTE</div>' +
              '<div id="relogio">00:00:00</div>' +
            '</div>' +
            '<div class="tv-display">' +
              '<div style="color:#94a3b8; font-weight:700;">PRÓXIMA TURMA PARA O REFEITÓRIO:</div>' +
              '<div class="tv-turma-nome" id="tvTurma">' + turmaAtual + '</div>' +
            '</div>' +
          '</div>';

        setInterval(function() {
          var r = document.getElementById('relogio');
          if (r) {
            var d = new Date();
            var h = ("0" + d.getHours()).slice(-2);
            var m = ("0" + d.getMinutes()).slice(-2);
            var s = ("0" + d.getSeconds()).slice(-2);
            r.innerText = h + ":" + m + ":" + s;
          }
        }, 1000);

      } else if (modo === 'admin') {
        document.getElementById('btnAdmin').className = 'nav-btn active';

        var html = 
          '<div class="container">' +
            '<div class="card-box">' +
              '<h3 style="margin-bottom:12px;">✏️ Gerenciar Turmas do Sistema</h3>' +
              '<div class="form-group">' +
                '<input type="text" id="inputNovaTurma" class="input-turma" placeholder="Ex: 9º ANO B">' +
                '<button class="btn-add" onclick="adicionarTurma()">➕ Adicionar</button>' +
              '</div>' +
              '<ul class="lista-edicao">';

        for (var i = 0; i < turmas.length; i++) {
          html += 
            '<li class="item-edicao">' +
              '<span>' + turmas[i] + '</span>' +
              '<button class="btn-del" onclick="removerTurma(' + i + ')">🗑️ Excluir</button>' +
            '</li>';
        }

        html += 
              '</ul>' +
            '</div>' +
          '</div>';

        container.innerHTML = html;

      } else {
        document.getElementById('btnMobile').className = 'nav-btn active';

        var html = 
          '<div class="container">' +
            '<div class="banner-chamada">' +
              '<div style="font-size:0.85rem; text-transform:uppercase; font-weight:700;">Chamando na TV:</div>' +
              '<div class="valor" id="mobTurma">' + turmaAtual + '</div>' +
            '</div>' +
            '<div style="margin-bottom:10px; font-weight:700; color:#475569;">Clique para chamar:</div>' +
            '<div class="grid-turmas">';

        for (var j = 0; j < turmas.length; j++) {
          var t = turmas[j];
          html += '<button class="btn-turma" onclick="enviarRede(\'' + t + '\')">' + t + '</button>';
        }

        html += 
            '</div>' +
            '<div class="acoes-rapidas">' +
              '<button class="btn-acao btn-cancelar" onclick="enviarRede(\'Chamada Cancelada\')">❌ Cancelar Chamada</button>' +
            '</div>' +
            '<div class="card-box">' +
              '<h3>📋 Últimas Chamadas</h3>' +
              '<ul class="lista-historico" id="listaHist"></ul>' +
            '</div>' +
          '</div>';

        container.innerHTML = html;
        atualizarHistoricoUI();
      }
    }

    window.adicionarTurma = function() {
      var input = document.getElementById('inputNovaTurma');
      var nome = input.value.replace(/^\s+|\s+$/g, '').toUpperCase();
      if (nome) {
        turmas.push(nome);
        salvarESincronizarTurmas();
        input.value = "";
        render();
      }
    };

    window.removerTurma = function(index) {
      turmas.splice(index, 1);
      salvarESincronizarTurmas();
      render();
    };

    function salvarESincronizarTurmas() {
      try { localStorage.setItem('filainteligente_turmas', JSON.stringify(turmas)); } catch(e){}
      enviarRede("CFG_TURMAS:" + turmas.join("|"));
    }

    function atualizarHistoricoUI() {
      var el = document.getElementById('listaHist');
      if (!el) return;
      el.innerHTML = "";
      for (var k = 0; k < historico.length; k++) {
        var li = document.createElement('li');
        li.innerText = historico[k];
        el.appendChild(li);
      }
    }

    conectar();
    render();
  </script>
</body>
</html>
