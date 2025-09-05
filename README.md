# tuncrkabdulwallpaper
Abdulazim fotoğraflarından oluşan bir wallpaper
<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Abdulazim Duvar Kağıtları</title>
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: linear-gradient(135deg, #e0eafc, #cfdef3);
      display: flex;
      flex-direction: column;
      align-items: stretch;
      position: relative;
    }
    header {
      background: linear-gradient(90deg, #667eea, #764ba2);
      color: white;
      padding: 15px;
      text-align: center;
      box-shadow: 0 4px 6px rgba(0,0,0,0.2);
      position: relative;
      height: 100px;
    }
    header h1 {
      margin: 0;
      font-size: 2em;
      text-shadow: 1px 1px 2px rgba(0,0,0,0.5);
      line-height: 50px;
    }
    header p {
      margin: 5px 0 0 0;
      font-size: 1em;
      color: #f0f0f0;
    }
    #controls {
      margin: 20px;
      display: flex;
      gap: 10px;
      justify-content: center;
      flex-wrap: wrap;
    }
    #fileInput, #saveSelected {
      padding: 10px 15px;
      border-radius: 8px;
      border: none;
      background: #667eea;
      color: white;
      font-weight: bold;
      cursor: pointer;
      box-shadow: 0 2px 4px rgba(0,0,0,0.2);
      transition: transform 0.2s, background 0.2s;
    }
    #fileInput:hover, #saveSelected:hover {
      transform: scale(1.05);
      background: #5a67d8;
    }
    #canvas {
      border-radius: 0;
      border: none;
      background: #fff;
      width: 100%;
      height: calc(60vh);
    }
    #thumbnails {
      margin-top: 15px;
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      padding: 10px;
      background: #f5f5f5;
      box-shadow: inset 0 2px 4px rgba(0,0,0,0.1);
      overflow-y: auto;
      max-height: 35vh;
    }
    #thumbnails img {
      width: 150px;
      height: 150px;
      object-fit: cover;
      border-radius: 8px;
      border: 2px solid #ccc;
      cursor: pointer;
      box-shadow: 0 2px 4px rgba(0,0,0,0.2);
    }
    .thumbnail-wrapper {
      display: inline-block;
      position: relative;
      text-align: center;
    }
    .delete-btn {
      position: absolute;
      top: -5px;
      right: -5px;
      background: red;
      color: white;
      border: none;
      border-radius: 50%;
      width: 22px;
      height: 22px;
      cursor: pointer;
      font-size: 14px;
      line-height: 18px;
      text-align: center;
      box-shadow: 0 2px 4px rgba(0,0,0,0.3);
      transition: transform 0.2s;
    }
    .delete-btn:hover {
      transform: scale(1.2);
    }
    .checkbox {
      position: absolute;
      bottom: 5px;
      left: 5px;
      transform: scale(1.2);
    }
    .selected {
      border: 3px solid #4CAF50;
    }
  </style>
</head>
<body>
  <header>
    <h1>Abdulazim Duvar Kağıtları</h1>
    <p>Tunç RK tarafından oluşturulmuştur</p>
  </header>
  <div id="controls">
    <input type="file" id="fileInput" accept="image/*" multiple>
    <button id="saveSelected">Seçilenleri ZIP Olarak Kaydet</button>
  </div>
  <canvas id="canvas"></canvas>
  <div id="thumbnails"></div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.0/jszip.min.js"></script>
  <script>
    const fileInput = document.getElementById('fileInput');
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    const thumbnails = document.getElementById('thumbnails');
    const saveSelectedBtn = document.getElementById('saveSelected');

    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight * 0.6;

    window.addEventListener('resize', () => {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight * 0.6;
      drawCanvas();
    });

    let images = [];

    fileInput.addEventListener('change', (event) => {
      const files = Array.from(event.target.files);
      files.forEach(file => {
        const reader = new FileReader();
        reader.onload = (e) => {
          const img = new Image();
          img.src = e.target.result;
          img.onload = () => {
            const fixedHeight = 150;
            const scale = fixedHeight / img.height;
            const w = img.width * scale;
            const h = fixedHeight;

            let offsetX = 10;
            let offsetY = 10;
            if(images.length > 0){
              const last = images[images.length -1];
              offsetX = last.x + last.w + 10;
              offsetY = last.y;
              if(offsetX + w > canvas.width){
                offsetX = 10;
                offsetY = last.y + fixedHeight + 10;
              }
            }

            images.push({ img, x: offsetX, y: offsetY, w, h, selected: false, name: file.name });
            drawCanvas();
            updateThumbnails();
          };
        };
        reader.readAsDataURL(file);
      });
    });

    function drawCanvas() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      images.forEach(obj => {
        ctx.globalAlpha = 1;
        ctx.drawImage(obj.img, obj.x, obj.y, obj.w, obj.h);
      });
    }

    function updateThumbnails() {
      thumbnails.innerHTML = '';
      images.forEach((obj, index) => {
        const wrapper = document.createElement('div');
        wrapper.className = 'thumbnail-wrapper';
        const thumb = document.createElement('img');
        thumb.src = obj.img.src;
        if(obj.selected) thumb.classList.add('selected');
        wrapper.appendChild(thumb);

        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'checkbox';
        checkbox.checked = obj.selected;
        checkbox.addEventListener('change', () => {
          obj.selected = checkbox.checked;
          if(obj.selected) thumb.classList.add('selected');
          else thumb.classList.remove('selected');
        });
        wrapper.appendChild(checkbox);

        const delBtn = document.createElement('button');
        delBtn.className = 'delete-btn';
        delBtn.innerText = '×';
        delBtn.addEventListener('click', () => {
          images.splice(index, 1);
          drawCanvas();
          updateThumbnails();
        });
        wrapper.appendChild(delBtn);

        thumb.addEventListener('click', () => {
          obj.selected = !obj.selected;
          checkbox.checked = obj.selected;
          if(obj.selected) thumb.classList.add('selected');
          else thumb.classList.remove('selected');
        });

        thumbnails.appendChild(wrapper);
      });
    }

    saveSelectedBtn.addEventListener('click', async () => {
      const selectedImages = images.filter(img => img.selected);
      if(selectedImages.length === 0) return;

      const zip = new JSZip();

      for(const i of selectedImages){
        const canvasTmp = document.createElement('canvas');
        canvasTmp.width = i.w;
        canvasTmp.height = i.h;
        const ctxTmp = canvasTmp.getContext('2d');
        ctxTmp.drawImage(i.img, 0, 0, i.w, i.h);
        const dataUrl = canvasTmp.toDataURL('image/png');
        const base64 = dataUrl.split(',')[1];
        zip.file(i.name, base64, {base64: true});
      }

      const content = await zip.generateAsync({type: 'blob'});
      const link = document.createElement('a');
      link.href = URL.createObjectURL(content);
      link.download = 'secilen_duvarkagitlar.zip';
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    });
  </script>
</body>
</html>
