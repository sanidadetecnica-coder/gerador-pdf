# gerador-pdf
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Gerador de PDF — Sanidade Técnica</title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    background: #0d0d0d;
    color: #fff;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 40px 20px;
  }

  .logo {
    font-size: 11px;
    letter-spacing: 3px;
    text-transform: uppercase;
    color: #E8622A;
    margin-bottom: 40px;
    opacity: 0.8;
  }

  .card {
    background: #1a1a1a;
    border: 1px solid #2a2a2a;
    border-radius: 12px;
    padding: 40px;
    width: 100%;
    max-width: 560px;
  }

  h1 {
    font-size: 24px;
    font-weight: 700;
    margin-bottom: 8px;
    color: #fff;
  }

  .subtitle {
    font-size: 14px;
    color: rgba(255,255,255,0.4);
    margin-bottom: 32px;
    line-height: 1.6;
  }

  /* Drop zone */
  .drop-zone {
    border: 2px dashed #333;
    border-radius: 8px;
    padding: 48px 24px;
    text-align: center;
    cursor: pointer;
    transition: all 0.2s;
    background: rgba(255,255,255,0.02);
    position: relative;
  }

  .drop-zone:hover,
  .drop-zone.drag-over {
    border-color: #E8622A;
    background: rgba(232,98,42,0.05);
  }

  .drop-zone input[type="file"] {
    position: absolute;
    inset: 0;
    opacity: 0;
    cursor: pointer;
    width: 100%;
    height: 100%;
  }

  .drop-icon { font-size: 40px; margin-bottom: 12px; }

  .drop-title {
    font-size: 16px;
    font-weight: 600;
    color: #fff;
    margin-bottom: 6px;
  }

  .drop-sub {
    font-size: 13px;
    color: rgba(255,255,255,0.35);
  }

  /* File selected state */
  .file-info {
    display: none;
    align-items: center;
    gap: 14px;
    background: rgba(232,98,42,0.1);
    border: 1px solid rgba(232,98,42,0.3);
    border-radius: 8px;
    padding: 16px 20px;
    margin-top: 16px;
  }

  .file-info.show { display: flex; }

  .file-icon {
    width: 40px;
    height: 40px;
    background: #E8622A;
    border-radius: 8px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 18px;
    flex-shrink: 0;
  }

  .file-name {
    font-size: 14px;
    font-weight: 500;
    color: #fff;
    word-break: break-all;
  }

  .file-size {
    font-size: 12px;
    color: rgba(255,255,255,0.4);
    margin-top: 2px;
  }

  /* Options */
  .options {
    margin-top: 24px;
    display: flex;
    flex-direction: column;
    gap: 12px;
  }

  .option-row {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 16px;
  }

  .option-label {
    font-size: 13px;
    color: rgba(255,255,255,0.6);
  }

  select, input[type="text"] {
    background: #111;
    border: 1px solid #333;
    color: #fff;
    border-radius: 6px;
    padding: 8px 12px;
    font-size: 13px;
    outline: none;
    cursor: pointer;
    min-width: 140px;
  }

  select:focus, input[type="text"]:focus {
    border-color: #E8622A;
  }

  /* Button */
  .btn-gerar {
    width: 100%;
    background: #E8622A;
    color: white;
    border: none;
    padding: 16px;
    font-size: 16px;
    font-weight: 700;
    border-radius: 8px;
    cursor: pointer;
    margin-top: 24px;
    transition: all 0.2s;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 10px;
  }

  .btn-gerar:hover:not(:disabled) {
    background: #F2855A;
    transform: translateY(-1px);
  }

  .btn-gerar:disabled {
    opacity: 0.4;
    cursor: not-allowed;
    transform: none;
  }

  /* Progress */
  .progress-wrap {
    display: none;
    margin-top: 20px;
  }

  .progress-wrap.show { display: block; }

  .progress-label {
    font-size: 13px;
    color: rgba(255,255,255,0.5);
    margin-bottom: 8px;
    display: flex;
    justify-content: space-between;
  }

  .progress-bar {
    height: 4px;
    background: #222;
    border-radius: 2px;
    overflow: hidden;
  }

  .progress-fill {
    height: 100%;
    background: #E8622A;
    border-radius: 2px;
    width: 0%;
    transition: width 0.3s ease;
  }

  .status-msg {
    font-size: 13px;
    color: rgba(255,255,255,0.4);
    margin-top: 10px;
    min-height: 20px;
    font-style: italic;
  }

  /* Success */
  .success-box {
    display: none;
    background: rgba(42,122,75,0.1);
    border: 1px solid rgba(42,122,75,0.3);
    border-radius: 8px;
    padding: 16px 20px;
    margin-top: 20px;
    align-items: center;
    gap: 14px;
  }

  .success-box.show { display: flex; }

  .success-icon { font-size: 24px; }
  .success-text { font-size: 14px; color: rgba(255,255,255,0.75); }

  /* Error */
  .error-box {
    display: none;
    background: rgba(192,57,43,0.1);
    border: 1px solid rgba(192,57,43,0.3);
    border-radius: 8px;
    padding: 16px 20px;
    margin-top: 20px;
    font-size: 13px;
    color: #f87171;
    line-height: 1.5;
  }

  .error-box.show { display: block; }

  /* How to use */
  .how-to {
    margin-top: 32px;
    padding-top: 24px;
    border-top: 1px solid #1f1f1f;
  }

  .how-to-title {
    font-size: 11px;
    letter-spacing: 2px;
    text-transform: uppercase;
    color: rgba(255,255,255,0.25);
    margin-bottom: 14px;
  }

  .steps {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  .step {
    display: flex;
    align-items: flex-start;
    gap: 10px;
    font-size: 13px;
    color: rgba(255,255,255,0.45);
    line-height: 1.5;
  }

  .step-num {
    width: 20px;
    height: 20px;
    background: #222;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 10px;
    color: #E8622A;
    flex-shrink: 0;
    margin-top: 1px;
  }

  /* Preview iframe */
  #preview-container {
    position: fixed;
    left: -9999px;
    top: 0;
    width: 210mm;
    background: white;
    visibility: hidden;
  }

  #preview-frame {
    width: 210mm;
    border: none;
    display: block;
  }

  .spinner {
    width: 18px;
    height: 18px;
    border: 2px solid rgba(255,255,255,0.3);
    border-top-color: white;
    border-radius: 50%;
    animation: spin 0.7s linear infinite;
  }

  @keyframes spin { to { transform: rotate(360deg); } }
</style>
</head>
<body>

<div class="logo">✓ Sanidade Técnica · Ferramentas</div>

<div class="card">
  <h1>Gerador de PDF</h1>
  <p class="subtitle">Faça upload do arquivo HTML e baixe o PDF com diagramação perfeita — fontes, cores e layout preservados.</p>

  <!-- Drop zone -->
  <div class="drop-zone" id="drop-zone">
    <input type="file" id="file-input" accept=".html,.htm">
    <div class="drop-icon">📄</div>
    <div class="drop-title">Arraste o arquivo HTML aqui</div>
    <div class="drop-sub">ou clique para selecionar</div>
  </div>

  <!-- File info -->
  <div class="file-info" id="file-info">
    <div class="file-icon">📄</div>
    <div>
      <div class="file-name" id="file-name">—</div>
      <div class="file-size" id="file-size">—</div>
    </div>
  </div>

  <!-- Options -->
  <div class="options">
    <div class="option-row">
      <span class="option-label">Tamanho da página</span>
      <select id="page-size">
        <option value="a4" selected>A4 (210 × 297mm)</option>
        <option value="letter">Letter (216 × 279mm)</option>
        <option value="a3">A3 (297 × 420mm)</option>
      </select>
    </div>
    <div class="option-row">
      <span class="option-label">Orientação</span>
      <select id="orientation">
        <option value="portrait" selected>Retrato (vertical)</option>
        <option value="landscape">Paisagem (horizontal)</option>
      </select>
    </div>
    <div class="option-row">
      <span class="option-label">Escala</span>
      <select id="scale">
        <option value="1" selected>100% (sem escala)</option>
        <option value="0.9">90%</option>
        <option value="0.8">80%</option>
        <option value="0.75">75%</option>
        <option value="0.7">70%</option>
      </select>
    </div>
    <div class="option-row">
      <span class="option-label">Margem (mm)</span>
      <select id="margin">
        <option value="0" selected>Nenhuma (0mm)</option>
        <option value="5">Mínima (5mm)</option>
        <option value="10">Normal (10mm)</option>
        <option value="15">Ampla (15mm)</option>
      </select>
    </div>
    <div class="option-row">
      <span class="option-label">Nome do arquivo</span>
      <input type="text" id="filename" placeholder="documento" value="documento">
    </div>
  </div>

  <!-- Button -->
  <button class="btn-gerar" id="btn-gerar" disabled onclick="gerarPDF()">
    <span id="btn-text">Selecione um arquivo primeiro</span>
  </button>

  <!-- Progress -->
  <div class="progress-wrap" id="progress-wrap">
    <div class="progress-label">
      <span id="progress-text">Preparando...</span>
      <span id="progress-pct">0%</span>
    </div>
    <div class="progress-bar">
      <div class="progress-fill" id="progress-fill"></div>
    </div>
    <div class="status-msg" id="status-msg"></div>
  </div>

  <!-- Success -->
  <div class="success-box" id="success-box">
    <div class="success-icon">✅</div>
    <div class="success-text">PDF gerado com sucesso! O download começou automaticamente.</div>
  </div>

  <!-- Error -->
  <div class="error-box" id="error-box"></div>

  <!-- How to use -->
  <div class="how-to">
    <div class="how-to-title">Como usar</div>
    <div class="steps">
      <div class="step"><div class="step-num">1</div>Faça upload do arquivo .html do seu e-book ou guia</div>
      <div class="step"><div class="step-num">2</div>Escolha tamanho, orientação, escala e margem</div>
      <div class="step"><div class="step-num">3</div>Clique em "Gerar PDF" e aguarde — pode levar 10 a 30 segundos</div>
      <div class="step"><div class="step-num">4</div>O PDF é baixado automaticamente com layout perfeito</div>
    </div>
  </div>
</div>

<!-- Hidden preview container -->
<div id="preview-container">
  <iframe id="preview-frame"></iframe>
</div>

<!-- Libraries -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

<script>
let htmlContent = null;
let fileName = 'documento';

// ===== FILE HANDLING =====
const dropZone = document.getElementById('drop-zone');
const fileInput = document.getElementById('file-input');

dropZone.addEventListener('dragover', (e) => {
  e.preventDefault();
  dropZone.classList.add('drag-over');
});

dropZone.addEventListener('dragleave', () => {
  dropZone.classList.remove('drag-over');
});

dropZone.addEventListener('drop', (e) => {
  e.preventDefault();
  dropZone.classList.remove('drag-over');
  const file = e.dataTransfer.files[0];
  if (file) processFile(file);
});

fileInput.addEventListener('change', (e) => {
  const file = e.target.files[0];
  if (file) processFile(file);
});

function processFile(file) {
  if (!file.name.match(/\.html?$/i)) {
    showError('Por favor, selecione um arquivo .html ou .htm');
    return;
  }

  const reader = new FileReader();
  reader.onload = (e) => {
    htmlContent = e.target.result;
    
    // Update UI
    document.getElementById('file-name').textContent = file.name;
    document.getElementById('file-size').textContent = formatBytes(file.size);
    document.getElementById('file-info').classList.add('show');
    
    // Set filename from file
    const baseName = file.name.replace(/\.html?$/i, '');
    document.getElementById('filename').value = baseName;
    
    // Enable button
    const btn = document.getElementById('btn-gerar');
    btn.disabled = false;
    document.getElementById('btn-text').textContent = 'Gerar PDF';
    
    hideError();
    document.getElementById('success-box').classList.remove('show');
  };
  reader.readAsText(file);
}

function formatBytes(bytes) {
  if (bytes < 1024) return bytes + ' bytes';
  if (bytes < 1024 * 1024) return (bytes / 1024).toFixed(1) + ' KB';
  return (bytes / (1024 * 1024)).toFixed(1) + ' MB';
}

// ===== PDF GENERATION =====
async function gerarPDF() {
  if (!htmlContent) return;

  const btn = document.getElementById('btn-gerar');
  btn.disabled = true;
  document.getElementById('btn-text').innerHTML = '<div class="spinner"></div> Gerando...';

  hideError();
  document.getElementById('success-box').classList.remove('show');
  showProgress(true);

  try {
    const pageSize = document.getElementById('page-size').value;
    const orientation = document.getElementById('orientation').value;
    const scale = parseFloat(document.getElementById('scale').value);
    const margin = parseInt(document.getElementById('margin').value);
    const filename = document.getElementById('filename').value || 'documento';

    // Page dimensions in mm
    const pageDims = {
      a4:     { w: 210, h: 297 },
      letter: { w: 216, h: 279 },
      a3:     { w: 297, h: 420 }
    };

    let { w: pageW, h: pageH } = pageDims[pageSize];
    if (orientation === 'landscape') [pageW, pageH] = [pageH, pageW];

    // mm to px at 96dpi
    const mmToPx = 96 / 25.4;
    const pageWpx = pageW * mmToPx;

    updateProgress(10, 'Carregando o documento...');
    setStatus('Renderizando HTML com fontes e estilos...');

    // Create a hidden iframe to render the HTML
    const iframe = document.createElement('iframe');
    iframe.style.cssText = `
      position: fixed;
      left: -9999px;
      top: 0;
      width: ${pageWpx}px;
      height: 1px;
      border: none;
      visibility: hidden;
    `;
    document.body.appendChild(iframe);

    // Write HTML into iframe
    const iframeDoc = iframe.contentDocument || iframe.contentWindow.document;
    iframeDoc.open();
    iframeDoc.write(htmlContent);
    iframeDoc.close();

    // Wait for iframe to load including fonts
    await new Promise(resolve => {
      iframe.onload = resolve;
      setTimeout(resolve, 3000); // fallback
    });

    updateProgress(25, 'Aguardando fontes carregarem...');
    setStatus('Carregando fontes Google Fonts...');

    // Wait for fonts inside iframe
    try {
      if (iframe.contentDocument.fonts) {
        await iframe.contentDocument.fonts.ready;
      }
    } catch(e) {}

    // Extra wait to ensure everything renders
    await sleep(2000);

    updateProgress(40, 'Calculando páginas...');
    setStatus('Medindo conteúdo...');

    const iframeBody = iframeDoc.body;
    const totalHeight = iframeBody.scrollHeight;
    const pageHpx = pageH * mmToPx;
    const contentHeight = pageHpx - (margin * 2 * mmToPx);
    const totalPages = Math.ceil(totalHeight / contentHeight);

    updateProgress(50, `Renderizando ${totalPages} páginas...`);
    setStatus(`Capturando ${totalPages} páginas em alta resolução...`);

    // Init jsPDF
    const { jsPDF } = window.jspdf;
    const pdf = new jsPDF({
      orientation: orientation === 'landscape' ? 'l' : 'p',
      unit: 'mm',
      format: pageSize === 'letter' ? [pageW, pageH] : pageSize,
      compress: true
    });

    const imgW = pageW - (margin * 2);

    // Capture the entire content as one tall canvas, then slice into pages
    updateProgress(55, 'Capturando canvas...');
    setStatus('Renderizando em alta qualidade (pode demorar)...');

    const canvas = await html2canvas(iframeDoc.body, {
      scale: 2, // 2x for retina quality
      useCORS: true,
      allowTaint: true,
      backgroundColor: null,
      width: pageWpx,
      scrollX: 0,
      scrollY: 0,
      windowWidth: pageWpx,
      logging: false,
      onclone: (doc) => {
        // Ensure print styles are applied
        doc.body.style.width = pageWpx + 'px';
      }
    });

    updateProgress(80, 'Construindo PDF...');
    setStatus('Montando páginas no PDF...');

    const canvasW = canvas.width;
    const canvasH = canvas.height;
    const pageCanvasH = (pageHpx / pageWpx) * canvasW;
    const numPages = Math.ceil(canvasH / pageCanvasH);

    for (let i = 0; i < numPages; i++) {
      if (i > 0) pdf.addPage();

      const srcY = i * pageCanvasH;
      const srcH = Math.min(pageCanvasH, canvasH - srcY);

      // Create a slice canvas
      const sliceCanvas = document.createElement('canvas');
      sliceCanvas.width = canvasW;
      sliceCanvas.height = pageCanvasH;
      const ctx = sliceCanvas.getContext('2d');

      // Fill white background
      ctx.fillStyle = '#ffffff';
      ctx.fillRect(0, 0, canvasW, pageCanvasH);

      // Draw the slice
      ctx.drawImage(canvas, 0, srcY, canvasW, srcH, 0, 0, canvasW, srcH);

      const imgData = sliceCanvas.toDataURL('image/jpeg', 0.92);
      const imgH = (srcH / canvasW) * imgW;

      pdf.addImage(imgData, 'JPEG', margin, margin, imgW, imgH, '', 'FAST');

      const pct = 80 + Math.round((i + 1) / numPages * 18);
      updateProgress(pct, `Página ${i + 1} de ${numPages}...`);
    }

    updateProgress(99, 'Salvando arquivo...');
    setStatus('Finalizando...');

    await sleep(300);
    pdf.save(filename + '.pdf');

    // Cleanup
    document.body.removeChild(iframe);

    updateProgress(100, 'Concluído!');
    setStatus('');
    document.getElementById('success-box').classList.add('show');

  } catch (err) {
    console.error(err);
    showError('Erro ao gerar o PDF: ' + err.message + '\n\nTente com uma escala menor (70-80%) se o arquivo for muito grande.');
  } finally {
    btn.disabled = false;
    document.getElementById('btn-text').textContent = 'Gerar PDF novamente';
    showProgress(false);
  }
}

// ===== HELPERS =====
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

function showProgress(show) {
  document.getElementById('progress-wrap').classList.toggle('show', show);
  if (!show) {
    updateProgress(0, '');
    setStatus('');
  }
}

function updateProgress(pct, text) {
  document.getElementById('progress-fill').style.width = pct + '%';
  document.getElementById('progress-pct').textContent = pct + '%';
  if (text) document.getElementById('progress-text').textContent = text;
}

function setStatus(msg) {
  document.getElementById('status-msg').textContent = msg;
}

function showError(msg) {
  const el = document.getElementById('error-box');
  el.textContent = msg;
  el.classList.add('show');
}

function hideError() {
  document.getElementById('error-box').classList.remove('show');
}
</script>
</body>
</html>
