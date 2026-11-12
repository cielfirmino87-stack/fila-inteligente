<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Fila Inteligente - Refeitório</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; font-family: Arial, Helvetica, sans-serif; }
    body { background-color: #f1f5f9; color: #1e293b; }
    
    header { background-color: #0284c7; color: #ffffff; padding: 15px; text-align: center; }
    header h1 { font-size: 24px; margin-bottom: 10px; font-weight: bold; }
    .nav-links { text-align: center; }
    .nav-btn { background-color: #0369a1; color: #ffffff; border: 1px solid #38bdf8; padding: 8px 16px; border-radius: 20px; font-size: 14px; font-weight: bold; cursor: pointer; margin: 3px; }
    .nav-btn.active { background-color: #ffffff; color: #0284c7; }

    .status-bar { background-color: #0f172a; color: #38bdf8; text-align: center; font-size: 13px; padding: 6px; font-weight: bold; }

    /* Estilo Celular */
    .container { max-width: 600px; margin: 20px auto; padding: 0 15px; }
    .banner-chamada { background-color: #0284c7; color: #ffffff; text-align: center; padding: 18px; border-radius: 14px; margin-bottom: 20px; }
    .banner-chamada .valor { font-size: 34px; font-weight: bold; color: #facc15; margin-top: 5px; }

    .grid-turmas { text-align: center; }
    .btn-turma { background-color: #ffffff; border: 2px solid #0284c7; color: #0284c7; font-size: 16px; font-weight: bold; padding: 15px 10px; border-radius: 10px; cursor: pointer; width: 30%; margin: 1.5%; display: inline-block; }
    .btn-turma:active { background-color: #0284c7; color: #ffffff; }

    .acoes-rapidas { margin-top: 15px; text-align: center; }
    .btn-cancelar { background-color: #ef4444; color: #ffffff; border: none; padding: 14px; border-radius: 10px; font-weight: bold; cursor: pointer; font-size: 16px; width: 100%; }

    .card-box { background-color: #ffffff; border-radius: 12px; padding: 16px; margin-top: 20px; border: 1px solid #cbd5e1; }
    .lista-historico { list-style: none; margin-top: 8px; }
    .lista-historico li { padding: 8px 0; border-bottom: 1px solid #e2e8f0; font-weight: bold; font-size: 15px; }

    /* Estilo Edicao */
    .form-group { margin-bottom: 15px; }
    .input-turma { width: 68%; padding: 10px; border: 2px solid #cbd5e1; border-radius: 8px; font-size: 15px; font-weight: bold; }
    .btn-add { width: 29%; background-color: #10b981; color: #ffffff; border: none; padding: 12px 8px; border-radius: 8px; font-weight: bold; cursor: pointer; font-size: 14px; }
    .lista-edicao { list-style: none; }
    .item-edicao { padding: 10px; background-color: #f8fafc; border-radius: 8px; margin-bottom: 8px; border: 1px solid #cbd5e1; font-weight: bold; overflow: hidden; }
    .btn-del { float: right; background-color: #ef4444; color: #ffffff; border: none; padding: 6px 12px; border-radius: 6px; cursor: pointer; font-size: 13px; font-weight: bold; }

    /* Estilo TV Clássico (Compatível com TVs antigas) */
    body.tv-mode { background-color: #090d16; color: #ffffff; }
    .tv-wrapper { width: 94%; max-width: 1200px; margin: 20px auto; text-align: center; }
    .audio-alert { background-color: #f59e0b; color: #000000; padding: 16px; border-radius: 10px; font-size: 20px; font-weight: bold; cursor: pointer; margin-bottom: 20px; }
    .tv-header { background-color: #161e2e; padding: 18px 25px; border-radius: 14px; border: 2px solid #233044; margin-bottom: 25px; overflow: hidden; }
    .tv-titulo { float: left; font-size: 26px; font-weight: bold; color: #38bdf8; }
    .tv-direita { float: right; }
    .tv-relogio { font-size: 26px; font-weight: bold; color: #ffffff; display: inline-block; margin-right: 15px; }
    .btn-sair-tv { background-color: #334155; color: #cbd5e1; border: 1px solid #475569; padding: 6px 12px; border-radius: 8px; font-size: 14px; cursor: pointer; }
    .tv-display { background-color: #161e2e; border: 6px solid #0284c7; border-radius: 24px; padding: 60px 20px; text-align: center; }
    .tv-subtitulo { color: #cbd5e1; font-size: 26px; font-weight: bold; margin-bottom: 20px; letter-spacing: 1px; }
    .tv-turma-nome { font-size: 110px; font-weight: bold; color: #4ade80; line-height: 1.2; word-wrap: break-word; }
  </style>
</head>
<body>

  <div id="topHeader">
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
  </div>

  <div id="app"></div>

  <script>
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
      // Efeito visual (pisca a tela para chamar atenção no refeitório)
      var box = document.getElementById('tvDisplayBox');
      var txt = document.getElementById('tvTurma');
      if (box && txt && texto) {
        box.style.backgroundColor = '#facc15';
        txt.style.color = '#000000';
        setTimeout(function() {
          box.style.backgroundColor = '#161e2e';
          txt.style.color = '#4ade80';
        }, 1500);
      }

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
      var topHeader = document.getElementById('topHeader');
      document.body.className = '';
      document.getElementById('btnMobile').className = 'nav-btn';
      document.getElementById('btnTV').className = 'nav-btn';
      document.getElementById('btnAdmin').className = 'nav-btn';

      if (modo === 'tv') {
        document.body.className = 'tv-mode';
        document.body.style.backgroundColor = '#090d16';
        document.body.style.color = '#ffffff';
        if (topHeader) topHeader.style.display = 'none';

        container.innerHTML = 
          '<div class="tv-wrapper">' +
            '<div class="audio-alert" onclick="this.style.display=\'none\'; falarNaTV(\'\');">' +
              '🔊 CLIQUE AQUI UMA VEZ NA TV PARA ATIVAR O SOM E VOZ' +
            '</div>' +
            '<div class="tv-header">' +
              '<div class="tv-titulo">🍽️ FILA INTELIGENTE</div>' +
              '<div class="tv-direita">' +
                '<span class="tv-relogio" id="relogio">00:00:00</span>' +
                '<button class="btn-sair-tv" onclick="mudarModo(\'mobile\')">Sair TV</button>' +
              '</div>' +
            '</div>' +
            '<div class="tv-display" id="tvDisplayBox">' +
              '<div class="tv-subtitulo">PRÓXIMA TURMA PARA O REFEITÓRIO:</div>' +
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
        document.body.style.backgroundColor = '#f1f5f9';
        document.body.style.color = '#1e293b';
        if (topHeader) topHeader.style.display = 'block';
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
        document.body.style.backgroundColor = '#f1f5f9';
        document.body.style.color = '#1e293b';
        if (topHeader) topHeader.style.display = 'block';
        document.getElementById('btnMobile').className = 'nav-btn active';

        var html = 
          '<div class="container">' +
            '<div class="banner-chamada">' +
              '<div style="font-size:14px; text-transform:uppercase; font-weight:bold;">Chamando na TV:</div>' +
              '<div class="valor" id="mobTurma">' + turmaAtual + '</div>' +
            '</div>' +
            '<div style="margin-bottom:10px; font-weight:bold; color:#475569;">Clique para chamar:</div>' +
            '<div class="grid-turmas">';

        for (var j = 0; j < turmas.length; j++) {
          var t = turmas[j];
          html += '<button class="btn-turma" onclick="enviarRede(\'' + t + '\')">' + t + '</button>';
        }

        html += 
            '</div>' +
            '<div class="acoes-rapidas">' +
              '<button class="btn-cancelar" onclick="enviarRede(\'Chamada Cancelada\')">❌ Cancelar Chamada</button>' +
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
