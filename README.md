index.htmlfrom pathlib import Path
p=Path("/mnt/data/freeas-audio-index.html")
html=r'''<!doctype html>
<html lang="id">
<head>
<meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1">
<title>FreeAS Audio — Free Audio Tools</title>
<style>
:root{color-scheme:dark;--bg:#100b1e;--panel:#211433;--accent:#a66cff;--text:#f6efff;--muted:#b9a8ce}
*{box-sizing:border-box}body{margin:0;background:radial-gradient(circle at top,#2c1648,#100b1e 55%);font:16px system-ui;color:var(--text);min-height:100vh}
header{padding:26px 18px;text-align:center;border-bottom:1px solid #ffffff15}h1{margin:0;font-size:clamp(28px,7vw,42px)}h1 span{color:#c49aff}header p{color:var(--muted);margin:8px 0 0}
main{max-width:760px;margin:28px auto;padding:0 15px}.card{background:#211433dd;border:1px solid #ffffff17;border-radius:20px;padding:22px;box-shadow:0 18px 60px #0003}
.drop{border:2px dashed #7954a2;border-radius:16px;padding:28px 15px;text-align:center;background:#160e25;margin-bottom:18px}.drop strong{display:block;font-size:20px;margin-bottom:8px}.drop input{max-width:100%;margin-top:12px}
label{display:flex;justify-content:space-between;margin:18px 0 8px;color:#e8dafa}input[type=range]{width:100%;accent-color:var(--accent)}
button{background:linear-gradient(135deg,#a66cff,#7b42d5);border:0;color:white;font-weight:700;padding:13px 18px;border-radius:12px;cursor:pointer;width:100%;margin-top:12px}
button:disabled{opacity:.45}audio{width:100%;margin:12px 0}small,.note{color:var(--muted);line-height:1.5}footer{text-align:center;color:var(--muted);padding:25px}
</style>
</head>
<body>
<header><h1>FreeAS <span>Audio</span></h1><p>Free audio tools. Simple, fast, and accessible.</p></header>
<main><section class="card">
<div class="drop"><strong>🎵 Pilih file audio</strong><div>MP3, WAV, OGG, dan format audio browser lainnya</div><input id="file" type="file" accept="audio/*"></div>
<div id="filename" class="note">Belum ada file dipilih.</div><audio id="player" controls></audio>
<label for="speed">Kecepatan <span id="speedVal">1.00×</span></label><input id="speed" type="range" min=".25" max="2.7" step=".05" value="1">
<label for="volume">Volume <span id="volVal">100%</span></label><input id="volume" type="range" min="0" max="2" step=".01" value="1">
<button id="export" disabled>Ekspor WAV</button>
<p class="note">Pemrosesan dilakukan di browser perangkat kamu. Untuk file besar, proses mungkin memerlukan waktu dan memori.</p>
</section></main><footer>© <span id="year"></span> FreeAS Audio · Gratis digunakan</footer>
<script>
const file=document.querySelector('#file'),player=document.querySelector('#player'),speed=document.querySelector('#speed'),volume=document.querySelector('#volume'),exp=document.querySelector('#export');let audioBuffer=null,ctx=null;
document.querySelector('#year').textContent=new Date().getFullYear();
file.onchange=async()=>{const f=file.files[0];if(!f)return;player.src=URL.createObjectURL(f);document.querySelector('#filename').textContent=f.name;exp.disabled=false;try{ctx=new (window.AudioContext||window.webkitAudioContext)();audioBuffer=await ctx.decodeAudioData(await f.arrayBuffer())}catch(e){audioBuffer=null;alert('File tidak dapat dibaca untuk ekspor WAV. Coba file audio lain.')}};
speed.oninput=()=>{player.playbackRate=+speed.value;document.querySelector('#speedVal').textContent=(+speed.value).toFixed(2)+'×'};
volume.oninput=()=>{player.volume=Math.min(1,+volume.value);document.querySelector('#volVal').textContent=Math.round(+volume.value*100)+'%';};
exp.onclick=()=>{if(!audioBuffer)return alert('Pilih file audio yang valid dahulu.');const rate=+speed.value,channels=audioBuffer.numberOfChannels,srcLen=audioBuffer.length,outLen=Math.ceil(srcLen/rate),sr=audioBuffer.sampleRate;let data=Array.from({length:channels},(_,c)=>audioBuffer.getChannelData(c));let bytes=44+outLen*channels*2,buf=new ArrayBuffer(bytes),v=new DataView(buf),pos=0;
const str=s=>{for(let i=0;i<s.length;i++)v.setUint8(pos++,s.charCodeAt(i))};str('RIFF');v.setUint32(pos,bytes-8,true);pos+=4;str('WAVE');str('fmt ');v.setUint32(pos,16,true);pos+=4;v.setUint16(pos,1,true);pos+=2;v.setUint16(pos,channels,true);pos+=2;v.setUint32(pos,sr,true);pos+=4;v.setUint32(pos,sr*channels*2,true);pos+=4;v.setUint16(pos,channels*2,true);pos+=2;v.setUint16(pos,16,true);pos+=2;str('data');v.setUint32(pos,outLen*channels*2,true);pos+=4;
for(let i=0;i<outLen;i++)for(let c=0;c<channels;c++){let sample=data[c][Math.min(srcLen-1,Math.floor(i*rate))]*Math.min(1,+volume.value);sample=Math.max(-1,Math.min(1,sample));v.setInt16(pos,sample<0?sample*32768:sample*32767,true);pos+=2}
let a=document.createElement('a');a.href=URL.createObjectURL(new Blob([buf],{type:'audio/wav'}));a.download='FreeAS-Audio.wav';a.click();URL.revokeObjectURL(a.href)};
</script></body></html>'''
p.write_text(html)
print("File siap:",p, "bytes:",p.stat().st_size)
