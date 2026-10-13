<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Güçlü AL Sistemi Pro</title>
<style>
*{box-sizing:border-box}
body{font-family:system-ui,sans-serif;margin:0;background:#0f1218;color:#e6e8ee;padding:12px}
input,button,select{padding:10px;border:0;border-radius:6px;font-size:14px}
input,select{background:#1d2330;color:#fff;max-width:100%}
button{background:#2ebd85;color:#07130e;font-weight:700;cursor:pointer}
button:disabled{opacity:.5}
h3{margin:18px 0 8px}
small,.muted{color:#929caf}
.toolbar{display:flex;gap:7px;flex-wrap:wrap;margin:12px 0}
.toolbar input{width:125px}
.cards{display:grid;grid-template-columns:repeat(4,minmax(0,1fr));gap:7px;margin:12px 0}
.card{background:#1d2330;padding:10px;border-radius:8px;min-width:0}
.card small{display:block}
.card b{display:block;font-size:17px;overflow-wrap:anywhere;margin-top:3px}
.panel{background:#171c26;border-radius:9px;padding:10px;margin:10px 0}
.good{color:#2ebd85}.bad{color:#f6465d}.yellow{color:#f0b90b}
table{width:100%;border-collapse:collapse;font-size:12px;white-space:nowrap}
th,td{padding:8px 6px;border-bottom:1px solid #292f3b;text-align:left}
th{color:#aeb8cb;position:sticky;top:0;background:#171c26}
.scroll{overflow:auto;max-height:520px}
canvas{width:100%;height:300px;background:#151a24;border-radius:8px}
.progress{height:6px;background:#292f3b;border-radius:5px;overflow:hidden}
#bar{height:100%;width:0;background:#2ebd85}
.note{font-size:12px;line-height:1.6;color:#aab3c4}
.tag{padding:3px 6px;border-radius:4px;background:#252c3a}
</style>
</head>
<body>
<b>Güçlü AL Sistemi Pro · Binance Spot</b>
<div class="note">4 saatlik EMA200 filtresi + 1 saatlik teknik analiz · Kaldıraçsız</div>

<div class="toolbar">
<input id="sym" value="BTCUSDT" aria-label="Coin">
<button id="analyze" onclick="run()">Coin Analiz</button>
<button id="scan" onclick="scan()">Tüm Coinleri Tara</button>
</div>

<div id="status" class="panel muted">Hazır. Analiz veya tarama başlat.</div>
<div class="progress"><div id="bar"></div></div>
<div class="cards" id="stats"></div>

<h3>Teknik değerlendirme</h3>
<div id="detail" class="panel muted">Analiz sonucu burada gösterilecek.</div>

<canvas id="cv"></canvas>

<h3>Son işlemler · Komisyon sonrası</h3>
<div class="scroll">
<table id="tr"></table>
</div>

<h3>Binance Spot coin taraması</h3>
<div class="toolbar">
<input id="search" placeholder="Coin ara..." oninput="renderScan()">
<select id="filter" onchange="renderScan()">
<option value="ALL">Tüm sonuçlar</option>
<option value="GÜÇLÜ AL">GÜÇLÜ AL</option>
<option value="AL">AL</option>
<option value="BEKLE">BEKLE</option>
<option value="UYGUN DEĞİL">UYGUN DEĞİL</option>
<option value="SAT">SAT</option>
<option value="GÜÇLÜ SAT">GÜÇLÜ SAT</option>
</select>
</div>
<div class="scroll">
<table id="sc"></table>
</div>

<div class="note">
<br><b>Sistem kuralları</b><br>
• 4 saatlik kapanış EMA200 üzerinde olmalı.<br>
• 1 saatlik EMA20, EMA50 üzerinde olmalı; fiyat EMA50 üzerinde olmalı.<br>
• MACD, RSI ve ADX trend teyidi sağlar.<br>
• Giriş: 20 mumluk tepe kırılımı veya EMA20'ye geri çekilme.<br>
• Yatay piyasa ve geç giriş riski elenir.<br>
• Stop: 1,5 ATR. Hedef: 2 ATR.<br>
• Alım/satım komisyonu varsayımı: her işlem yönünde %0,25.<br>
• 24 saatlik hacim bilgi amaçlıdır; alım sinyali filtresi değildir.<br>
• Yalnızca kapanmış mumlar kullanılır. Sonuçlar gelecek performansını garanti etmez.
</div>

<script>
"use strict";
const $=id=>document.getElementById(id);
const FEE=.0025;
const HOUR=3600000;
let scanRows=[],busy=false;

const ema=(a,n)=>{
 const out=Array(a.length).fill(NaN),k=2/(n+1);
 a.forEach((v,i)=>out[i]=i===0?v:v*k+out[i-1]*(1-k));
 return out;
};
const sma=(a,n)=>a.map((_,i)=>{
 if(i<n-1)return NaN;
 let s=0;for(let j=i-n+1;j<=i;j++)s+=a[j];
 return s/n;
});
const rma=(a,n)=>{
 const out=Array(a.length).fill(NaN);
 let sum=0,prev=NaN;
 for(let i=0;i<a.length;i++){
  if(!Number.isFinite(a[i]))continue;
  if(i<n){sum+=a[i];if(i===n-1)prev=sum/n;}
  else prev=(prev*(n-1)+a[i])/n;
  if(i>=n-1)out[i]=prev;
 }
 return out;
};
function rsi(c,n=14){
 const g=[],l=[];
 for(let i=0;i<c.length;i++){
  const d=i?c[i]-c[i-1]:0;
  g.push(Math.max(d,0));l.push(Math.max(-d,0));
 }
 const ag=rma(g,n),al=rma(l,n);
 return c.map((_,i)=>!Number.isFinite(ag[i])?NaN:
  al[i]===0?100:100-100/(1+ag[i]/al[i]));
}
function atr(k,n=14){
 const tr=k.map((x,i)=>i===0?x.h-x.l:
 Math.max(x.h-x.l,Math.abs(x.h-k[i-1].c),Math.abs(x.l-k[i-1].c)));
 return rma(tr,n);
}
function macd(c){
 const a=ema(c,12),b=ema(c,26);
 const line=c.map((_,i)=>a[i]-b[i]);
 const sig=ema(line,9);
 return {line,sig,hist:line.map((v,i)=>v-sig[i])};
}
function adx(k,n=14){
 const tr=[],p=[],m=[];
 for(let i=0;i<k.length;i++){
  if(!i){tr.push(k[i].h-k[i].l);p.push(0);m.push(0);continue;}
  const up=k[i].h-k[i-1].h,down=k[i-1].l-k[i].l;
  p.push(up>down&&up>0?up:0);
  m.push(down>up&&down>0?down:0);
  tr.push(Math.max(k[i].h-k[i].l,
   Math.abs(k[i].h-k[i-1].c),Math.abs(k[i].l-k[i-1].c)));
 }
 const rt=rma(tr,n),rp=rma(p,n),rm=rma(m,n);
 const dx=k.map((_,i)=>{
  if(!rt[i]||!Number.isFinite(rt[i]))return NaN;
  const a=100*rp[i]/rt[i],b=100*rm[i]/rt[i];
  return a+b?100*Math.abs(a-b)/(a+b):0;
 });
 return {adx:rma(dx,n),plus:rp.map((v,i)=>rt[i]?100*v/rt[i]:NaN),
 minus:rm.map((v,i)=>rt[i]?100*v/rt[i]:NaN)};
}
function indicators(k){
 const c=k.map(x=>x.c);
 const e20=ema(c,20),e50=ema(c,50),e100=ema(c,100),
 e200=ema(c,200),r=rsi(c),a=atr(k),m=macd(c),d=adx(k);
 const mid=sma(c,20);
 const dev=c.map((_,i)=>{
  if(i<19)return NaN;
  let sum=0;
  for(let j=i-19;j<=i;j++)sum+=(c[j]-mid[i])**2;
  return Math.sqrt(sum/20);
 });
 const bbw=c.map((_,i)=>mid[i]?4*dev[i]/mid[i]*100:NaN);
 const vs=sma(k.map(x=>x.v),20);
 return {e20,e50,e100,e200,r,a,m,d,mid,bbw,vs};
}
async function getJSON(url){
 let last;
 for(let n=0;n<4;n++){
  try{
   const r=await fetch(url);
   if(r.status===429||r.status===418){
    await new Promise(ok=>setTimeout(ok,1500*(n+1)));
    continue;
   }
   if(!r.ok)throw Error("Binance API HTTP "+r.status);
   return await r.json();
  }catch(e){last=e;await new Promise(ok=>setTimeout(ok,400*(n+1)));}
 }
 throw last||Error("Veri alınamadı");
}
async function load(symbol,interval,limit=500){
 const j=await getJSON("https://api.binance.com/api/v3/klines?symbol="+
 encodeURIComponent(symbol)+"&interval="+interval+"&limit="+limit);
 const now=Date.now();
 return j.filter(x=>x[6]<now).map(x=>({
  t:x[0],o:+x[1],h:+x[2],l:+x[3],c:+x[4],v:+x[5],ct:x[6]
 }));
}
function makeSignals(k,h4){
 const I=indicators(k),H=indicators(h4),n=k.length;
 I.buy=Array(n).fill(false);
 I.sell=Array(n).fill(false);
 I.score=Array(n).fill(0);
 I.state=Array(n).fill("BEKLE");
 I.htf=Array(n).fill(false);
 I.sideways=Array(n).fill(false);
 I.late=Array(n).fill(false);
 I.trigger=Array(n).fill(false);

 for(let i=200;i<n;i++){
  // Her 1 saatlik kapanışta yalnızca kapanmış 4 saatlik mum.
  let hi=-1;
  for(let z=h4.length-1;z>=0;z--){
   if(h4[z].ct<=k[i].ct){hi=z;break;}
  }
  if(hi<199)continue;
  const x=k[i],c=x.c;
  const htf=h4[hi].c>H.e200[hi];
  I.htf[i]=htf;
  const trend=c>I.e50[i]&&I.e20[i]>I.e50[i];
  const macdOK=I.m.line[i]>I.m.sig[i]&&I.m.hist[i]>0;
  const rsiOK=I.r[i]>=48&&I.r[i]<=68;
  const adxOK=I.d.adx[i]>=20;
  let high=-Infinity;
  for(let z=i-20;z<i;z++)high=Math.max(high,k[z].h);
  const breakout=c>high;
  const pullback=Math.abs(c-I.e20[i])/I.e20[i]<=.012&&
   x.l<=I.e20[i]*1.012&&c>I.e20[i]&&c>x.o;
  const trigger=breakout||pullback;
  I.trigger[i]=trigger;
  const slope=i>=10?(I.e20[i]/I.e20[i-10]-1)*100:999;
  const sideways=I.d.adx[i]<17&&Math.abs(slope)<.15&&I.bbw[i]<3;
  const late=(c/I.e20[i]-1)*100>2.5||I.r[i]>=72;
  I.sideways[i]=sideways;I.late[i]=late;

  let score=0;
  if(htf)score+=25;
  if(trend)score+=20;
  if(macdOK)score+=15;
  if(rsiOK)score+=15;
  if(adxOK)score+=10;
  if(trigger)score+=15;
  I.score[i]=score;

  const bearish=c<I.e50[i]&&I.e20[i]<I.e50[i];
  const macdBear=I.m.line[i]<I.m.sig[i]&&I.m.hist[i]<0;
  const weakRSI=I.r[i]<45;
  const weakADX=I.d.adx[i]>=20;
  const sellScore=(bearish?1:0)+(macdBear?1:0)+
   (weakRSI?1:0)+(weakADX?1:0);
  if(bearish&&sellScore>=4)I.state[i]="GÜÇLÜ SAT";
  else if(bearish&&sellScore>=3)I.state[i]="SAT";
  else if(!htf||sideways||late)I.state[i]="UYGUN DEĞİL";
  else if(trigger&&score>=80)I.state[i]="GÜÇLÜ AL";
  else if(trigger&&score>=65)I.state[i]="AL";
  else I.state[i]="BEKLE";

  I.buy[i]=I.state[i]==="AL"||I.state[i]==="GÜÇLÜ AL";
  I.sell[i]=I.state[i]==="SAT"||I.state[i]==="GÜÇLÜ SAT";
 }
 return I;
}
function backtest(k,I){
 const T=[];let i=200;
 while(i<k.length){
  if(!I.buy[i]||(i>200&&I.buy[i-1])){i++;continue;}
  const en=k[i].c,a=I.a[i];
  if(!Number.isFinite(a)||a<=0){i++;continue;}
  const sl=en-1.5*a,tp=en+2*a;
  let j=i+1,ex=null,why="Açık";
  for(;j<k.length;j++){
   // Aynı mumda stop ve hedef görülürse temkinli biçimde stop varsayılır.
   if(k[j].l<=sl){ex=sl;why="STOP";break;}
   if(k[j].h>=tp){ex=tp;why="HEDEF";break;}
   if(I.sell[j]){ex=k[j].c;why=I.state[j];break;}
  }
  if(ex===null){j=k.length-1;ex=k[j].c;why="Açık";}
  const gross=ex/en;
  const net=(gross*(1-FEE)*(1-FEE)-1)*100;
  T.push({i,j,en,ex,why,p:net,open:why==="Açık"});
  i=Math.max(i+1,j+1);
 }
 return T;
}
function fmt(v){
 if(!Number.isFinite(v))return "—";
 return v>=1000?v.toLocaleString("en-US",{maximumFractionDigits:2}):
 v.toPrecision(6);
}
function cls(s){
 if(s==="AL"||s==="GÜÇLÜ AL")return "good";
 if(s==="SAT"||s==="GÜÇLÜ SAT")return "bad";
 if(s==="UYGUN DEĞİL")return "bad";
 return "yellow";
}
function draw(k,I,T){
 const cv=$("cv"),d=window.devicePixelRatio||1;
 const W=cv.clientWidth,H=cv.clientHeight;
 if(!W||!H)return;
 cv.width=W*d;cv.height=H*d;
 const x=cv.getContext("2d");x.scale(d,d);
 const N=180,s=Math.max(0,k.length-N),sl=k.slice(s);
 const mn=Math.min(...sl.map(a=>a.l)),mx=Math.max(...sl.map(a=>a.h));
 const span=mx-mn||1;
 const Y=p=>H-8-(p-mn)/span*(H-16),w=W/sl.length;
 sl.forEach((a,i)=>{
  const X=i*w+w/2;
  x.strokeStyle=x.fillStyle=a.c>=a.o?"#2ebd85":"#f6465d";
  x.beginPath();x.moveTo(X,Y(a.h));x.lineTo(X,Y(a.l));x.stroke();
  x.fillRect(X-w*.32,Math.min(Y(a.o),Y(a.c)),Math.max(1,w*.64),
   Math.max(1,Math.abs(Y(a.o)-Y(a.c))));
 });
 [["e20","#43b581"],["e50","#f0b90b"],["e200","#6678ff"]].forEach(([name,color])=>{
  x.strokeStyle=color;x.lineWidth=1.2;x.beginPath();
  sl.forEach((_,i)=>{
   const p=I[name][s+i];
   if(!Number.isFinite(p))return;
   if(i===0)x.moveTo(w/2,Y(p));else x.lineTo(i*w+w/2,Y(p));
  });x.stroke();
 });
 T.forEach(t=>{
  if(t.i>=s){
   const X=(t.i-s)*w+w/2;
   x.fillStyle="#2ebd85";x.fillRect(X-10,Y(k[t.i].l)+5,20,13);
   x.fillStyle="#07130e";x.font="9px sans-serif";x.fillText("AL",X-6,Y(k[t.i].l)+15);
  }
  if(t.j>=s&&!t.open){
   const X=(t.j-s)*w+w/2;
   x.fillStyle="#f6465d";x.fillRect(X-12,Y(k[t.j].h)-18,24,13);
   x.fillStyle="#fff";x.fillText("ÇIK",X-8,Y(k[t.j].h)-8);
  }
 });
}
function renderTrade(k,T){
 const closed=T.filter(t=>!t.open);
 const wins=closed.filter(t=>t.p>0).length;
 const sum=closed.reduce((a,t)=>a+t.p,0);
 const avg=closed.length?sum/closed.length:0;
 const win=closed.length?wins/closed.length*100:0;
 const grossWins=closed.filter(t=>t.p>0).reduce((a,t)=>a+t.p,0);
 const grossLoss=Math.abs(closed.filter(t=>t.p<0).reduce((a,t)=>a+t.p,0));
 const pf=grossLoss?grossWins/grossLoss:NaN;
 $("stats").innerHTML=`
 <div class="card"><small>Kapanmış işlem</small><b>${closed.length}</b></div>
 <div class="card"><small>Başarı oranı</small><b>${win.toFixed(1)}%</b></div>
 <div class="card"><small>Ortalama net işlem</small><b class="${avg>=0?"good":"bad"}">${avg.toFixed(2)}%</b></div>
 <div class="card"><small>İşlem yüzdeleri toplamı</small><b class="${sum>=0?"good":"bad"}">${sum.toFixed(2)}%</b></div>`;
 $("tr").innerHTML="<tr><th>Giriş zamanı</th><th>Giriş</th><th>Çıkış</th><th>Çıkış fiyatı</th><th>Net sonuç</th></tr>"+
 T.slice(-30).reverse().map(t=>`<tr>
 <td>${new Date(k[t.i].ct).toLocaleString("tr-TR")}</td>
 <td>${fmt(t.en)}</td><td>${t.why}</td><td>${fmt(t.ex)}</td>
 <td class="${t.p>=0?"good":"bad"}">${t.p.toFixed(2)}%${t.open?"*":""}</td>
 </tr>`).join("");
 if(!T.length)$("tr").innerHTML="<tr><td>Yeterli sinyal oluşmadı.</td></tr>";
}
async function run(){
 if(busy)return;
 const s=$("sym").value.trim().toUpperCase();
 if(!/^[A-Z0-9]{5,25}$/.test(s)){alert("Coin sembolünü kontrol et.");return;}
 $("status").textContent=s+" verileri yükleniyor...";
 try{
  const [k,h4]=await Promise.all([load(s,"1h",500),load(s,"4h",260)]);
  if(k.length<220||h4.length<205)throw Error("EMA200 için yeterli mum verisi yok.");
  const I=makeSignals(k,h4),T=backtest(k,I),n=k.length-1;
  draw(k,I,T);renderTrade(k,T);
  const state=I.state[n],idx=h4.findLastIndex(x=>x.ct<=k[n].ct);
  const htf=idx>=0&&h4[idx].c>indicators(h4).e200[idx];
  $("status").innerHTML=`<b>${s}</b> · Son kapanış: ${fmt(k[n].c)} · Durum:
  <b class="${cls(state)}">${state}</b>`;
  $("detail").innerHTML=`
  <div class="cards">
  <div class="card"><small>Sistem puanı</small><b>${I.score[n]}/100</b></div>
  <div class="card"><small>4s EMA200 filtresi</small><b class="${htf?"good":"bad"}">${htf?"OLUMLU":"OLUMSUZ"}</b></div>
  <div class="card"><small>RSI / ADX</small><b>${I.r[n].toFixed(1)} / ${I.d.adx[n].toFixed(1)}</b></div>
  <div class="card"><small>ATR(14)</small><b>${fmt(I.a[n])}</b></div>
  </div>
  <p>EMA20: ${fmt(I.e20[n])} · EMA50: ${fmt(I.e50[n])} · EMA200: ${fmt(I.e200[n])}</p>
  <p>MACD: ${I.m.line[n]>I.m.sig[n]?"Pozitif":"Negatif"} · Giriş tetikleyicisi: ${I.trigger[n]?"Var":"Yok"}</p>
  <p>Yatay piyasa: ${I.sideways[n]?"Tespit edildi":"Tespit edilmedi"} · Geç giriş riski: ${I.late[n]?"Var":"Yok"}</p>
  <p>Referans stop: ${fmt(k[n].c-1.5*I.a[n])} · Referans hedef: ${fmt(k[n].c+2*I.a[n])}</p>
  <p class="note">Bu stop ve hedef, mevcut kapanış fiyatına göre referanstır; gerçek açık pozisyon takibi değildir.</p>`;
 }catch(e){$("status").innerHTML=`<span class="bad">${e.message}</span>`;}
}
function renderScan(){
 const q=$("search").value.trim().toUpperCase();
 const f=$("filter").value;
 const rows=scanRows.filter(r=>(!q||r.symbol.includes(q))&&(f==="ALL"||r.state===f));
 $("sc").innerHTML="<tr><th>Coin</th><th>Durum</th><th>Fiyat</th><th>24s hacim (USDT)</th><th>Puan</th><th>RSI</th><th>ADX</th></tr>"+
 rows.map(r=>`<tr>
 <td><b>${r.symbol}</b></td>
 <td class="${cls(r.state)}">${r.state}</td>
 <td>${fmt(r.price)}</td><td>${r.vol.toLocaleString("en-US",{maximumFractionDigits:0})}</td>
 <td>${r.score===null?"—":r.score}</td>
 <td>${r.rsi===null?"—":r.rsi.toFixed(1)}</td>
 <td>${r.adx===null?"—":r.adx.toFixed(1)}</td>
 </tr>`).join("");
 if(!rows.length)$("sc").innerHTML+="<tr><td colspan='7'>Bu filtrede sonuç yok.</td></tr>";
}
async function scan(){
 if(busy)return;
 busy=true;
 $("scan").disabled=true;$("analyze").disabled=true;
 scanRows=[];
 $("sc").innerHTML="<tr><td>Binance Spot pariteleri alınıyor...</td></tr>";
 try{
  const [info,tickers]=await Promise.all([
   getJSON("https://api.binance.com/api/v3/exchangeInfo"),
   getJSON("https://api.binance.com/api/v3/ticker/24hr")
  ]);
  const stable=new Set(["USDT","USDC","BUSD","FDUSD","TUSD","DAI","USDP","USDE","PYUSD"]);
  const tickerMap=new Map(tickers.map(x=>[x.symbol,x]));
  const markets=info.symbols.filter(x=>
   x.status==="TRADING"&&x.isSpotTradingAllowed&&x.quoteAsset==="USDT"&&
   !stable.has(x.baseAsset)&&
   !/(UP|DOWN|BULL|BEAR)$/.test(x.baseAsset)&&tickerMap.has(x.symbol)
  ).map(x=>({symbol:x.symbol,base:x.baseAsset,ticker:tickerMap.get(x.symbol)}));
  let done=0;
  const total=markets.length;
  $("status").textContent=`Toplam ${total} uygun Spot USDT paritesi bulundu. 4 saatlik filtre kontrol ediliyor...`;
  $("sc").innerHTML="<tr><th>Coin</th><th>Durum</th><th>Fiyat</th><th>24s hacim (USDT)</th><th>Puan</th><th>RSI</th><th>ADX</th></tr>";
  let next=0;
  async function worker(){
   while(next<markets.length){
    const m=markets[next++];
    try{
     const h4=await load(m.symbol,"4h",210);
     if(h4.length<200)throw Error("Yetersiz 4s mum");
     const H=indicators(h4),hi=h4.length-1;
     const vol=+(m.ticker.quoteVolume||0);
     let row={symbol:m.symbol,price:+m.ticker.lastPrice,vol,
      state:"UYGUN DEĞİL",score:null,rsi:null,adx:null};
     if(h4[hi].c>H.e200[hi]){
      const k=await load(m.symbol,"1h",240);
      if(k.length>=205){
       const I=makeSignals(k,h4),n=k.length-1;
       row={...row,state:I.state[n],score:I.score[n],rsi:I.r[n],adx:I.d.adx[n],price:k[n].c};
      }
     }
     scanRows.push(row);
    }catch(e){
     scanRows.push({symbol:m.symbol,price:+m.ticker.lastPrice,
      vol:+m.ticker.quoteVolume,state:"VERİ HATASI",score:null,rsi:null,adx:null});
    }
    done++;
    if(done%5===0||done===total){
     $("bar").style.width=(done/total*100)+"%";
     $("status").textContent=`Tarama: ${done}/${total} parite`;
     scanRows.sort((a,b)=>{
      const rank={"GÜÇLÜ AL":0,"AL":1,"SAT":2,"GÜÇLÜ SAT":3,"BEKLE":4,"UYGUN DEĞİL":5,"VERİ HATASI":6};
      return (rank[a.state]??9)-(rank[b.state]??9)||b.vol-a.vol;
     });
     renderScan();
    }
   }
  }
  await Promise.all(Array.from({length:4},()=>worker()));
  $("bar").style.width="100%";
  $("status").textContent=`Tarama tamamlandı: ${done} parite kontrol edildi. Sonuçlar yalnızca son kapanmış mumlara dayanır.`;
  renderScan();
 }catch(e){
  $("status").innerHTML=`<span class="bad">Tarama hatası: ${e.message}. Bir süre sonra tekrar deneyebilirsin.</span>`;
 }finally{
  busy=false;$("scan").disabled=false;$("analyze").disabled=false;
 }
}
window.addEventListener("resize",()=>{});
run();
</script>
</body>
</html>
