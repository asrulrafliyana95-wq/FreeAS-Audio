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
from pathlib import Path
p=Path("/mnt/data/freeas-audio-index.html")
html='''<!doctype html><html lang="id"><head><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1"><title>FreeAS Audio</title><style>
body{margin:0;background:#130b20;color:#f5eaff;font:16px system-ui}header{text-align:center;padding:28px}h1{font-size:38px}h1 span{color:#b78aff}main{max-width:680px;margin:20px auto;padding:15px}.card{background:#241536;border:1px solid #493263;border-radius:18px;padding:22px}.drop{text-align:center;border:2px dashed #8057ad;padding:25px;border-radius:14px}audio{width:100%;margin:18px 0}label{display:flex;justify-content:space-between;margin:18px 0 8px}input[type=range]{width:100%;accent-color:#ac75ff}button{width:100%;padding:14px;background:#9658e8;color:white;border:0;border-radius:12px;font-weight:bold;margin-top:15px}small{color:#c4b2d7}footer{text-align:center;color:#b9a8ca;padding:25px}</style></head><body><header><h1>FreeAS <span>Audio</span></h1><p>Free audio tools • Simple, fast, accessible</p></header><main><section class="card"><div class="drop"><b>🎵 Pilih file audio</b><p>Upload audio dari perangkat kamu</p><input id="file" type="file" accept="audio/*"></div><p id="name">Belum ada file.</p><audio id="player" controls></audio><label>Kecepatan <span id="sv">1×</span></label><input id="speed" type="range" min=".25" max="2.7" step=".05" value="1"><label>Volume <span id="vv">100%</span></label><input id="vol" type="range" min="0" max="1" step=".01" value="1"><button id="export" disabled>Ekspor WAV</button><p><small>Konversi dilakukan di browser. Ini versi awal; fitur OGG dan login belum tersedia.</small></p></section></main><footer>© FreeAS Audio</footer><script>
let f=document.querySelector('#file'),a=document.querySelector('#player'),s=document.querySelector('#speed'),v=document.querySelector('#vol'),buf=null,ctx;
f.onchange=async()=>{let x=f.files[0];if(!x)return;a.src=URL.createObjectURL(x);document.querySelector('#name').textContent=x.name;try{ctx=new AudioContext();buf=await ctx.decodeAudioData(await x.arrayBuffer());document.querySelector('#export').disabled=false}catch(e){alert('Format file tidak didukung untuk ekspor WAV')}};
s.oninput=()=>{a.playbackRate=+s.value;document.querySelector('#sv').textContent=s.value+'×'};v.oninput=()=>{a.volume=+v.value;document.querySelector('#vv').textContent=Math.round(v.value*100)+'%'};
document.querySelector('#export').onclick=()=>{if(!buf)return;let rate=+s.value,ch=buf.numberOfChannels,n=Math.ceil(buf.length/rate),data=Array.from({length:ch},(_,i)=>buf.getChannelData(i)),b=new ArrayBuffer(44+n*ch*2),d=new DataView(b),o=0;function w(t){for(let c of t)d.setUint8(o++,c.charCodeAt(0))}w('RIFF');d.setUint32(o,n*ch*2+36,true);o+=4;w('WAVE');w('fmt ');d.setUint32(o,16,true);o+=4;d.setUint16(o,1,true);o+=2;d.setUint16(o,ch,true);o+=2;d.setUint32(o,buf.sampleRate,true);o+=4;d.setUint32(o,buf.sampleRate*ch*2,true);o+=4;d.setUint16(o,ch*2,true);o+=2;d.setUint16(o,16,true);o+=2;w('data');d.setUint32(o,n*ch*2,true);o+=4;for(let i=0;i<n;i++)for(let c=0;c<ch;c++){let z=Math.max(-1,Math.min(1,data[c][Math.min(buf.length-1,Math.floor(i*rate))]*+v.value));d.setInt16(o,z<0?z*32768:z*32767,true);o+=2}let link=document.createElement('a');link.href=URL.createObjectURL(new Blob([b],{type:'audio/wav'}));link.download='FreeAS-Audio.wav';link.click()};
</script></body></html>'''
p.write_text(html)
print(f"Created {p} ({p.stat().st_size} bytes)")
