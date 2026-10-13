<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Binance Spot Tarayıcı</title>
<style>
:root{
  --bg:#0b1018;--panel:#131c29;--panel2:#182435;
  --border:#263448;--text:#e5edf7;--muted:#91a1b6;
  --green:#19c784;--red:#ff5b6e;--blue:#58a6ff;
  --yellow:#f5c451;--radius:13px
}
*{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--text);
  font:14px/1.5 Arial,Helvetica,sans-serif}
button,input,select{font:inherit}
button{cursor:pointer}
.app{max-width:1600px;margin:auto;padding:22px}
header{display:flex;justify-content:space-between;align-items:center;
  gap:15px;flex-wrap:wrap;margin-bottom:20px}
.brand{font-size:22px;font-weight:800;letter-spacing:.2px}
.brand span{color:var(--yellow)}
.sub{color:var(--muted);font-size:12px;margin-top:4px}
.actions{display:flex;gap:8px;align-items:center;flex-wrap:wrap}
.btn{background:var(--panel2);color:var(--text);border:1px solid var(--border);
  padding:9px 13px;border-radius:8px}
.btn.primary{background:#174a3b;border-color:#197755}
.btn:disabled{opacity:.55;cursor:wait}
.cards{display:grid;grid-template-columns:repeat(4,minmax(0,1fr));
  gap:12px;margin-bottom:15px}
.card,.panel{background:var(--panel);border:1px solid var(--border);
  border-radius:var(--radius)}
.card{padding:15px}
.card .label{font-size:12px;color:var(--muted)}
.card .value{font-size:23px;font-weight:800;margin-top:6px}
.card .hint{font-size:11px;color:var(--muted);margin-top:4px}
.layout{display:grid;grid-template-columns:minmax(0,1.35fr) minmax(330px,.9fr);
  gap:14px;align-items:start}
.panel{padding:16px;margin-bottom:14px;min-width:0}
.panel-title{display:flex;justify-content:space-between;gap:10px;
  align-items:center;flex-wrap:wrap;margin-bottom:13px}
.panel-title h2{font-size:15px;margin:0}
.muted{color:var(--muted)}
.small{font-size:12px}
.toolbar{display:flex;gap:8px;flex-wrap:wrap;margin-bottom:12px}
input,select{background:#0e1723;color:var(--text);
  border:1px solid var(--border);border-radius:8px;padding:9px;min-width:0}
input{flex:1;min-width:130px}
select{max-width:100%}
.table-wrap{overflow:auto;max-height:570px;border:1px solid var(--border);
  border-radius:9px}
table{width:100%;border-collapse:collapse;white-space:nowrap;font-size:12px}
th{position:sticky;top:0;background:#182435;color:var(--muted);
  text-align:left;font-weight:600;z-index:1}
th,td{padding:11px 10px;border-bottom:1px solid #223044}
tbody tr{cursor:pointer}
tbody tr:hover,tbody tr.selected{background:#1b2b3e}
.coin{font-weight:800;color:var(--text)}
.coin small{display:block;color:var(--muted);font-weight:400}
.badge{display:inline-block;padding:4px 7px;border-radius:6px;
  font-size:11px;font-weight:700;background:#263448;color:#c7d4e5}
.badge.buy{background:#123e32;color:#54e4ac}
.badge.sell{background:#4b202b;color:#ff8d9a}
.badge.wait{background:#3b3420;color:#f4d278}
.positive{color:var(--green)}.negative{color:var(--red)}
.blue{color:var(--blue)}.yellow{color:var(--yellow)}
.chartbox{position:relative;width:100%;height:330px;background:#0d1520;
  border:1px solid var(--border);border-radius:10px;overflow:hidden}
canvas{display:block;width:100%;height:100%}
.chart-legend{display:flex;gap:13px;flex-wrap:wrap;font-size:11px;
  color:var(--muted);margin-top:9px}
.dot{display:inline-block;width:8px;height:8px;border-radius:50%;margin-right:4px}
.coin-summary{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));
  gap:8px;margin:12px 0}
.metric{padding:10px;background:#0e1723;border:1px solid var(--border);
  border-radius:8px;min-width:0}
.metric .label{font-size:11px;color:var(--muted)}
.metric .value{font-weight:800;margin-top:4px;overflow-wrap:anywhere}
.detail-text{white-space:pre-line;color:#c4d0df;font-size:13px}
.strategy-grid{display:grid;grid-template-columns:1fr;gap:9px}
.strategy{border:1px solid var(--border);background:#0f1824;
  border-radius:10px;padding:12px}
.strategy-head{display:flex;justify-content:space-between;gap:8px;align-items:center}
.strategy h3{font-size:13px;margin:0}
.strategy .stats{display:grid;grid-template-columns:repeat(3,1fr);
  gap:7px;margin-top:10px}
.strategy .stats div{background:#151f2d;padding:7px;border-radius:6px}
.strategy .stats small{display:block;color:var(--muted);font-size:10px}
.strategy .stats b{display:block;margin-top:3px;font-size:12px}
.strategy button{margin-top:9px;width:100%}
.history-table{max-height:270px}
.statusline{color:var(--muted);font-size:12px;margin-top:10px}
.progress{height:4px;background:#253245;border-radius:5px;overflow:hidden;margin-top:8px}
.progress>div{height:100%;width:0;background:var(--blue);transition:width .2s}
.empty{padding:24px;text-align:center;color:var(--muted)}
footer{padding:10px 0 22px;color:var(--muted);font-size:11px}
@media(max-width:1000px){
 .layout{grid-template-columns:1fr}
 .cards{grid-template-columns:repeat(2,minmax(0,1fr))}
}
@media(max-width:560px){
 .app{padding:12px}.brand{font-size:19px}.panel{padding:12px}
 .cards{gap:8px}.card{padding:11px}.card .value{font-size:20px}
 .chartbox{height:270px}.coin-summary{grid-template-columns:repeat(2,minmax(0,1fr))}
}
</style>
</head>
<body>
<div class="app">
<header>
  <div>
    <div class="brand"><span>◈</span> Binance Spot Tarayıcı</div>
    <div class="sub">1 saatlik sinyaller · 4 saatlik EMA200 trend filtresi · Kapanmış mumlar</div>
  </div>
  <div class="actions">
    <span id="connection" class="badge">Bağlantı bekleniyor</span>
    <button class="btn primary" id="refreshBtn" onclick="startScan()">⟳ Taramayı Başlat</button>
  </div>
</header>

<section class="cards">
  <div class="card"><div class="label">Taranan coin</div><div class="value" id="totalCoins">—</div><div class="hint">Binance Spot USDT pariteleri</div></div>
  <div class="card"><div class="label">AL sinyali</div><div class="value positive" id="buyCount">—</div><div class="hint">Seçili tarama sonuçları</div></div>
  <div class="card"><div class="label">GÜÇLÜ AL</div><div class="value yellow" id="strongCount">—</div><div class="hint">Tüm koşulları aynı mumda sağlayanlar</div></div>
  <div class="card"><div class="label">Son güncelleme</div><div class="value" style="font-size:17px" id="updated">—</div><div class="hint" id="scanStatus">Henüz tarama yapılmadı</div></div>
</section>

<div class="panel">
  <div class="panel-title">
    <h2>Coin tarama tablosu</h2>
    <span class="muted small">Coin adına tıklayarak ayrıntılarını aç</span>
  </div>
  <div class="toolbar">
    <input id="search" placeholder="Coin ara: BTC, ETH, SOL..." oninput="renderTable()">
    <select id="signalFilter" onchange="renderTable()">
      <option value="all">Tüm durumlar</option>
      <option value="buy">AL sinyalleri</option>
      <option value="strong">GÜÇLÜ AL</option>
      <option value="sell">SAT sinyalleri</option>
      <option value="wait">Bekle / Uygun değil</option>
    </select>
    <select id="sortBy" onchange="renderTable()">
      <option value="score">Puana göre sırala</option>
      <option value="symbol">Coin adına göre sırala</option>
      <option value="volume">24 saatlik hacme göre sırala</option>
      <option value="change">24 saatlik değişime göre sırala</option>
    </select>
  </div>
  <div class="progress"><div id="progressBar"></div></div>
  <div id="scanProgress" class="statusline">Taramayı başlatmak için düğmeye bas.</div>
  <div class="table-wrap">
    <table>
      <thead><tr>
        <th>Coin</th><th>Durum</th><th>Puan</th><th>Fiyat</th>
        <th>Giriş</th><th>STOP</th><th>HEDEF</th><th>RSI</th>
        <th>24s hacim</th><th>24s %</th>
      </tr></thead>
      <tbody id="coinRows"><tr><td colspan="10" class="empty">Henüz sonuç yok.</td></tr></tbody>
    </table>
  </div>
</div>

<div class="layout">
  <main>
    <div class="panel">
      <div class="panel-title">
        <h2 id="chartTitle">Seçilen coin grafiği</h2>
        <span id="chartInterval" class="badge">1 saat</span>
      </div>
      <div class="chartbox"><canvas id="chart"></canvas></div>
      <div class="chart-legend">
        <span><i class="dot" style="background:#58a6ff"></i>EMA20</span>
        <span><i class="dot" style="background:#f5c451"></i>EMA50</span>
        <span><i class="dot" style="background:#bd8cff"></i>EMA200</span>
        <span><i class="dot" style="background:#19c784"></i>Yükseliş mumu</span>
        <span><i class="dot" style="background:#ff5b6e"></i>Düşüş mumu</span>
      </div>
      <div class="statusline" id="chartInfo">Bir coin seçildiğinde grafiği burada açılır.</div>
    </div>

    <div class="panel">
      <div class="panel-title">
        <h2>Seçilen coin hakkında</h2>
        <span id="selectedBadge" class="badge">Coin seçilmedi</span>
      </div>
      <div id="coinSummary" class="coin-summary">
        <div class="metric"><div class="label">Fiyat</div><div class="value">—</div></div>
        <div class="metric"><div class="label">RSI</div><div class="value">—</div></div>
        <div class="metric"><div class="label">ATR</div><div class="value">—</div></div>
      </div>
      <div id="coinCommentary" class="detail-text">Tarama listesinden bir coin seç. Trend, göstergeler, destek/direnç ve strateji sonuçları burada gösterilecek.</div>
    </div>

    <div class="panel">
      <div class="panel-title">
        <h2>Geçmiş sinyal analizi</h2>
        <span id="historySubtitle" class="muted small">Seçilen coin · 5 strateji</span>
      </div>
      <div class="table-wrap history-table">
        <table>
          <thead><tr><th>Strateji</th><th>İşlem</th><th>Kazanç</th><th>Başarı</th><th>Net %</th></tr></thead>
          <tbody id="historyRows"><tr><td colspan="5" class="empty">Önce coin seç.</td></tr></tbody>
        </table>
      </div>
      <div class="statusline">Test, geçmiş kapanmış mumlarla ve aynı mum içinde stop/hedef görülürse temkinli olarak stop önceliğiyle hesaplanır. Komisyon varsayımı: alış %0,25 + satış %0,25.</div>
    </div>
  </main>

  <aside>
    <div class="panel">
      <div class="panel-title">
        <h2>5 al-sat stratejisi</h2>
        <span class="muted small">Seçili coin</span>
      </div>
      <div id="strategyList" class="strategy-grid">
        <div class="empty">Strateji sonuçları burada gösterilecek.</div>
      </div>
    </div>
    <div class="panel">
      <div class="panel-title"><h2>GÜÇLÜ AL kuralları</h2></div>
      <div class="detail-text">✓ 4 saatlik EMA200 filtresi olumlu
✓ 1 saatlik fiyat EMA200 üzerinde
✓ EMA20 &gt; EMA50 &gt; EMA100
✓ Kapanış, önceki 20 mumun en yüksek seviyesini aşar
✓ Hacim, önceki 20 mum ortalamasının 1,5 katından fazla
✓ RSI 55–80 arasında

STOP: 1,5 × ATR
HEDEF: 2,5 × ATR

GÜÇLÜ SAT: Fiyat EMA50 altındayken EMA20, EMA50'nin altına keser.</div>
    </div>
    <div class="panel">
      <div class="panel-title"><h2>Tarama bilgisi</h2></div>
      <div id="scanNotes" class="detail-text">Sistem, Binance Spot USDT paritelerinin kapanmış 1 saatlik mumlarını değerlendirir. 24 saatlik hacim tabloda bilgi amaçlıdır; sinyal koşulu değildir.</div>
    </div>
  </aside>
</div>
<footer>
Bu araç teknik analiz ve eğitim amaçlıdır; finansal tavsiye değildir. Sinyaller kâr garantisi vermez. API bağlantısı, gecikme, spread ve gerçek emir gerçekleşmeleri sonuçları etkileyebilir.
</footer>
</div>

<script>
"use strict";

const API="https://api.binance.com";
const FEE=0.0025;
const LIMIT=300;
const STRATEGIES=[
 {id:"sr",name:"1. Destek–Direnç + Trend"},
 {id:"pull",name:"2. EMA20–50 Geri Çekilme"},
 {id:"ta",name:"3. Teknik Analiz Sonrası Al-Sat"},
 {id:"cross",name:"4. EMA20–50 Kesişimi"},
 {id:"strong",name:"5. GÜÇLÜ AL / GÜÇLÜ SAT"}
];

let allRows=[],selectedSymbol=null,selectedData=null,busy=false;
let scanCache=new Map();

const $=id=>document.getElementById(id);
const fmt=(n,d=4)=>{
 if(!Number.isFinite(n))return "—";
 if(Math.abs(n)>=1000)return n.toLocaleString("en-US",{maximumFractionDigits:2});
 if(Math.abs(n)>=1)return n.toLocaleString("en-US",{maximumFractionDigits:d});
 return n.toLocaleString("en-US",{maximumFractionDigits:8});
};
const pct=(n)=>Number.isFinite(n)?(n>0?"+":"")+n.toFixed(2)+"%":"—";
const volfmt=n=>{
 if(!Number.isFinite(n))return "—";
 if(n>=1e9)return (n/1e9).toFixed(2)+"B";
 if(n>=1e6)return (n/1e6).toFixed(2)+"M";
 if(n>=1e3)return (n/1e3).toFixed(1)+"K";
 return n.toFixed(0);
};
const wait=ms=>new Promise(r=>setTimeout(r,ms));

async function getJSON(url){
 let last;
 for(let attempt=0;attempt<3;attempt++){
  try{
   const r=await fetch(url);
   if(!r.ok)throw new Error("HTTP "+r.status);
   return await r.json();
  }catch(e){last=e;await wait(350*(attempt+1));}
 }
 throw last||new Error("API bağlantı hatası");
}
function closedKlines(rows){
 const now=Date.now();
 return rows.filter(x=>Number(x[6])<now).map(x=>({
  t:+x[0],o:+x[1],h:+x[2],l:+x[3],c:+x[4],v:+x[5],closeTime:+x[6]
 }));
}
async function loadCandles(symbol,interval,limit=LIMIT){
 const rows=await getJSON(`${API}/api/v3/klines?symbol=${symbol}&interval=${interval}&limit=${limit}`);
 return closedKlines(rows);
}
function ema(values,period){
 const out=Array(values.length).fill(NaN);
 if(values.length<period)return out;
 const k=2/(period+1);
 let seed=0;
 for(let i=0;i<period;i++)seed+=values[i];
 let prev=seed/period;
 out[period-1]=prev;
 for(let i=period;i<values.length;i++){
  prev=values[i]*k+prev*(1-k);out[i]=prev;
 }
 return out;
}
function sma(values,period){
 const out=Array(values.length).fill(NaN);let sum=0;
 for(let i=0;i<values.length;i++){
  sum+=values[i];
  if(i>=period)sum-=values[i-period];
  if(i>=period-1)out[i]=sum/period;
 }
 return out;
}
function rsi(values,period=14){
 const out=Array(values.length).fill(NaN);
 if(values.length<=period)return out;
 let gain=0,loss=0;
 for(let i=1;i<=period;i++){
  const d=values[i]-values[i-1];gain+=Math.max(d,0);loss+=Math.max(-d,0);
 }
 let ag=gain/period,al=loss/period;
 out[period]=al===0?100:100-100/(1+ag/al);
 for(let i=period+1;i<values.length;i++){
  const d=values[i]-values[i-1];
  ag=(ag*(period-1)+Math.max(d,0))/period;
  al=(al*(period-1)+Math.max(-d,0))/period;
  out[i]=al===0?100:100-100/(1+ag/al);
 }
 return out;
}
function atr(candles,period=14){
 const tr=candles.map((x,i)=>i===0?x.h-x.l:
  Math.max(x.h-x.l,Math.abs(x.h-candles[i-1].c),Math.abs(x.l-candles[i-1].c)));
 return sma(tr,period);
}
function macd(values){
 const a=ema(values,12),b=ema(values,26);
 const line=values.map((_,i)=>a[i]-b[i]);
 const signal=ema(line.map(v=>Number.isFinite(v)?v:0),9);
 return {line,signal,hist:line.map((v,i)=>v-signal[i])};
}
function adx(candles,period=14){
 const n=candles.length,plus=Array(n).fill(NaN),minus=Array(n).fill(NaN);
 const tr=Array(n).fill(0);
 for(let i=1;i<n;i++){
  const up=candles[i].h-candles[i-1].h;
  const dn=candles[i-1].l-candles[i].l;
  plus[i]=up>dn&&up>0?up:0;
  minus[i]=dn>up&&dn>0?dn:0;
  tr[i]=Math.max(candles[i].h-candles[i].l,
   Math.abs(candles[i].h-candles[i-1].c),
   Math.abs(candles[i].l-candles[i-1].c));
 }
 const smooth=a=>{
  let out=Array(n).fill(NaN);
  if(n<=period)return out;
  let s=0;for(let i=1;i<=period;i++)s+=a[i]||0;
  let p=s;
  out[period]=p;
  for(let i=period+1;i<n;i++){p=p-p/period+(a[i]||0);out[i]=p;}
  return out;
 };
 const st=smooth(tr),sp=smooth(plus),sm=smooth(minus);
 const dx=Array(n).fill(NaN);
 for(let i=period;i<n;i++){
  if(st[i]>0){
   const p=100*sp[i]/st[i],m=100*sm[i]/st[i];
   dx[i]=p+m===0?0:100*Math.abs(p-m)/(p+m);
  }
 }
 const out=Array(n).fill(NaN);
 for(let i=period*2;i<n;i++){
  const slice=dx.slice(i-period+1,i+1).filter(Number.isFinite);
  if(slice.length===period)out[i]=slice.reduce((a,b)=>a+b,0)/period;
 }
 return out;
}
function bollinger(values,period=20,mult=2){
 const mid=sma(values,period),up=[],low=[],width=[];
 for(let i=0;i<values.length;i++){
  if(i<period-1){up.push(NaN);low.push(NaN);width.push(NaN);continue;}
  const sl=values.slice(i-period+1,i+1),mean=mid[i];
  const sd=Math.sqrt(sl.reduce((s,v)=>s+(v-mean)**2,0)/period);
  up.push(mean+mult*sd);low.push(mean-mult*sd);
  width.push(mean?((up[i]-low[i])/mean)*100:NaN);
 }
 return {mid,up,low,width};
}
function maxPrev(candles,i,n,field="h"){
 let m=-Infinity;
 for(let j=i-n;j<i;j++)if(j>=0)m=Math.max(m,candles[j][field]);
 return m;
}
function minPrev(candles,i,n,field="l"){
 let m=Infinity;
 for(let j=i-n;j<i;j++)if(j>=0)m=Math.min(m,candles[j][field]);
 return m;
}
function avgPrev(values,i,n){
 if(i<n)return NaN;
 let s=0;for(let j=i-n;j<i;j++)s+=values[j];
 return s/n;
}
function crossUp(a,b,i){return a[i]>b[i]&&a[i-1]<=b[i-1];}
function crossDown(a,b,i){return a[i]<b[i]&&a[i-1]>=b[i-1];}

function prepare(candles,h4){
 const c=candles.map(x=>x.c),v=candles.map(x=>x.v);
 const E={e20:ema(c,20),e50:ema(c,50),e100:ema(c,100),e200:ema(c,200),
  r:rsi(c),a:atr(c),m:macd(c),adx:adx(c),bb:bollinger(c),vol:sma(v,20)};
 const hclose=h4.map(x=>x.c),hE=ema(hclose,200);
 // Her 1 saatlik muma, o saatte kapanmış en son 4 saatlik mumun EMA200 değerini bağla.
 const gate=Array(candles.length).fill(false),gateValue=Array(candles.length).fill(NaN);
 let hi=0;
 for(let i=0;i<candles.length;i++){
  while(hi+1<h4.length&&h4[hi+1].closeTime<=candles[i].closeTime)hi++;
  if(hi>=199&&Number.isFinite(hE[hi])){
   gate[i]=h4[hi].c>hE[hi];gateValue[i]=hE[hi];
  }
 }
 return {candles,h4,E,gate,gateValue};
}

function supportResistance(candles,i){
 const support=minPrev(candles,i,20);
 const resistance=maxPrev(candles,i,20);
 return {support,resistance};
}
function strongBuy(d,i){
 const {candles,E,gate}=d,c=candles[i],e=E;
 if(i<201||!gate[i])return false;
 const high=maxPrev(candles,i,20,"h");
 const av=avgPrev(candles.map(x=>x.v),i,20);
 return c.c>e.e200[i]&&e.e20[i]>e.e50[i]&&e.e50[i]>e.e100[i]&&
  c.c>high&&c.v>av*1.5&&e.r[i]>=55&&e.r[i]<=80;
}
function strongSell(d,i){
 const {candles,E}=d;
 return i>1&&candles[i].c<E.e50[i]&&crossDown(E.e20,E.e50,i);
}
function strategySignal(d,id,i){
 const {candles,E,gate}=d,c=candles[i],e=E;
 if(i<202||!Number.isFinite(e.a[i])||!Number.isFinite(e.e200[i]))return "";
 const trend=gate[i]&&c.c>e.e200[i];
 if(id==="strong"){
  if(strongBuy(d,i))return "BUY";
  if(strongSell(d,i))return "SELL";
  return "";
 }
 if(id==="sr"){
  const resistance=maxPrev(candles,i,20,"h");
  const support=minPrev(candles,i,20,"l");
  const breakout=c.c>resistance&&c.c>c.o&&e.e20[i]>e.e50[i];
  const supportBounce=c.l<=support*1.01&&c.c>c.o&&c.c>e.e20[i]&&e.e20[i]>e.e50[i];
  if(trend&&(breakout||supportBounce))return "BUY";
  if(c.c<e.e50[i]&&c.c<support)return "SELL";
 }
 if(id==="pull"){
  const pullback=(c.l<=e.e20[i]||c.l<=e.e50[i])&&c.c>e.e20[i]&&c.c>c.o;
  if(trend&&e.e20[i]>e.e50[i]&&e.e50[i]>e.e100[i]&&pullback)return "BUY";
  if(c.c<e.e50[i]&&e.e20[i]<e.e50[i])return "SELL";
 }
 if(id==="ta"){
  const score=technicalScore(d,i);
  if(trend&&score>=70)return "BUY";
  if(c.c<e.e200[i]&&e.r[i]<45&&e.m.hist[i]<0&&e.adx[i]>18)return "SELL";
 }
 if(id==="cross"){
  if(trend&&crossUp(e.e20,e.e50,i))return "BUY";
  if(c.c<e.e50[i]&&crossDown(e.e20,e.e50,i))return "SELL";
 }
 return "";
}
function technicalScore(d,i){
 const {candles,E,gate}=d,c=candles[i],e=E;
 let score=0;
 if(gate[i]&&c.c>e.e200[i])score+=20;
 if(e.e20[i]>e.e50[i])score+=12;
 if(e.e50[i]>e.e100[i])score+=8;
 if(e.m.hist[i]>0)score+=14;
 if(e.r[i]>=50&&e.r[i]<=70)score+=14;
 else if(e.r[i]>70&&e.r[i]<80)score+=7;
 if(e.adx[i]>20)score+=12;
 if(c.v>avgPrev(candles.map(x=>x.v),i,20))score+=10;
 if(c.c>e.bb.mid[i])score+=10;
 return score;
}
function signalScore(d,id,i){
 const s=strategySignal(d,id,i);
 if(!s)return technicalScore(d,i);
 if(id==="strong")return s==="BUY"?100:90;
 return Math.min(98,Math.max(60,technicalScore(d,i)+(s==="BUY"?12:0)));
}
function currentSignal(d){
 const i=d.candles.length-1;
 const signals=STRATEGIES.map(s=>({id:s.id,name:s.name,signal:strategySignal(d,s.id,i),
  score:signalScore(d,s.id,i)}));
 const strong=signals.find(x=>x.id==="strong");
 const buys=signals.filter(x=>x.signal==="BUY");
 const sells=signals.filter(x=>x.signal==="SELL");
 let main;
 if(strong.signal==="BUY")main={signal:"GÜÇLÜ AL",score:100};
 else if(strong.signal==="SELL")main={signal:"GÜÇLÜ SAT",score:90};
 else if(buys.length)main={signal:"AL",score:Math.max(...buys.map(x=>x.score))};
 else if(sells.length)main={signal:"SAT",score:Math.max(...sells.map(x=>x.score))};
 else main={signal:d.gate[i]?"BEKLE":"UYGUN DEĞİL",score:technicalScore(d,i)};
 return {signals,main};
}

// Basitleştirilmiş mum bazlı geçmiş test.
// Sinyal mumunun kapanışından giriş varsayılır; sonraki mumlarda stop/hedef aranır.
function backtest(d,id){
 const c=d.candles,e=d.E;let trades=[],inTrade=null;
 for(let i=202;i<c.length-1;i++){
  if(inTrade){
   const bar=c[i];let exit=null,price=null;
   if(bar.l<=inTrade.stop){exit="STOP";price=inTrade.stop;}
   else if(bar.h>=inTrade.target){exit="HEDEF";price=inTrade.target;}
   else if(id==="strong"&&strongSell(d,i)){exit="GÜÇLÜ SAT";price=bar.c;}
   else if(strategySignal(d,id,i)==="SELL"){exit="SAT";price=bar.c;}
   if(exit){
    const ret=(price-inTrade.entry)/inTrade.entry*100-2*FEE*100;
    trades.push({entryTime:inTrade.time,exitTime:bar.t,entry:inTrade.entry,exit:price,
      result:ret,reason:exit});
    inTrade=null;
   }
   continue;
  }
  if(strategySignal(d,id,i)==="BUY"&&Number.isFinite(e.a[i])&&e.a[i]>0){
   inTrade={entry:c[i].c,time:c[i].t,stop:c[i].c-1.5*e.a[i],
    target:c[i].c+(id==="strong"?2.5:2)*e.a[i]};
  }
 }
 if(inTrade){
  const last=c[c.length-1],ret=(last.c-inTrade.entry)/inTrade.entry*100-2*FEE*100;
  trades.push({entryTime:inTrade.time,exitTime:last.t,entry:inTrade.entry,
    exit:last.c,result:ret,reason:"Son kapanış"});
 }
 const wins=trades.filter(t=>t.result>0).length;
 return {trades,wins,losses:trades.length-wins,
  winRate:trades.length?wins/trades.length*100:0,
  net:trades.reduce((s,t)=>s+t.result,0)};
}
function latestLevels(d){
 const i=d.candles.length-1,c=d.candles[i],e=d.E;
 return {entry:c.c,stop:c.c-1.5*e.a[i],target:c.c+2.5*e.a[i]};
}
function mainRow(d,info){
 const sig=currentSignal(d),lev=latestLevels(d),i=d.candles.length-1;
 return {symbol:info.symbol,base:info.base,price:d.candles[i].c,
  volume:+info.quoteVolume||0,change:+info.priceChangePercent||0,
  rsi:d.E.r[i],score:sig.main.score,signal:sig.main.signal,
  entry:lev.entry,stop:lev.stop,target:lev.target,data:d,signals:sig.signals};
}

async function startScan(){
 if(busy)return;
 busy=true;
 $("refreshBtn").disabled=true;
 $("connection").textContent="Tarama sürüyor";
 $("connection").className="badge wait";
 $("scanStatus").textContent="Piyasa verileri alınıyor...";
 $("coinRows").innerHTML='<tr><td colspan="10" class="empty">Veriler alınıyor…</td></tr>';
 $("progressBar").style.width="0%";
 try{
  const [exchange,tickers]=await Promise.all([
   getJSON(API+"/api/v3/exchangeInfo"),
   getJSON(API+"/api/v3/ticker/24hr")
  ]);
  const tickerMap=new Map(tickers.map(x=>[x.symbol,x]));
  const symbols=exchange.symbols.filter(s=>s.status==="TRADING"&&s.quoteAsset==="USDT"&&
    s.isSpotTradingAllowed!==false&&!["USDCUSDT","FDUSDUSDT","TUSDUSDT","USDPUSDT","BUSDUSDT","EURUSDT"].includes(s.symbol))
    .map(s=>({symbol:s.symbol,base:s.baseAsset,...(tickerMap.get(s.symbol)||{})}));
  $("totalCoins").textContent=symbols.length;
  let done=0,errors=0;
  const results=[];
  // İstekleri kontrollü paralel yürüt; 24 saatlik hacim sinyal filtresi olarak kullanılmaz.
  const workers=5;
  let cursor=0;
  async function worker(){
   while(true){
    const index=cursor++;
    if(index>=symbols.length)return;
    const info=symbols[index];
    try{
     const [h1,h4]=await Promise.all([
      loadCandles(info.symbol,"1h",LIMIT),
      loadCandles(info.symbol,"4h",260)
     ]);
     if(h1.length<220||h4.length<205)throw new Error("Yetersiz mum verisi");
     const data=prepare(h1,h4);
     const row=mainRow(data,info);
     results.push(row);scanCache.set(info.symbol,row);
    }catch(err){errors++;}
    done++;
    const percent=Math.round(done/symbols.length*100);
    $("progressBar").style.width=percent+"%";
    $("scanProgress").textContent=`Tarama: ${done}/${symbols.length} · Hatalı/eksik: ${errors}`;
    if(done%10===0)renderTable();
   }
  }
  await Promise.all(Array.from({length:workers},worker));
  allRows=results;
  renderTable();
  $("updated").textContent=new Date().toLocaleTimeString("tr-TR",{hour:"2-digit",minute:"2-digit"});
  $("scanStatus").textContent=`Tamamlandı · ${errors} coin okunamadı`;
  $("connection").textContent="Bağlandı";
  $("connection").className="badge buy";
  $("buyCount").textContent=allRows.filter(r=>r.signal==="AL"||r.signal==="GÜÇLÜ AL").length;
  $("strongCount").textContent=allRows.filter(r=>r.signal==="GÜÇLÜ AL").length;
  if(!selectedSymbol&&allRows.length)selectCoin(allRows[0].symbol);
  else if(selectedSymbol&&scanCache.has(selectedSymbol))selectCoin(selectedSymbol);
 }catch(e){
  $("connection").textContent="Bağlantı hatası";
  $("connection").className="badge sell";
  $("scanProgress").textContent="Binance API verisi alınamadı: "+e.message+
   ". Biraz sonra tekrar dene.";
  $("scanNotes").textContent="API bağlantısı, bölgesel erişim veya istek sınırı sorun yaratmış olabilir. Tarayıcı konsolunda hata ayrıntıları görülebilir.";
 }finally{
  busy=false;$("refreshBtn").disabled=false;
 }
}
function renderTable(){
 const q=$("search").value.trim().toUpperCase(),filter=$("signalFilter").value;
 const sort=$("sortBy").value;
 let rows=allRows.filter(r=>r.symbol.includes(q));
 rows=rows.filter(r=>{
  if(filter==="buy")return r.signal==="AL"||r.signal==="GÜÇLÜ AL";
  if(filter==="strong")return r.signal==="GÜÇLÜ AL";
  if(filter==="sell")return r.signal==="SAT"||r.signal==="GÜÇLÜ SAT";
  if(filter==="wait")return r.signal==="BEKLE"||r.signal==="UYGUN DEĞİL";
  return true;
 });
 rows.sort((a,b)=>sort==="symbol"?a.symbol.localeCompare(b.symbol):
  sort==="volume"?b.volume-a.volume:sort==="change"?b.change-a.change:b.score-a.score);
 if(!rows.length){
  $("coinRows").innerHTML='<tr><td colspan="10" class="empty">Bu filtrede sonuç yok.</td></tr>';return;
 }
 $("coinRows").innerHTML=rows.map(r=>{
  const isBuy=r.signal==="AL"||r.signal==="GÜÇLÜ AL";
  const isSell=r.signal==="SAT"||r.signal==="GÜÇLÜ SAT";
  const cls=isBuy?"buy":isSell?"sell":"wait";
  const selected=r.symbol===selectedSymbol?"selected":"";
  return `<tr class="${selected}" data-symbol="${r.symbol}">
   <td><span class="coin">${r.base}</span><small>${r.symbol}</small></td>
   <td><span class="badge ${cls}">${r.signal}</span></td>
   <td>${Math.round(r.score)}/100</td><td>${fmt(r.price)}</td>
   <td>${fmt(r.entry)}</td><td class="negative">${fmt(r.stop)}</td>
   <td class="positive">${fmt(r.target)}</td><td>${fmt(r.rsi,1)}</td>
   <td>${volfmt(r.volume)}</td><td class="${r.change>=0?"positive":"negative"}">${pct(r.change)}</td>
  </tr>`;
 }).join("");
 $("coinRows").querySelectorAll("tr[data-symbol]").forEach(tr=>{
  tr.addEventListener("click",()=>selectCoin(tr.dataset.symbol));
 });
}
async function selectCoin(symbol){
 selectedSymbol=symbol;
 let row=scanCache.get(symbol);
 if(!row){
  renderTable();return;
 }
 selectedData=row.data;
 renderTable();
 $("chartTitle").textContent=symbol+" · 1 saatlik grafik";
 $("selectedBadge").textContent=symbol;
 $("historySubtitle").textContent=symbol+" · 5 strateji";
 renderChart(selectedData);
 renderCoinDetail(row);
 renderStrategies(row);
 renderHistory(row);
}
function renderCoinDetail(row){
 const d=row.data,i=d.candles.length-1,c=d.candles[i],e=d.E;
 const lev=latestLevels(d);
 const trend4=d.gate[i]?"Olumlu":"Olumsuz / veri yetersiz";
 const trend1=c.c>e.e200[i]?"EMA200 üzerinde":"EMA200 altında";
 const emaOrder=e.e20[i]>e.e50[i]&&e.e50[i]>e.e100[i]?"Pozitif sıralama":
  e.e20[i]<e.e50[i]&&e.e50[i]<e.e100[i]?"Negatif sıralama":"Karışık sıralama";
 const sr=supportResistance(d.candles,i);
 const macdState=e.m.hist[i]>0?"Pozitif":"Negatif";
 const adxState=e.adx[i]>=25?"Belirgin trend":e.adx[i]>=18?"Orta/zayıf trend":"Trend gücü zayıf";
 $("coinSummary").innerHTML=`
 <div class="metric"><div class="label">Son fiyat</div><div class="value">${fmt(c.c)}</div></div>
 <div class="metric"><div class="label">RSI (14)</div><div class="value">${fmt(e.r[i],1)}</div></div>
 <div class="metric"><div class="label">ATR (14)</div><div class="value">${fmt(e.a[i])}</div></div>
 <div class="metric"><div class="label">Stop (1,5 ATR)</div><div class="value negative">${fmt(lev.stop)}</div></div>
 <div class="metric"><div class="label">Hedef (2,5 ATR)</div><div class="value positive">${fmt(lev.target)}</div></div>
 <div class="metric"><div class="label">24s hacim</div><div class="value">${volfmt(row.volume)} USDT</div></div>`;
 const signals=row.signals.map(s=>`${s.name}: ${s.signal||"Sinyal yok"} (puan ${Math.round(s.score)})`).join("\n");
 $("coinCommentary").textContent=
 `GENEL DURUM: ${row.signal} · Puan ${Math.round(row.score)}/100\n\n`+
 `4 saatlik EMA200 filtresi: ${trend4}\n1 saatlik fiyat / EMA200: ${trend1}\nEMA sıralaması: ${emaOrder}\n`+
 `MACD: ${macdState} · ADX: ${fmt(e.adx[i],1)} (${adxState})\n`+
 `Bollinger genişliği: ${fmt(e.bb.width[i],2)}%\n`+
 `20 mumluk destek: ${fmt(sr.support)}\n20 mumluk direnç: ${fmt(sr.resistance)}\n`+
 `24 saatlik fiyat değişimi: ${pct(row.change)}\n\n`+
 `STRATEJİ YORUMLARI\n${signals}\n\n`+
 `RİSK PLANI\nMevcut fiyat referans alınarak 1,5 ATR stop ve 2,5 ATR hedef gösteriliyor. Bunlar otomatik emir değildir. Güçlü AL için hedef 2,5 ATR; diğer stratejilerde test varsayımı 2 ATR'dir.`;
}
function renderStrategies(row){
 const d=row.data,i=d.candles.length-1;
 $("strategyList").innerHTML=STRATEGIES.map(s=>{
  const sig=row.signals.find(x=>x.id===s.id),bt=backtest(d,s.id);
  const cls=sig.signal==="BUY"?"buy":sig.signal==="SELL"?"sell":"wait";
  const label=sig.signal==="BUY"?(s.id==="strong"?"GÜÇLÜ AL":"AL"):
   sig.signal==="SELL"?(s.id==="strong"?"GÜÇLÜ SAT":"SAT"):"SİNYAL YOK";
  return `<div class="strategy">
   <div class="strategy-head"><h3>${s.name}</h3><span class="badge ${cls}">${label}</span></div>
   <div class="muted small" style="margin-top:5px">Anlık puan: ${Math.round(sig.score)}/100</div>
   <div class="stats">
    <div><small>Test işlemi</small><b>${bt.trades.length}</b></div>
    <div><small>Başarı oranı</small><b>${bt.trades.length?bt.winRate.toFixed(1)+"%":"—"}</b></div>
    <div><small>Net test sonucu</small><b class="${bt.net>=0?"positive":"negative"}">${bt.trades.length?pct(bt.net):"—"}</b></div>
   </div>
   <button class="btn" onclick="showStrategyHistory('${s.id}')">Geçmiş sinyalleri göster</button>
  </div>`;
 }).join("");
}
function renderHistory(row){
 const summaries=STRATEGIES.map(s=>{
  const bt=backtest(row.data,s.id);
  return `<tr><td>${s.name}</td><td>${bt.trades.length}</td>
  <td>${bt.wins} / ${bt.losses}</td><td>${bt.trades.length?bt.winRate.toFixed(1)+"%":"—"}</td>
  <td class="${bt.net>=0?"positive":"negative"}">${bt.trades.length?pct(bt.net):"—"}</td></tr>`;
 }).join("");
 $("historyRows").innerHTML=summaries;
}
function showStrategyHistory(id){
 if(!selectedData)return;
 const s=STRATEGIES.find(x=>x.id===id),bt=backtest(selectedData,id);
 $("historySubtitle").textContent=selectedSymbol+" · "+s.name;
 if(!bt.trades.length){
  $("historyRows").innerHTML='<tr><td colspan="5" class="empty">Test edilen mum aralığında bu stratejide kapanan işlem yok.</td></tr>';
  return;
 }
 $("historyRows").innerHTML=bt.trades.slice().reverse().slice(0,100).map(t=>
  `<tr><td>${new Date(t.entryTime).toLocaleDateString("tr-TR")} → ${new Date(t.exitTime).toLocaleDateString("tr-TR")}<small style="display:block;color:var(--muted)">${t.reason}</small></td>
  <td>${fmt(t.entry)}</td><td>${fmt(t.exit)}</td>
  <td class="${t.result>=0?"positive":"negative"}">${pct(t.result)}</td><td>Komisyon dahil</td></tr>`
 ).join("");
 // Tarih detay görünümünde sütunları açıklayıcı başlıklarla değiştir.
 $("historyRows").previousElementSibling?.querySelector("thead")?.replaceChildren();
 const head=document.querySelector(".history-table thead tr");
 if(head)head.innerHTML="<th>Giriş → Çıkış / Tarih</th><th>Giriş fiyatı</th><th>Çıkış fiyatı</th><th>Net sonuç</th><th>Hesaplama</th>";
}
function renderChart(d){
 const canvas=$("chart"),ctx=canvas.getContext("2d");
 const rect=canvas.getBoundingClientRect();
 const W=Math.max(300,Math.floor(rect.width)),H=Math.max(200,Math.floor(rect.height));
 const ratio=window.devicePixelRatio||1;
 canvas.width=W*ratio;canvas.height=H*ratio;ctx.setTransform(ratio,0,0,ratio,0,0);
 ctx.clearRect(0,0,W,H);
 const c=d.candles.slice(-100),offset=d.candles.length-c.length;
 if(!c.length)return;
 const series=[
  {a:d.E.e20.slice(offset),color:"#58a6ff"},
  {a:d.E.e50.slice(offset),color:"#f5c451"},
  {a:d.E.e200.slice(offset),color:"#bd8cff"}
 ];
 const lows=c.map(x=>x.l),highs=c.map(x=>x.h);
 series.forEach(s=>s.a.forEach(v=>{if(Number.isFinite(v)){lows.push(v);highs.push(v);}}));
 let min=Math.min(...lows),max=Math.max(...highs);
 const pad=(max-min)*.08||1;min-=pad;max+=pad;
 const left=8,right=68,top=13,bottom=24;
 const cw=W-left-right,ch=H-top-bottom;
 const y=v=>top+(max-v)/(max-min)*ch;
 const x=i=>left+(i+.5)*cw/c.length;
 ctx.font="10px Arial";ctx.textAlign="left";
 for(let j=0;j<=4;j++){
  const value=max-(max-min)*j/4,yy=top+ch*j/4;
  ctx.strokeStyle="#263448";ctx.beginPath();ctx.moveTo(left,yy);ctx.lineTo(W-right,yy);ctx.stroke();
  ctx.fillStyle="#91a1b6";ctx.fillText(fmt(value),W-right+6,yy+3);
 }
 const step=cw/c.length,body=Math.max(2,step*.56);
 c.forEach((b,i)=>{
  const xx=x(i),up=b.c>=b.o;
  ctx.strokeStyle=up?"#19c784":"#ff5b6e";
  ctx.fillStyle=ctx.strokeStyle;ctx.lineWidth=1;
  ctx.beginPath();ctx.moveTo(xx,y(b.h));ctx.lineTo(xx,y(b.l));ctx.stroke();
  const yy=Math.min(y(b.o),y(b.c)),hh=Math.max(1,Math.abs(y(b.o)-y(b.c)));
  ctx.fillRect(xx-body/2,yy,body,hh);
 });
 series.forEach(s=>{
  ctx.beginPath();ctx.strokeStyle=s.color;ctx.lineWidth=1.2;let started=false;
  s.a.forEach((v,i)=>{
   if(!Number.isFinite(v)){started=false;return;}
   if(!started){ctx.moveTo(x(i),y(v));started=true;}else ctx.lineTo(x(i),y(v));
  });ctx.stroke();
 });
 ctx.fillStyle="#91a1b6";ctx.textAlign="center";
 [0,24,49,74,99].forEach(i=>{
  if(c[i])ctx.fillText(new Date(c[i].t).toLocaleDateString("tr-TR",{day:"2-digit",month:"2-digit"}),x(i),H-7);
 });
 const last=c[c.length-1];
 $("chartInfo").textContent=`Son kapanmış mum: ${new Date(last.closeTime).toLocaleString("tr-TR")} · Kapanış: ${fmt(last.c)} · Grafik son 100 kapanmış mumu gösterir.`;
}
window.addEventListener("resize",()=>{if(selectedData)renderChart(selectedData);});
startScan();
</script>
</body>
</html>
