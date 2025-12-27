<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>Roblox Utility Pro</title>
    <style>
        body { margin: 0; overflow: hidden; background: transparent; font-family: sans-serif; }

        /* MIRA PROFISSIONAL */
        #crosshair-container {
            position: fixed; top: 50%; left: 50%;
            transform: translate(-50%, -50%);
            pointer-events: none; z-index: 999; display: none;
        }
        .sniper-ring {
            width: 80px; height: 80px;
            border: 2px solid rgba(255, 0, 0, 0.5);
            border-radius: 50%; position: relative;
        }
        .center-dot {
            position: absolute; top: 50%; left: 50%;
            width: 6px; height: 6px; background: #00ff00;
            border-radius: 50%; transform: translate(-50%, -50%);
            box-shadow: 0 0 10px #00ff00;
        }

        /* MENU PRETO E OP */
        #gui {
            position: absolute; top: 10px; left: 10px;
            width: 220px; background: #000;
            border: 2px solid #333; color: white;
            padding: 10px; border-radius: 5px; cursor: move;
        }
        h2 { font-size: 14px; color: #00ff00; margin: 0 0 10px; text-align: center; border-bottom: 1px solid #333; }
        
        button {
            width: 100%; padding: 8px; margin: 4px 0;
            background: #111; color: #eee; border: 1px solid #444;
            cursor: pointer; font-size: 11px; text-align: left;
        }
        button:hover { border-color: #00ff00; }
        button.active { background: #00ff00; color: #000; font-weight: bold; }

        /* Filtro de Visão (Night Vision) */
        #vision-filter {
            position: fixed; top: 0; left: 0; width: 100vw; height: 100vh;
            pointer-events: none; z-index: 1; transition: 0.3s;
        }
    </style>
</head>
<body>

<div id="vision-filter"></div>

<div id="crosshair-container">
    <div class="sniper-ring"><div class="center-dot"></div></div>
</div>

<div id="gui" onmousedown="dragElement(this)">
    <h2>ROBLOX HELPER</h2>
    <button onclick="toggleMira(this)">[F1] MIRA VERDE OP</button>
    <button onclick="toggleNightVision(this)">[F2] NIGHT VISION (CONTRASTE)</button>
    <button onclick="toggleRadar(this)">[F3] RADAR DE PASSOS (PÁ)</button>
    <button onclick="toggleZoom(this)">[SCROLL] ZOOM DA MIRA</button>
    
    <div style="margin-top:10px; font-size:10px;">
        VOL SOM (10x): <span id="vLab">1x</span>
        <input type="range" min="1" max="10" value="1" style="width:100%" oninput="updateVol(this.value)">
    </div>
</div>

<script>
    // Mira e Zoom
    let zoomActive = false;
    let scale = 1.0;
    function toggleMira(btn) {
        const c = document.getElementById('crosshair-container');
        c.style.display = c.style.display === 'block' ? 'none' : 'block';
        btn.classList.toggle('active');
    }

    function toggleZoom(btn) {
        zoomActive = !zoomActive;
        btn.classList.toggle('active');
    }

    window.addEventListener('wheel', (e) => {
        if(!zoomActive) return;
        scale += e.deltaY < 0 ? 0.1 : -0.1;
        scale = Math.max(0.2, Math.min(4, scale));
        document.getElementById('crosshair-container').style.transform = `translate(-50%, -50%) scale(${scale})`;
    });

    // Night Vision (Melhora brilho e saturação)
    function toggleNightVision(btn) {
        const filter = document.getElementById('vision-filter');
        if(filter.style.backdropFilter === 'contrast(1.5) brightness(1.2)') {
            filter.style.backdropFilter = 'none';
            btn.classList.remove('active');
        } else {
            filter.style.backdropFilter = 'contrast(1.5) brightness(1.2)';
            btn.classList.add('active');
        }
    }

    // Som e Radar
    let radar;
    function toggleRadar(btn) {
        if(radar) { clearInterval(radar); radar = null; btn.classList.remove('active'); }
        else {
            btn.classList.add('active');
            radar = setInterval(() => {
                const a = new (window.AudioContext || window.webkitAudioContext)();
                const o = a.createOscillator();
                const g = a.createGain();
                o.connect(g); g.connect(a.destination);
                o.frequency.value = 100; g.gain.value = 0.02;
                o.start(); o.stop(a.currentTime + 0.1);
            }, 2000);
        }
    }

    function updateVol(v) { document.getElementById('vLab').innerText = v + 'x'; }

    // Função de Arrastar o Menu
    function dragElement(elmnt) {
        var pos1 = 0, pos2 = 0, pos3 = 0, pos4 = 0;
        elmnt.onmousedown = dragMouseDown;
        function dragMouseDown(e) { e.preventDefault(); pos3 = e.clientX; pos4 = e.clientY; document.onmouseup = closeDragElement; document.onmousemove = elementDrag; }
        function elementDrag(e) { e.preventDefault(); pos1 = pos3 - e.clientX; pos2 = pos4 - e.clientY; pos3 = e.clientX; pos4 = e.clientY; elmnt.style.top = (elmnt.offsetTop - pos2) + "px"; elmnt.style.left = (elmnt.offsetLeft - pos1) + "px"; }
        function closeDragElement() { document.onmouseup = null; document.onmousemove = null; }
    }
</script>
</body>
</html>
