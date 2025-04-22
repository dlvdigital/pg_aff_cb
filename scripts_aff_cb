<!-- Script ClickBank -->
<script>
  window.clickbank = {
    vendor: "glycoshiel",
    affiliate: "vkenishigm"
  };

  // Aguarda 5 segundos após o carregamento da página
  window.addEventListener('DOMContentLoaded', function () {
    setTimeout(function () {
      var script = document.createElement('script');
      script.src = "https://scripts.clickbank.net/hop.min.js";
      script.type = "text/javascript";
      document.body.appendChild(script);
    }, 5000);
  });
</script>

<!-- Script Disparo de DTC Redtrack -->
<script>
  document.addEventListener('DOMContentLoaded', function() {
    // Função para extrair o valor de um parâmetro específico de uma URL
    function getParameterByName(url, name) {
      name = name.replace(/[\[\]]/g, '\\$&');
      const regex = new RegExp('[?&]' + name + '(=([^&#]*)|&|#|$)');
      const resultados = regex.exec(url);
      if (!resultados) return null;
      if (!resultados[2]) return '';
      return decodeURIComponent(resultados[2].replace(/\+/g, ' '));
    }

    // Adicionar listener de clique usando delegação de eventos
    document.addEventListener('click', function(event) {
      let target = event.target;
      while (target && target !== document) {
        if (target.tagName.toLowerCase() === 'a') {
          break;
        }
        target = target.parentElement;
      }

      if (target && target.tagName.toLowerCase() === 'a') {
        const href = target.getAttribute('href') || '';

        // Verifica se a URL de destino termina com ".clickbank.net"
        const checkoutDomainPattern = /\.clickbank\.net(?:\/|$)/;
        if (checkoutDomainPattern.test(href)) {
          const clickid = getParameterByName(href, 'tid') || '{CLICKID}';

          if (clickid !== '{CLICKID}') {
            event.preventDefault();

            // Criar e adicionar o script do postback dinamicamente
            const newScript = document.createElement('script');
            newScript.async = true;
            newScript.type = 'text/javascript';
            newScript.text = `
              (function() {
                var script = document.createElement('script');
                script.src = 'https://track.thegenesis.pro/postback?clickid=' + encodeURIComponent('${clickid}') + '&type=DTC';
                document.head.appendChild(script);
              })();
            `;

            document.head.appendChild(newScript);

            newScript.onload = function() {
              console.log('Script de InitiateCheckout do Redtrack carregado.');
            };

            newScript.onerror = function() {
              console.error('Falha ao carregar o script de InitiateCheckout do Redtrack.');
            };

            setTimeout(function() {
              window.location.href = href;
            }, 300);
          } else {
            console.warn('clickid não encontrado na URL de destino.');
          }
        }
      }
    }, false);
  });
</script>

<!-- Script VTurb Fullscreen -->

<script>
  function moveCTA(container) {
   const cta = container.querySelector(".smartplay + .smartplayer-call-action");

   if (!cta) return;

   container.parentNode.insertBefore(cta, container.nextSibling);
  }

  function insertBeforePlayer(instance, prefix) {
   if(!instance) return;

   const player = instance.mobileContainer || instance.container;

   const container = document.createElement("div");

   container.id = `${prefix}-${instance.options.id}`
   container.classList.add(prefix);

   player.parentNode.insertBefore(container, player);

   container.appendChild(player);

   return container;
  }

  function mountContainers() {
   const instances = window.smartplayer.instances;

   instances.forEach(instance => {
    const container = insertBeforePlayer(instance, `player-fake-fs`)

    if(!container) return;

    moveCTA(container)

    let firstClick = instance.resume?.inResume === undefined;
   });

   instances.forEach(instance => insertBeforePlayer(instance, `player-auto-height`));
  }

  function mountStyles() {
   const styles = document.createElement("style");

   styles.innerHTML = `
    .player-fake-fs[data-fullscreen='true'] {
     display: flex !important;
     align-items: center !important;
     background-color: #000 !important;
     height: 100vh !important;
     width: 100vw !important;
     cursor: pointer;
    }

    .player-fake-fs[data-fullscreen='true'] .player-auto-height .smartplayer-mobile-container {
     max-width: 100% !important;
    }

    .player-fake-fs[data-fullscreen='false'] .player-auto-height {
     width: 100% !important;
    }

    .player-fake-fs[data-fullscreen='false'] {
     margin-left: 0 !important;
    }

    .player-auto-height {
     margin: 0 auto;
     width: 100%;
    }
   `;

   document.head.appendChild(styles);
  }

  function adjustXPositionFs() {
   const currentFs = document.querySelectorAll(".player-fake-fs[data-fullscreen='true']");

   currentFs.forEach(container => {
    container.style.marginLeft = "0";

    const position = container.getBoundingClientRect()

    if(position.left <= 0) return;

    container.style.marginLeft = `-${position.left}px`;
   });
  }

  function changeWidthByHeight() {
   const containers = document.querySelectorAll(".player-auto-height");

   containers.forEach(container => {
    const video = container.querySelector("video");

    if (!video) return;

    const aspectRatio = video.clientWidth / video.clientHeight;

    const windowHeight = window.innerHeight;
    const newWidth = windowHeight * aspectRatio;

    const width = (newWidth / window.innerWidth) * 100;

    container.style.width = `${width >= 100 ? 100 : width}%`;
   });
  }

  function scrollToFS(container) {
   const position = container.getBoundingClientRect();

   window.scrollTo({
    top: position.top + window.scrollY,
    left: position.left + window.scrollX,
   });
  }

  function toggleFs(id, inFullscreen = false) {
   const container = document.getElementById(`player-fake-fs-${id}`);
   if (smartplayer.instances[0].smartAutoPlay || !container) return;

   container.dataset.fullscreen = inFullscreen.toString()

   var el = document.querySelector(".player-fake-fs");
   var elDistanceToTop = window.pageYOffset + el.getBoundingClientRect().top
   var html = document.querySelector("html");

   if(!inFullscreen){
    html.style.marginTop = `${elDistanceToTop}px`;
   }

   if (inFullscreen) {
    html.style.marginTop = `-${elDistanceToTop}px`;
    console.log(el);
    window.addEventListener("resize", changeWidthByHeight);

    changeWidthByHeight();
    adjustXPositionFs();

    return scrollToFS(container);
   }

   window.removeEventListener("resize", changeWidthByHeight);
  }

  function mountFakeFsEvents() {
   const instances = window.smartplayer.instances;

   window.addEventListener("resize", adjustXPositionFs);

   instances.forEach(instance => {
    instance.on("play", () => toggleFs(instance.options.id, true));

    instance.on("pause", () => toggleFs(instance.options.id, false));
   });
  }

  function customEvents() {
   mountContainers();
   mountStyles();
   mountFakeFsEvents()
  }

  function checkPlayerLoaded() {
   if (window.smartplayer && window.smartplayer.instances) return customEvents()

   return setTimeout(checkPlayerLoaded, 100)
  }

  window.addEventListener("load", () => checkPlayerLoaded())
</script>

<!-- Script RTKCPMID -->

<script>
      // Retrieve a parameter value from the URL
      function getQueryParam(param) {
        let urlParams = new URLSearchParams(window.location.search);
        return urlParams.get(param);
      }

      // Save relevant parameters to Local Storage
      function saveSubIDs() {
        let rtkcpmid = getQueryParam('rtkcpmid'); // Captura o parâmetro 'rtkcpmid'

        if (rtkcpmid) localStorage.setItem('rtkcpmid', rtkcpmid);

        // Verifica se o parâmetro 'rtkcpmid' foi encontrado na URL
        if (rtkcpmid) {
          // Cria o script dinamicamente
          const script = document.createElement('script');
          script.type = 'text/javascript';
          script.src = `https://track.thegenesis.pro/track.js?rtkcmpid=${rtkcpmid}`;
          document.body.appendChild(script);
        } else {
          console.error("Parâmetro 'rtkcpmid' não encontrado na URL.");
        }
      }

      // Append saved IDs to links on the page
      function appendSubIDsToLinks() {
        let rtkcpmid = localStorage.getItem('rtkcpmid');

        document.querySelectorAll('a').forEach(link => {
          let url = new URL(link.href);
          if (rtkcpmid) url.searchParams.set('rtkcpmid', rtkcpmid);

          link.href = url.toString();
        });
      }

      document.addEventListener('DOMContentLoaded', () => {
        saveSubIDs();
        appendSubIDsToLinks();
      });
</script>

<!-- Script Disparo de Eventos Redtrack -->
<script type="text/javascript" src="https://track.thegenesis.pro/track.js"></script>

<script>
(function() {
    /**
     * Função para obter o valor de um cookie pelo nome.
     * @param {string} nome - Nome do cookie.
     * @returns {string|null} - Valor do cookie ou null se não encontrado.
     */
    function getCookie(nome) {
        const nomeEQ = nome + "=";
        const ca = document.cookie.split(';');
        for(let i = 0; i < ca.length; i++) {
            let c = ca[i].trim();
            if (c.indexOf(nomeEQ) === 0) {
                return decodeURIComponent(c.substring(nomeEQ.length, c.length));
            }
        }
        return null;
    }

    /**
     * Função para inicializar o rastreamento com o clickid disponível.
     * @param {string} clickid - O identificador de clique extraído do cookie.
     */
    function initializeTracking(clickid) {
        console.log('Cookie encontrado: ', clickid);

        // Adiciona cid=clickid à URL atual se ainda não estiver presente
        var currentUrl = window.location.href;
        if (currentUrl.indexOf('cid=') === -1) {
            var separator = currentUrl.indexOf('?') > -1 ? '&' : '?';
            var newUrl = currentUrl + separator + 'tid=' + encodeURIComponent(clickid);
            window.history.replaceState(null, '', newUrl);
            console.log('URL da página modificada: ', window.location.href);
        }

        // Aguarda 5 segundos antes de modificar os links
        setTimeout(function() {
            document.querySelectorAll('a').forEach(function(el) {
                if (el.href.includes("{clickid}") || el.href.includes("%7Bclickid%7D")) {
                    el.href = el.href.replace(/{clickid}/g, clickid).replace(/%7Bclickid%7D/g, clickid);
                    console.log('URL do link modificada após 5 segundos: ', el.href);
                }
            });
        }, 5000);

        // Define as URLs de postback com o clickid obtido do cookie
        let e = {
            view: `https://track.thegenesis.pro/postback?clickid=${encodeURIComponent(clickid)}&type=VSL%20View`,
            play: `https://track.thegenesis.pro/postback?clickid=${encodeURIComponent(clickid)}&type=VSL%20-%20Play`,
            leadView: `https://track.thegenesis.pro/postback?clickid=${encodeURIComponent(clickid)}&type=VSL%20-%20Lead%20View`,
            pitchView: `https://track.thegenesis.pro/postback?clickid=${encodeURIComponent(clickid)}&type=VSL%20-%20Pitch%20View`
        };

        // Envia uma requisição de visualização imediatamente
        fetch(e.view).catch(err => console.error('Erro ao enviar VSL View:', err));

        // Escuta por mensagens enviadas à janela para diferentes interações
        window.addEventListener("message", function(event) {
            const data = event.data;

            if (data === "VSL View") {
                fetch(e.view).catch(err => console.error('Erro ao enviar VSL View:', err));
                console.log("VSL View");
            }

            if (data === "VSL - Play") {
                fetch(e.play).catch(err => console.error('Erro ao enviar VSL - Play:', err));
                // Oculta todos os elementos com a classe 'play'
                document.querySelectorAll('.play').forEach(function(element) {
                    element.style.display = 'none';
                });
                // Altera a cor de fundo da página para preto
                document.body.style.backgroundColor = '#000000';
                console.log("VSL - Play");
            }

            if (data === "VSL - Lead View") {
                fetch(e.leadView).catch(err => console.error('Erro ao enviar VSL - Lead View:', err));
                console.log("VSL - Lead View");
            }

            if (data === "VSL - Pitch View") {
                // Armazena a flag no localStorage
                localStorage.setItem("pitchView", "true");

                fetch(e.pitchView).catch(err => console.error('Erro ao enviar VSL - Pitch View:', err));
                console.log("VSL - Pitch View");
            }
        });
    }

    /**
     * Função para verificar periodicamente a disponibilidade do clickid.
     * @param {number} interval - Intervalo entre as verificações em milissegundos.
     * @param {number} maxAttempts - Número máximo de tentativas antes de parar.
     */
    function checkClickid(interval, maxAttempts) {
        let attempts = 0;
        var intervalId = setInterval(function() {
            var clickid = getCookie('rtkclickid-store');

            if (clickid) {
                clearInterval(intervalId);
                initializeTracking(clickid);
            } else {
                attempts++;
                console.log(`Tentativa ${attempts}: Cookie rtkclickid-store ainda não disponível`);

                if (attempts >= maxAttempts) {
                    clearInterval(intervalId);
                    console.log('Cookie rtkclickid-store não encontrado após várias tentativas');
                }
            }
        }, interval);
    }

    // Iniciar a verificação assim que o script é carregado
    // Configura para verificar a cada 100ms, com no máximo 50 tentativas (5 segundos)
    checkClickid(100, 50);
})();
</script>
