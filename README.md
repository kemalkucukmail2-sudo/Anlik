<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Binance Spot Tarayıcı Pro</title>
<style>
:root{
  --bg:#0b1018;--panel:#121a26;--panel2:#172232;--line:#29374a;
  --text:#b8c3d1;--muted:#78879a;--green:#28d69b;--red:#ff6878;
  --yellow:#f4c76b;--blue:#76aaff;
}
*{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--text);font:14px/1.5 Arial,sans-serif}
header{padding:18px 20px;border-bottom:1px solid var(--line);background:#0e1520}
h1{font-size:22px;color:#e5edf7;margin:0 0 5px}
h2{font-size:16px;color:#d5dfeb;margin:0 0 13px}
h3{font-size:14px;color:#d5dfeb;margin:0 0 8px}
p{margin:5px 0;color:var(--muted)}
.container{max-width:1600px;margin:auto;padding:16px}
.toolbar{display:flex;gap:8px;flex-wrap:wrap;align-items:center;margin-bottom:14px}
button,select,input{background:var(--panel2);color:var(--text);border:1px solid var(--line);border-radius:8px;padding:10px 12px;font-size:13px}
button{cursor:pointer;font-weight:700}
button.primary{background:#164f43;border-color:#237b64;color:#a5ffdc}
button:hover{filter:brightness(1.15)}
button:disabled{opacity:.5;cursor:wait}
input{min-width:170px}
.status{font-size:12px;color:var(--muted);margin-left:auto}
.cards{display:grid;grid-template-columns:repeat(4,minmax(0,1fr));gap:10px;margin-bottom:14px}
.card,.panel{background:var(--panel);border:1px solid var(--line);border-radius:12px;padding:14px;min-width:0}
.card .label{font-size:12px;color:var(--muted)}
.card .value{font-size:21px;font-weight:700;color:#e5edf7;margin-top:5px}
.layout{display:grid;grid-template-columns:minmax(0,1.5fr) minmax(300px,1fr);gap:14px}
.panel{margin-bottom:14px}
.table-wrap{overflow:auto;max-height:600px}
table{width:100%;border-collapse:collapse;white-space:nowrap;font-size:12px}
th,td{padding:10px 9px;border-bottom:1px solid #243143;text-align:left}
th{color:#8e9eb2;font-weight:700;background:#111925;position:sticky;top:0;z-index:1}
tbody tr{cursor:pointer}
tbody tr:hover{background:#1a293a}
tbody tr.selected{background:#203449}
.coin{font-weight:700;color:#dce6f2}
.muted{color:var(--muted)}
.buy{color:var(--green);font-weight:700}
.sell{color:var(--red);font-weight:700}
.wait{color:var(--yellow);font-weight:700}
.neutral{color:var(--muted)}
.pill{display:inline-block;border:1px solid var(--line);border-radius:6px;padding:3px 6px;font-size:11px}
.chartbox{height:300px;position:relative}
canvas{width:100%;height:100%;display:block}
.legend{display:flex;gap:12px;flex-wrap:wrap;font-size:11px;color:var(--muted);margin-top:8px}
.dot{display:inline-block;width:8px;height:8px;border-radius:50%;margin-right:4px}
.strategy-list{display:grid;gap:8px}
.strategy{border:1px solid var(--line);border-radius:9px;padding:10px;background:#101824}
.strategy-head{display:flex;justify-content:space-between;gap:8px;align-items:center}
.strategy-name{font-weight:700;color:#dbe5f1}
.strategy-detail{font-size:12px;color:var(--muted);margin-top:5px}
.stats{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:8px;margin:10px 0}
.stat{background:#0d141e;border-radius:8px;padding:9px}
.stat small{display:block;color:var(--muted);font-size:11px}
.stat b{display:block;color:#dce6f2;margin-top:3px}
.tabs{display:flex;gap:6px;flex-wrap:wrap;margin-bottom:10px}
.tabs button{padding:8px 10px;font-size:12px}
.tabs button.active{background:#213b54;border-color:#41698e;color:#e0efff}
.notice{border-left:3px solid var(--blue);padding:9px 11px;background:#111d2b;color:#9fb3ca;font-size:12px;border-radius:4px;margin:10px 0}
.error{color:var(--red)}
.good{color:var(--green)}
.small{font-size:11px;color:var(--muted)}
#historyTable{max-height:300px}
@media(max-width:900px){
 .layout{grid-template-columns:1fr}
 .cards{grid-template-columns:repeat(2,minmax(0,1fr))}
 .status{width:100%;margin-left:0}
 header{padding:15px}
 .container{padding:10px}
}
</style>
</head>
<body>
<header>
  <h1>Binance Spot Tarayıcı Pro</h1>
  <p>1 saatlik analiz · 4 saatlik EMA200 filtresi · 5 bağımsız strateji</p>
</header>

<div class="container">
  <div class="toolbar">
    <button id="scanBtn" class="primary">Taramayı Başlat</button>
    <button id="refreshBtn">Seçili Coini Yenile</button>
    <input id="search" placeholder="Coin ara: BTC, ETH, SOL...">
    <select id="signalFilter">
      <option value="all">Tüm sinyaller</option>
      <option value="buy">AL sinyali olanlar</option>
      <option value="strong">GÜÇLÜ AL olanlar</option>
      <option value="sell">SAT sinyali olanlar</option>
    </select>
    <span id="status" class="status">Tarama başlatılmayı bekliyor.</span>
  </div>

  <div class="cards">
    <div class="card"><div class="label">Taranan coin</div><div class="value" id="totalCoins">0</div></div>
    <div class="card"><div class="label">Güncel AL sinyali</div><div class="value good" id="buyCount">0</div></div>
    <div class="card"><div class="label">GÜÇLÜ AL sinyali</div><div class="value good" id="strongCount">0</div></div>
    <div class="card"><div class="label">Son tarama</div><div class="value" id="scanTime" style="font-size:16px">—</div></div>
  </div>

  <div class="layout">
    <div>
      <section class="panel">
        <h2>Coin Tarama Tablosu</h2>
        <p>Satıra dokunarak coin grafiğini ve beş stratejinin analizini aç.</p>
        <div class="table-wrap">
          <table>
            <thead><tr>
              <th>Coin</th><th>Fiyat</th><th>24s Değişim</th><th>24s Hacim (USDT)</th>
              <th>4s EMA200</th><th>Aktif sinyaller</th>
            </tr></thead>
            <tbody id="coinRows">
              <tr><td colspan="6" class="muted">Tarama başlatılınca coinler burada görünecek.</td></tr>
            </tbody>
          </table>
        </div>
      </section>

      <section class="panel">
        <h2 id="chartTitle">Coin Grafiği</h2>
        <div class="chartbox"><canvas id="chart"></canvas></div>
        <div class="legend">
          <span><i class="dot" style="background:#dbe5f1"></i>Fiyat</span>
          <span><i class="dot" style="background:#f4c76b"></i>EMA20</span>
          <span><i class="dot" style="background:#76aaff"></i>EMA50</span>
          <span><i class="dot" style="background:#c58cff"></i>EMA200</span>
        </div>
        <p id="chartInfo">Tarama tamamlandığında bir coin seç.</p>
      </section>

      <section class="panel">
        <h2 id="historyTitle">Seçilen Stratejinin İşlem Geçmişi</h2>
        <div class="tabs" id="historyTabs"></div>
        <div class="table-wrap" id="historyTable">
          <table>
            <thead><tr><th>İşlem</th><th>Giriş</th><th>Çıkış</th><th>Sonuç</th><th>Net %</th></tr></thead>
            <tbody id="historyRows"><tr><td colspan="5" class="muted">Önce bir coin seç.</td></tr></tbody>
          </table>
        </div>
      </section>
    </div>

    <div>
      <section class="panel">
        <h2 id="selectedTitle">Seçilen Coin Hakkında</h2>
        <div id="coinDetails"><p>Tarama sonrası bir coin seçerek detayları görebilirsin.</p></div>
      </section>

      <section class="panel">
        <h2>Beş Bağımsız Strateji</h2>
        <p>Her strateji kendi koşullarıyla sinyal üretir. Diğer stratejilerin AL vermesi şart değildir.</p>
        <div id="strategyCards" class="strategy-list" style="margin-top:12px">
          <p>Henüz analiz yok.</p>
        </div>
      </section>

      <section class="panel">
        <h2>Risk ve Hesaplama</h2>
        <div class="stats">
          <div class="stat"><small>Varsayılan zarar kes</small><b>1,5 × ATR</b></div>
          <div class="stat"><small>GÜÇLÜ AL hedefi</small><b>2,5 × ATR</b></div>
          <div class="stat"><small>Diğer strateji hedefi</small><b>2 × ATR</b></div>
        </div>
        <p>Alış komisyonu: %0,25</p>
        <p>Satış komisyonu: %0,25</p>
        <p>Net getiri hesabı komisyonları düşer. Kayma, emir defteri farkı ve gerçek emir dolumu hesaba dahil değildir.</p>
        <div class="notice">Bu site emir göndermez; yalnızca Binance Spot piyasa verilerini analiz eder. Geçmiş test sonuçları gelecekteki getiriyi garanti etmez.</div>
      </section>
    </div>
  </div>
</div>

<script>
"use strict";

const API_BASES = [
  "https://data-api.binance.vision",
  "https://api.binance.com"
];
const FEE = 0.0025;
const STRATEGIES = [
  {id:0,name:"1. Destek–Direnç + Trend",short:"Destek/Direnç"},
  {id:1,name:"2. EMA20–50 Geri Çekilme",short:"Geri Çekilme"},
  {id:2,name:"3. Teknik Analiz",short:"Teknik Analiz"},
  {id:3,name:"4. EMA20–50 Kesişimi",short:"EMA Kesişimi"},
  {id:4,name:"5. GÜÇLÜ AL",short:"GÜÇLÜ AL"}
];

let results = [];
let selectedSymbol = null;
let selectedData = null;
let historyStrategy = 4;
let scanning = false;

const $ = id => document.getElementById(id);
const fmt = (n,d=4) => Number.isFinite(n) ? n.toLocaleString("tr-TR",{maximumFractionDigits:d}) : "—";
const pct = n => Number.isFinite(n) ? (n>=0?"+":"")+n.toFixed(2)+"%" : "—";
const money = n => {
  if(!Number.isFinite(n)) return "—";
  if(n>=1e9) return (n/1e9).toFixed(2)+" Mr";
  if(n>=1e6) return (n/1e6).toFixed(2)+" Mn";
  if(n>=1e3) return (n/1e3).toFixed(1)+" B";
  return n.toFixed(0);
};
const sleep = ms => new Promise(r=>setTimeout(r,ms));

async function api(path){
  let lastError;
  for(const base of API_BASES){
    try{
      const res = await fetch(base+path,{cache:"no-store"});
      if(!res.ok) throw new Error("HTTP "+res.status);
      return await res.json();
    }catch(e){lastError=e;}
  }
  throw lastError || new Error("Binance verisine ulaşılamadı.");
}

function parseKlines(raw){
  return raw.map(k=>({
    time:+k[0],open:+k[1],high:+k[2],low:+k[3],close:+k[4],
    volume:+k[5],closeTime:+k[6],quoteVolume:+k[7]
  }));
}
function closedCandles(candles){
  const now=Date.now();
  return candles.filter(c=>c.closeTime<now);
}
function ema(values,period){
  const out=Array(values.length).fill(null);
  const k=2/(period+1);
  let prev=null;
  for(let i=0;i<values.length;i++){
    if(!Number.isFinite(values[i])) continue;
    if(prev===null){
      if(i<period-1) continue;
      const chunk=values.slice(i-period+1,i+1);
      if(chunk.some(v=>!Number.isFinite(v))) continue;
      prev=chunk.reduce((a,b)=>a+b,0)/period;
    }else prev=values[i]*k+prev*(1-k);
    out[i]=prev;
  }
  return out;
}
function sma(values,period){
  return values.map((v,i)=>{
    if(i<period-1)return null;
    const a=values.slice(i-period+1,i+1);
    if(a.some(x=>!Number.isFinite(x)))return null;
    return a.reduce((s,x)=>s+x,0)/period;
  });
}
function calcRSI(closes,period=14){
  const out=Array(closes.length).fill(null);
  let gain=0,loss=0;
  for(let i=1;i<=period;i++){
    const d=closes[i]-closes[i-1];
    gain+=Math.max(d,0);loss+=Math.max(-d,0);
  }
  if(closes.length<=period)return out;
  let ag=gain/period,al=loss/period;
  out[period]=al===0?100:100-100/(1+ag/al);
  for(let i=period+1;i<closes.length;i++){
    const d=closes[i]-closes[i-1];
    ag=(ag*(period-1)+Math.max(d,0))/period;
    al=(al*(period-1)+Math.max(-d,0))/period;
    out[i]=al===0?100:100-100/(1+ag/al);
  }
  return out;
}
function calcATR(candles,period=14){
  const tr=candles.map((c,i)=>{
    if(i===0)return c.high-c.low;
    return Math.max(c.high-c.low,Math.abs(c.high-candles[i-1].close),Math.abs(c.low-candles[i-1].close));
  });
  const out=Array(candles.length).fill(null);
  if(candles.length<=period)return out;
  let v=tr.slice(1,period+1).reduce((a,b)=>a+b,0)/period;
  out[period]=v;
  for(let i=period+1;i<candles.length;i++){v=(v*(period-1)+tr[i])/period;out[i]=v;}
  return out;
}
function calcADX(candles,period=14){
  const n=candles.length, tr=Array(n).fill(0), plus=Array(n).fill(0), minus=Array(n).fill(0);
  for(let i=1;i<n;i++){
    const up=candles[i].high-candles[i-1].high;
    const down=candles[i-1].low-candles[i].low;
    plus[i]=up>down&&up>0?up:0;
    minus[i]=down>up&&down>0?down:0;
    tr[i]=Math.max(candles[i].high-candles[i].low,Math.abs(candles[i].high-candles[i-1].close),Math.abs(candles[i].low-candles[i-1].close));
  }
  const out=Array(n).fill(null);
  if(n<period*2+1)return out;
  let trS=0,pS=0,mS=0;
  for(let i=1;i<=period;i++){trS+=tr[i];pS+=plus[i];mS+=minus[i];}
  const dx=[];
  for(let i=period;i<n;i++){
    if(i>period){trS=trS-trS/period+tr[i];pS=pS-pS/period+plus[i];mS=mS-mS/period+minus[i];}
    const p=trS?pS/trS*100:0,m=trS?mS/trS*100:0;
    const d=p+m?Math.abs(p-m)/(p+m)*100:0;
    dx.push(d);
    if(dx.length===period){
      let adx=dx.reduce((a,b)=>a+b,0)/period;
      out[i]=adx;
      for(let j=i+1;j<n;j++){
        const trj=tr[j],up=candles[j].high-candles[j-1].high,down=candles[j-1].low-candles[j].low;
        trS=trS-trS/period+trj;
        pS=pS-pS/period+(up>down&&up>0?up:0);
        mS=mS-mS/period+(down>up&&down>0?down:0);
        const pj=trS?pS/trS*100:0,mj=trS?mS/trS*100:0;
        const dxj=pj+mj?Math.abs(pj-mj)/(pj+mj)*100:0;
        adx=adx-(adx/period)+(dxj/period);
        out[j]=adx;
      }
      break;
    }
  }
  return out;
}
function macdCalc(closes){
  const e12=ema(closes,12),e26=ema(closes,26);
  const line=closes.map((_,i)=>e12[i]!==null&&e26[i]!==null?e12[i]-e26[i]:null);
  const signal=ema(line,9);
  const hist=line.map((v,i)=>v!==null&&signal[i]!==null?v-signal[i]:null);
  return {line,signal,hist};
}
function minLow(candles,start,end){
  let v=Infinity;
  for(let i=start;i<=end;i++)v=Math.min(v,candles[i].low);
  return v;
}
function maxHigh(candles,start,end){
  let v=-Infinity;
  for(let i=start;i<=end;i++)v=Math.max(v,candles[i].high);
  return v;
}
function prepare(h1,h4){
  const close=h1.map(c=>c.close);
  const e20=ema(close,20),e50=ema(close,50),e100=ema(close,100),e200=ema(close,200);
  const rsi=calcRSI(close),atr=calcATR(h1),adx=calcADX(h1),macd=macdCalc(close);
  const volAvg=sma(h1.map(c=>c.volume),20);
  const h4ema=ema(h4.map(c=>c.close),200);
  let j=0;
  const gate=h1.map(c=>{
    while(j+1<h4.length&&h4[j+1].closeTime<c.closeTime)j++;
    const h=h4[j];
    if(!h||h.closeTime>=c.closeTime)return {ok:false,close:null,ema:null};
    return {ok:Number.isFinite(h4ema[j])&&h.close>h4ema[j],close:h.close,ema:h4ema[j]};
  });
  return {h1,h4,e20,e50,e100,e200,rsi,atr,adx,macd,volAvg,gate};
}
function trendOK(d,i){
  return Number.isFinite(d.adx[i])&&d.adx[i]>=16&&
    Number.isFinite(d.atr[i])&&d.atr[i]>0&&
    Number.isFinite(d.e20[i])&&Number.isFinite(d.e50[i])&&
    Math.abs(d.e20[i]-d.e50[i])>d.atr[i]*0.08;
}
function conditions(d,i){
  const c=d.h1[i],p=d.h1[i-1];
  if(!c||!p)return Array(5).fill(null);
  const gate=!!d.gate[i]?.ok;
  const trend=trendOK(d,i);
  const e20=d.e20[i],e50=d.e50[i],e100=d.e100[i],e200=d.e200[i];
  const prev20High=maxHigh(d.h1,i-20,i-1);
  const support=minLow(d.h1,i-20,i-1);
  const macdUp=d.macd.hist[i]!==null&&d.macd.hist[i-1]!==null&&
    d.macd.hist[i]>0&&d.macd.hist[i]>d.macd.hist[i-1];
  const macdDown=d.macd.hist[i]!==null&&d.macd.hist[i-1]!==null&&
    d.macd.hist[i]<0&&d.macd.hist[i]<d.macd.hist[i-1];
  const r=d.rsi[i];
  const commonReady=gate&&Number.isFinite(e20)&&Number.isFinite(e50)&&Number.isFinite(d.atr[i]);
  if(!commonReady)return Array(5).fill(null);

  // 1) Destek bölgesinden tepki + trend
  const nearSupport=c.low<=support+d.atr[i]*0.35;
  const bullishCandle=c.close>c.open&&c.close>p.close&&c.close>e20;
  const s1=trend&&e20>e50&&nearSupport&&bullishCandle;
  const x1=c.close<support-d.atr[i]*0.35||(e20<e50&&c.close<e50);

  // 2) EMA20 geri çekilme ve yeniden üstünde kapanış
  const touched20=p.low<=d.e20[i-1]+d.atr[i-1]*0.25;
  const reclaim=c.close>e20&&c.close>c.open&&p.close<=d.e20[i-1]+d.atr[i-1]*0.5;
  const s2=trend&&e20>e50&&touched20&&reclaim;
  const x2=e20<e50||c.close<e50;

  // 3) Teknik analiz: RSI + MACD + EMA + mum
  const s3=trend&&e20>e50&&c.close>e20&&r>=50&&r<=70&&macdUp&&c.close>c.open;
  const x3=(e20<e50&&macdDown)||(r<42&&c.close<e50);

  // 4) EMA20/50 kesişimi: kapanmış mumda kesişim
  const s4=d.e20[i-1]<=d.e50[i-1]&&e20>e50&&c.close>e20&&trend;
  const x4=d.e20[i-1]>=d.e50[i-1]&&e20<e50;

  // 5) GÜÇLÜ AL: belirtilen koşullar aynı kapanmış mumda
  const vAvg=d.volAvg[i-1];
  const s5=c.close>e200&&e20>e50&&e50>e100&&
    c.close>prev20High&&Number.isFinite(vAvg)&&vAvg>0&&
    c.volume>1.5*vAvg&&r>=55&&r<=80&&trend;
  const x5=c.close<e50&&d.e20[i-1]>=d.e50[i-1]&&e20<e50;

  return [
    {buy:s1,sell:x1,reason:s1?"Destekten yukarı tepki":x1?"Destek/trend bozuldu":"Kurulum bekleniyor"},
    {buy:s2,sell:x2,reason:s2?"EMA20 geri çekilme onayı":x2?"EMA20/50 görünümü bozuldu":"Geri çekilme bekleniyor"},
    {buy:s3,sell:x3,reason:s3?"RSI + MACD + trend onayı":x3?"Momentum zayıfladı":"Teknik onay bekleniyor"},
    {buy:s4,sell:x4,reason:s4?"EMA20, EMA50'yi yukarı kesti":x4?"EMA20, EMA50'yi aşağı kesti":"Kesişim yok"},
    {buy:s5,sell:x5,reason:s5?"Tüm GÜÇLÜ AL koşulları tamam":x5?"GÜÇLÜ SAT koşulu":"GÜÇLÜ AL koşulları tamam değil"}
  ];
}
function backtest(d,strategyId){
  const trades=[];
  const start=Math.max(205,d.h1.length-260);
  let i=start;
  while(i<d.h1.length-1){
    const sig=conditions(d,i)[strategyId];
    if(!sig||!sig.buy){i++;continue;}
    const entry=d.h1[i].close,atr=d.atr[i];
    if(!Number.isFinite(atr)||atr<=0){i++;continue;}
    const stop=entry-1.5*atr;
    const target=entry+(strategyId===4?2.5:2)*atr;
    let exit=entry,exitIndex=Math.min(i+24,d.h1.length-1),why="Süre";
    for(let j=i+1;j<=Math.min(i+24,d.h1.length-1);j++){
      const c=d.h1[j];
      // Aynı mumda iki seviyeye de dokunulursa muhafazakâr biçimde stop kabul edilir.
      if(c.low<=stop){exit=stop;exitIndex=j;why="Zarar kes";break;}
      if(c.high>=target){exit=target;exitIndex=j;why="Kâr al";break;}
      if(j===Math.min(i+24,d.h1.length-1)){exit=c.close;exitIndex=j;why="Süre sonu";}
    }
    const gross=(exit-entry)/entry;
    const net=(1+gross)*(1-FEE)*(1-FEE)-1;
    trades.push({entryTime:d.h1[i].time,exitTime:d.h1[exitIndex].time,entry,exit,net:net*100,why});
    i=exitIndex+1;
  }
  const wins=trades.filter(t=>t.net>0).length;
  return {trades,wins,losses:trades.length-wins,winRate:trades.length?wins/trades.length*100:0,
    net:trades.reduce((s,t)=>s+t.net,0)};
}
function latestSignal(d){
  const i=d.h1.length-1;
  const sig=conditions(d,i);
  const levels={};
  const price=d.h1[i]?.close,atr=d.atr[i];
  if(Number.isFinite(price)&&Number.isFinite(atr)){
    levels.stop=price-1.5*atr;
    levels.target=price+2.5*atr;
  }
  return {i,sig,levels};
}
async function fetchSymbolData(symbol){
  const [raw1,raw4]=await Promise.all([
    api(`/api/v3/klines?symbol=${symbol}&interval=1h&limit=300`),
    api(`/api/v3/klines?symbol=${symbol}&interval=4h&limit=300`)
  ]);
  const h1=closedCandles(parseKlines(raw1));
  const h4=closedCandles(parseKlines(raw4));
  if(h1.length<220||h4.length<205)throw new Error("Yeterli mum verisi yok");
  const d=prepare(h1,h4);
  const last=latestSignal(d);
  const stats=STRATEGIES.map(s=>backtest(d,s.id));
  return {symbol,d,last,stats};
}
function signalLabel(s){
  if(!s)return {text:"VERİ YOK",cls:"neutral"};
  if(s.buy)return {text:s.id===4?"GÜÇLÜ AL":"AL",cls:"buy"};
  if(s.sell)return {text:s.id===4?"GÜÇLÜ SAT":"SAT",cls:"sell"};
  return {text:"BEKLE",cls:"neutral"};
}
function activeSignals(r){
  return r.last.sig.map((s,i)=>s&&s.buy?STRATEGIES[i].short:null).filter(Boolean);
}
function currentRowSignal(r){
  const buys=activeSignals(r);
  if(buys.includes("GÜÇLÜ AL"))return {text:"GÜÇLÜ AL",cls:"buy"};
  if(buys.length)return {text:"AL · "+buys.join(", "),cls:"buy"};
  if(r.last.sig.some(s=>s&&s.sell))return {text:"SAT",cls:"sell"};
  return {text:"BEKLE",cls:"neutral"};
}
function renderRows(){
  const q=$("search").value.trim().toUpperCase();
  const f=$("signalFilter").value;
  let list=results.filter(r=>r.symbol.includes(q));
  if(f==="buy")list=list.filter(r=>activeSignals(r).length>0);
  if(f==="strong")list=list.filter(r=>activeSignals(r).includes("GÜÇLÜ AL"));
  if(f==="sell")list=list.filter(r=>r.last.sig.some(s=>s&&s.sell));
  list.sort((a,b)=>{
    const ap=activeSignals(a).includes("GÜÇLÜ AL")?3:activeSignals(a).length?2:a.last.sig.some(s=>s&&s.sell)?1:0;
    const bp=activeSignals(b).includes("GÜÇLÜ AL")?3:activeSignals(b).length?2:b.last.sig.some(s=>s&&s.sell)?1:0;
    return bp-ap;
  });
  $("coinRows").innerHTML=list.map(r=>{
    const i=r.d.h1.length-1;
    const c=r.d.h1[i];
    const h4=r.d.gate[i];
    const sig=currentRowSignal(r);
    return `<tr data-symbol="${r.symbol}" class="${r.symbol===selectedSymbol?"selected":""}">
      <td class="coin">${r.symbol.replace("USDT","")}<span class="muted">/USDT</span></td>
      <td>${fmt(c.close,8)}</td>
      <td class="muted">—</td>
      <td class="muted">—</td>
      <td class="${h4.ok?"good":"error"}">${h4.ok?"OLUMLU":"OLUMSUZ"}</td>
      <td class="${sig.cls}">${sig.text}</td>
    </tr>`;
  }).join("")||`<tr><td colspan="6" class="muted">Bu aramada sonuç yok.</td></tr>`;
  // 24 saatlik veriler, sinyal hesaplamasından bağımsız olarak tabloya eklenir.
  for(const tr of $("coinRows").querySelectorAll("tr[data-symbol]")){
    tr.addEventListener("click",()=>selectCoin(tr.dataset.symbol));
  }
}
let tickerMap=new Map();
function renderRowsWithTicker(){
  renderRows();
  for(const tr of $("coinRows").querySelectorAll("tr[data-symbol]")){
    const t=tickerMap.get(tr.dataset.symbol);
    if(!t)continue;
    const cells=tr.querySelectorAll("td");
    cells[2].textContent=pct(Number(t.priceChangePercent));
    cells[2].className=Number(t.priceChangePercent)>=0?"good":"error";
    cells[3].textContent=money(Number(t.quoteVolume));
    cells[3].title=Number(t.quoteVolume).toLocaleString("en-US")+" USDT";
  }
}
function renderDetails(r){
  const i=r.d.h1.length-1,c=r.d.h1[i],d=r.d;
  const gate=d.gate[i];
  const buys=activeSignals(r);
  const atr=d.atr[i],stop=c.close-1.5*atr,target=c.close+2.5*atr;
  $("selectedTitle").textContent=r.symbol+" · Seçilen Coin Hakkında";
  $("coinDetails").innerHTML=`
    <div class="stats">
      <div class="stat"><small>Son kapanış</small><b>${fmt(c.close,8)}</b></div>
      <div class="stat"><small>RSI (14)</small><b>${fmt(d.rsi[i],2)}</b></div>
      <div class="stat"><small>ADX (14)</small><b>${fmt(d.adx[i],2)}</b></div>
      <div class="stat"><small>ATR (14)</small><b>${fmt(atr,8)}</b></div>
      <div class="stat"><small>4s EMA200 filtresi</small><b class="${gate.ok?"good":"error"}">${gate.ok?"OLUMLU":"OLUMSUZ"}</b></div>
      <div class="stat"><small>Aktif AL stratejisi</small><b>${buys.length}</b></div>
    </div>
    <p>EMA20: <b>${fmt(d.e20[i],8)}</b></p>
    <p>EMA50: <b>${fmt(d.e50[i],8)}</b></p>
    <p>EMA100: <b>${fmt(d.e100[i],8)}</b></p>
    <p>EMA200: <b>${fmt(d.e200[i],8)}</b></p>
    <div class="stats">
      <div class="stat"><small>Örnek zarar kes</small><b class="error">${fmt(stop,8)}</b></div>
      <div class="stat"><small>GÜÇLÜ AL hedefi</small><b class="good">${fmt(target,8)}</b></div>
      <div class="stat"><small>ATR</small><b>${fmt(atr,8)}</b></div>
    </div>
    <p class="small">Zarar kes ve hedef değerleri gösterim amaçlıdır; bu fiyatın alım önerisi olduğu anlamına gelmez. 24 saatlik hacim sinyal koşulu değildir.</p>`;
}
function renderStrategies(r){
  $("strategyCards").innerHTML=STRATEGIES.map(s=>{
    const sig=r.last.sig[s.id],st=r.stats[s.id],label=signalLabel(sig);
    const reasons=sig?sig.reason:"4s EMA200 verisi hazır değil";
    return `<div class="strategy">
      <div class="strategy-head">
        <span class="strategy-name">${s.name}</span>
        <span class="${label.cls}">${label.text}</span>
      </div>
      <div class="strategy-detail">${reasons}</div>
      <div class="stats">
        <div class="stat"><small>Geçmiş işlem</small><b>${st.trades.length}</b></div>
        <div class="stat"><small>Başarı oranı</small><b>${st.trades.length?st.winRate.toFixed(1)+"%":"—"}</b></div>
        <div class="stat"><small>Komisyon sonrası net</small><b class="${st.net>=0?"good":"error"}">${st.trades.length?pct(st.net):"—"}</b></div>
      </div>
      <button onclick="showHistory(${s.id})">Bu stratejinin geçmişi</button>
    </div>`;
  }).join("");
}
function selectCoin(symbol){
  const r=results.find(x=>x.symbol===symbol);
  if(!r)return;
  selectedSymbol=symbol;selectedData=r;
  renderRowsWithTicker();
  renderDetails(r);renderStrategies(r);drawChart(r);
  showHistory(historyStrategy);
}
function drawChart(r){
  const canvas=$("chart"),rect=canvas.getBoundingClientRect();
  const dpr=window.devicePixelRatio||1;
  canvas.width=Math.max(300,Math.floor(rect.width*dpr));
  canvas.height=Math.max(200,Math.floor(rect.height*dpr));
  const ctx=canvas.getContext("2d");
  ctx.scale(dpr,dpr);
  const w=canvas.width/dpr,h=canvas.height/dpr;
  ctx.clearRect(0,0,w,h);
  const d=r.d,from=Math.max(0,d.h1.length-100),n=d.h1.length-from;
  const cs=d.h1.slice(from);
  const vals=cs.flatMap((c,j)=>[c.high,c.low,d.e20[from+j],d.e50[from+j],d.e200[from+j]]).filter(Number.isFinite);
  if(!vals.length)return;
  let min=Math.min(...vals),max=Math.max(...vals),pad=(max-min)*.08||1;
  min-=pad;max+=pad;
  const left=8,right=58,top=12,bottom=20,pw=w-left-right,ph=h-top-bottom;
  const x=i=>left+(i/(n-1||1))*pw;
  const y=v=>top+(max-v)/(max-min)*ph;
  ctx.font="10px Arial";ctx.strokeStyle="#243143";ctx.fillStyle="#78879a";
  for(let k=0;k<=4;k++){
    const yy=top+ph*k/4;
    ctx.beginPath();ctx.moveTo(left,yy);ctx.lineTo(w-right,yy);ctx.stroke();
    ctx.fillText(fmt(max-(max-min)*k/4,5),w-right+5,yy+3);
  }
  const cw=Math.max(2,pw/n*.58);
  cs.forEach((c,j)=>{
    const xx=x(j),up=c.close>=c.open;
    ctx.strokeStyle=up?"#28d69b":"#ff6878";
    ctx.fillStyle=ctx.strokeStyle;
    ctx.beginPath();ctx.moveTo(xx,y(c.high));ctx.lineTo(xx,y(c.low));ctx.stroke();
    const yo=y(Math.max(c.open,c.close)),yh=y(Math.min(c.open,c.close));
    ctx.fillRect(xx-cw/2,yo,cw,Math.max(1,yh-yo));
  });
  function line(arr,color){
    ctx.strokeStyle=color;ctx.lineWidth=1.5;ctx.beginPath();let started=false;
    for(let j=0;j<n;j++){
      const v=arr[from+j];
      if(!Number.isFinite(v)){started=false;continue;}
      if(!started){ctx.moveTo(x(j),y(v));started=true;}else ctx.lineTo(x(j),y(v));
    }
    ctx.stroke();ctx.lineWidth=1;
  }
  line(d.e20,"#f4c76b");line(d.e50,"#76aaff");line(d.e200,"#c58cff");
  $("chartTitle").textContent=r.symbol+" · 1 saatlik mum grafiği";
  $("chartInfo").textContent="Son 100 kapanmış mum · Fiyat ve EMA20/50/200";
}
function showHistory(id){
  historyStrategy=id;
  const r=selectedData;
  $("historyTabs").innerHTML=STRATEGIES.map(s=>
    `<button class="${s.id===id?"active":""}" onclick="showHistory(${s.id})">${s.short}</button>`
  ).join("");
  $("historyTitle").textContent=(r?r.symbol:"Coin")+" · "+STRATEGIES[id].name+" geçmişi";
  if(!r){
    $("historyRows").innerHTML='<tr><td colspan="5" class="muted">Önce bir coin seç.</td></tr>';return;
  }
  const trades=r.stats[id].trades.slice().reverse();
  $("historyRows").innerHTML=trades.map(t=>`
    <tr>
      <td>${new Date(t.entryTime).toLocaleString("tr-TR")}</td>
      <td>${fmt(t.entry,8)}</td>
      <td>${fmt(t.exit,8)}</td>
      <td>${t.why}</td>
      <td class="${t.net>=0?"good":"error"}">${pct(t.net)}</td>
    </tr>`).join("")||'<tr><td colspan="5" class="muted">Bu dönem için geçmiş AL işlemi bulunamadı.</td></tr>';
}
async function worker(queue,fn){
  while(queue.length){
    const item=queue.shift();
    try{await fn(item);}catch(e){/* Tek coin hatası tüm taramayı durdurmaz. */}
  }
}
async function startScan(){
  if(scanning)return;
  scanning=true;
  $("scanBtn").disabled=true;
  $("status").textContent="Binance Spot pariteleri alınıyor...";
  results=[];tickerMap.clear();
  $("coinRows").innerHTML='<tr><td colspan="6">Piyasa verileri yükleniyor...</td></tr>';
  try{
    const [exchange,tickers]=await Promise.all([
      api("/api/v3/exchangeInfo"),
      api("/api/v3/ticker/24hr")
    ]);
    for(const t of tickers)tickerMap.set(t.symbol,t);
    const valid=new Set(exchange.symbols
      .filter(s=>s.status==="TRADING"&&s.isSpotTradingAllowed&&s.quoteAsset==="USDT")
      .map(s=>s.symbol)
      .filter(s=>!/(UP|DOWN|BULL|BEAR)USDT$/.test(s))
      .filter(s=>!["USDCUSDT","FDUSDUSDT","TUSDUSDT","USDPUSDT","DAIUSDT","EURUSDT"].includes(s)));
    const symbols=[...valid];
    let done=0,failed=0;
    $("status").textContent=`${symbols.length} Spot paritesi taranıyor...`;
    const queue=symbols.slice();
    await Promise.all(Array.from({length:5},()=>worker(queue,async symbol=>{
      try{
        const r=await fetchSymbolData(symbol);
        const t=tickerMap.get(symbol);
        r.ticker=t||null;
        results.push(r);
      }catch(e){failed++;}
      done++;
      if(done%5===0||done===symbols.length){
        $("status").textContent=`Tarama: ${done}/${symbols.length} · Veri alınamayan: ${failed}`;
        $("totalCoins").textContent=results.length;
        $("buyCount").textContent=results.filter(r=>activeSignals(r).length).length;
        $("strongCount").textContent=results.filter(r=>activeSignals(r).includes("GÜÇLÜ AL")).length;
        renderRowsWithTicker();
      }
    })));
    results.sort((a,b)=>a.symbol.localeCompare(b.symbol));
    $("scanTime").textContent=new Date().toLocaleTimeString("tr-TR",{hour:"2-digit",minute:"2-digit"});
    $("totalCoins").textContent=results.length;
    $("buyCount").textContent=results.filter(r=>activeSignals(r).length).length;
    $("strongCount").textContent=results.filter(r=>activeSignals(r).includes("GÜÇLÜ AL")).length;
    renderRowsWithTicker();
    if(selectedSymbol&&results.some(r=>r.symbol===selectedSymbol))selectCoin(selectedSymbol);
    else if(results.length)selectCoin(results.find(r=>activeSignals(r).includes("GÜÇLÜ AL"))?.symbol||results[0].symbol);
    $("status").textContent=`Tamamlandı · ${results.length} coin analiz edildi · ${failed} coin atlandı`;
  }catch(e){
    $("status").innerHTML=`<span class="error">Veri alınamadı: ${e.message}</span>`;
    $("coinRows").innerHTML='<tr><td colspan="6" class="error">Binance verisine ulaşılamadı. Biraz sonra tekrar dene.</td></tr>';
  }finally{
    scanning=false;$("scanBtn").disabled=false;
  }
}
$("scanBtn").addEventListener("click",startScan);
$("refreshBtn").addEventListener("click",async()=>{
  if(!selectedSymbol){$("status").textContent="Önce bir coin seç.";return;}
  $("status").textContent=selectedSymbol+" yenileniyor...";
  try{
    const r=await fetchSymbolData(selectedSymbol);
    r.ticker=tickerMap.get(selectedSymbol)||null;
    const ix=results.findIndex(x=>x.symbol===selectedSymbol);
    if(ix>=0)results[ix]=r;else results.push(r);
    selectCoin(selectedSymbol);
    $("status").textContent=selectedSymbol+" güncellendi.";
  }catch(e){$("status").textContent="Yenileme başarısız: "+e.message;}
});
$("search").addEventListener("input",renderRowsWithTicker);
$("signalFilter").addEventListener("change",renderRowsWithTicker);
window.addEventListener("resize",()=>{if(selectedData)drawChart(selectedData);});
</script>
</body>
</html>
