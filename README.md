<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Kripto Spot Tarayıcı Pro</title>

<style>
:root {
  --bg:#f3f5f9;
  --card:#ffffff;
  --text:#172033;
  --muted:#475569;
  --border:#d8dee9;
  --green:#15803d;
  --red:#b91c1c;
  --orange:#92400e;
  --blue:#1d4ed8;
}
* {box-sizing:border-box}
body {
  margin:0;
  padding:16px;
  background:var(--bg);
  color:var(--text);
  font-family:Arial,Helvetica,sans-serif;
  font-size:14px;
}
h1,h2,h3,p,label,span,div,td,th,button,input,select {
  color:var(--text);
}
h1 {font-size:25px;margin:0 0 8px}
h2 {font-size:18px;margin:0 0 12px}
small,.muted {color:var(--muted)!important}
header {margin-bottom:16px}
.subtitle {color:var(--muted);line-height:1.5}
.cards {
  display:grid;
  grid-template-columns:repeat(auto-fit,minmax(145px,1fr));
  gap:10px;
  margin:16px 0;
}
.card,.panel {
  background:var(--card);
  border:1px solid var(--border);
  border-radius:12px;
  padding:14px;
  min-width:0;
}
.card .label {font-size:12px;color:var(--muted);margin-bottom:8px}
.card .value {font-size:21px;font-weight:700}
.toolbar {
  display:flex;
  flex-wrap:wrap;
  gap:9px;
  margin-bottom:12px;
  align-items:center;
}
input,select,button {
  font:inherit;
  border:1px solid #cbd5e1;
  border-radius:8px;
  padding:10px 12px;
  background:#fff;
  color:#172033;
}
input {min-width:150px;flex:1}
button {
  cursor:pointer;
  background:#1d4ed8;
  color:#fff;
  border-color:#1d4ed8;
  font-weight:700;
}
button:disabled {opacity:.55;cursor:not-allowed}
button.secondary {background:#fff;color:#172033;border-color:#cbd5e1}
.status {
  padding:10px 12px;
  border-radius:8px;
  background:#e8eefc;
  color:#1e3a8a;
  margin-bottom:12px;
  line-height:1.5;
}
.progress {
  height:7px;background:#e2e8f0;border-radius:8px;
  overflow:hidden;margin:8px 0 14px;
}
.progress div {height:100%;width:0;background:#2563eb;transition:width .2s}
.table-wrap {overflow:auto;max-height:65vh;border-radius:8px}
table {width:100%;border-collapse:collapse;background:#fff;min-width:1000px}
th,td {
  padding:10px 9px;
  border-bottom:1px solid #e2e8f0;
  text-align:left;
  white-space:nowrap;
}
th {
  background:#edf1f7;
  color:#111827;
  position:sticky;top:0;z-index:1;
  font-size:12px;
}
td {font-size:13px}
tr:hover td {background:#f8fafc}
.pill {
  display:inline-block;padding:5px 8px;border-radius:7px;
  font-weight:700;font-size:11px;
}
.buy {background:#dcfce7;color:#166534!important}
.sell {background:#fee2e2;color:#991b1b!important}
.wait {background:#e2e8f0;color:#334155!important}
.warn {background:#fef3c7;color:#92400e!important}
.good {color:var(--green)!important;font-weight:700}
.bad {color:var(--red)!important;font-weight:700}
.section {margin-top:14px}
.note {font-size:12px;line-height:1.6;color:var(--muted)}
#details {line-height:1.7}
#backtestTable {min-width:750px}
footer {margin-top:18px}
@media(max-width:600px) {
  body {padding:9px}
  h1 {font-size:21px}
  .card .value {font-size:18px}
  .panel {padding:10px}
}
</style>
</head>

<body>
<header>
  <h1>Kripto Spot Tarayıcı Pro</h1>
  <div class="subtitle">
    Binance Spot · 4 saatlik EMA200 filtresi · 1 saatlik kapanmış mumlar ·
    ATR stop/hedef · 24 saatlik hacim bilgisi
  </div>
</header>

<div class="cards">
  <div class="card">
    <div class="label">Taranan pariteler</div>
    <div class="value" id="total">0</div>
  </div>
  <div class="card">
    <div class="label">Güçlü AL</div>
    <div class="value good" id="strongBuy">0</div>
  </div>
  <div class="card">
    <div class="label">AL</div>
    <div class="value good" id="buyCount">0</div>
  </div>
  <div class="card">
    <div class="label">SAT / Güçlü SAT</div>
    <div class="value bad" id="sellCount">0</div>
  </div>
  <div class="card">
    <div class="label">Uygun değil</div>
    <div class="value" id="unsuitable">0</div>
  </div>
</div>

<div class="panel">
  <div class="toolbar">
    <button id="scanBtn" onclick="startScan()">Taramayı Başlat</button>
    <button class="secondary" onclick="stopScan()">Durdur</button>
    <button class="secondary" onclick="exportCSV()">CSV İndir</button>
    <input id="search" placeholder="Coin ara: BTC, ETH, SOL..." oninput="renderTable()">
    <select id="filter" onchange="renderTable()">
      <option value="all">Tüm sonuçlar</option>
      <option value="buy">AL sinyalleri</option>
      <option value="strong">Güçlü AL</option>
      <option value="sell">SAT sinyalleri</option>
      <option value="eligible">İşleme uygun olanlar</option>
    </select>
    <select id="sort" onchange="renderTable()">
      <option value="score">Puana göre sırala</option>
      <option value="volume">24s hacme göre sırala</option>
      <option value="change">24s değişime göre sırala</option>
      <option value="symbol">Coin adına göre sırala</option>
    </select>
  </div>

  <div id="status" class="status">Tarama başlatılmayı bekliyor.</div>
  <div class="progress"><div id="progressBar"></div></div>

  <div class="table-wrap">
    <table>
      <thead>
        <tr>
          <th>Coin</th>
          <th>Sinyal</th>
          <th>Puan</th>
          <th>Fiyat</th>
          <th>24s değişim</th>
          <th>24s hacim (USDT)</th>
          <th>RSI</th>
          <th>ADX</th>
          <th>ATR</th>
          <th>Stop</th>
          <th>Hedef</th>
          <th>4s EMA200</th>
          <th>Detay</th>
        </tr>
      </thead>
      <tbody id="results">
        <tr><td colspan="13">Henüz tarama yapılmadı.</td></tr>
      </tbody>
    </table>
  </div>
</div>

<div class="panel section">
  <h2>Seçilen coin hakkında</h2>
  <div id="details" class="note">
    Ayrıntıları görmek için tablodaki bir coin adına tıkla.
  </div>
</div>

<div class="panel section">
  <h2>Geçmiş sinyal testi</h2>
  <p class="note">
    Son kapanmış 1 saatlik mumlardaki AL sinyallerini inceler.
    Alım ve satım için ayrı ayrı %0,25 komisyon varsayar.
    Bu test gelecekteki kârı garanti etmez; aynı mum içinde stop ve hedef
    birlikte görülürse temkinli biçimde stop önce kabul edilir.
  </p>
  <div class="toolbar">
    <select id="backtestCoin">
      <option value="">Önce tarama yap</option>
    </select>
    <button class="secondary" onclick="runBacktest()">Geçmiş testi çalıştır</button>
  </div>
  <div id="backtestSummary" class="note">Henüz test yapılmadı.</div>
  <div class="table-wrap">
    <table id="backtestTable">
      <thead>
        <tr>
          <th>Giriş zamanı</th>
          <th>Çıkış zamanı</th>
          <th>Giriş</th>
          <th>Çıkış</th>
          <th>Sonuç</th>
          <th>Net % (komisyon sonrası)</th>
        </tr>
      </thead>
      <tbody id="backtestRows">
        <tr><td colspan="6">Test sonucu burada gösterilir.</td></tr>
      </tbody>
    </table>
  </div>
</div>

<footer class="note">
  Bilgilendirme amaçlı teknik analiz aracıdır, otomatik emir göndermez.
  Kripto varlıklar yüksek risk taşır. Sinyal kârlılık garantisi değildir.
</footer>

<script>
"use strict";

const API = "https://api.binance.com";
const FEE = 0.0025;
const KLINE_LIMIT = 260;
const WORKERS = 4;

let marketData = [];
let scanning = false;
let scanToken = 0;
let selectedData = null;
let backtestCache = new Map();

const $ = id => document.getElementById(id);

function setStatus(text) { $("status").textContent = text; }
function setProgress(value) {
  $("progressBar").style.width = Math.max(0,Math.min(100,value)) + "%";
}
function num(v, digits=4) {
  if (!Number.isFinite(v)) return "—";
  return v.toLocaleString("en-US", {
    minimumFractionDigits:0, maximumFractionDigits:digits
  });
}
function money(v) {
  if (!Number.isFinite(v)) return "—";
  if (v >= 1e9) return (v/1e9).toFixed(2)+"B";
  if (v >= 1e6) return (v/1e6).toFixed(2)+"M";
  if (v >= 1e3) return (v/1e3).toFixed(2)+"K";
  return v.toFixed(0);
}
function pct(v) {
  if (!Number.isFinite(v)) return "—";
  return (v>0?"+":"")+v.toFixed(2)+"%";
}
function timeText(t) {
  if (!Number.isFinite(t)) return "—";
  return new Date(t).toLocaleString("tr-TR");
}
function esc(s) {
  return String(s).replace(/[&<>"']/g, c => ({
    "&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"
  })[c]);
}

async function getJSON(url, token) {
  if (token !== undefined && token !== scanToken) throw new Error("Tarama durduruldu");
  const response = await fetch(url);
  if (!response.ok) {
    if (response.status === 429 || response.status === 418) {
      throw new Error("Binance istek sınırı. Biraz bekleyip yeniden dene.");
    }
    throw new Error("Binance API hatası: HTTP "+response.status);
  }
  return response.json();
}

function ema(values, period) {
  const out = Array(values.length).fill(null);
  if (values.length < period) return out;
  let sum = 0;
  for (let i=0;i<period;i++) sum += values[i];
  let prev = sum/period;
  out[period-1] = prev;
  const alpha = 2/(period+1);
  for (let i=period;i<values.length;i++) {
    prev = values[i]*alpha + prev*(1-alpha);
    out[i] = prev;
  }
  return out;
}

function rma(values, period) {
  const out = Array(values.length).fill(null);
  let seed = [];
  let prev = null;
  for (let i=0;i<values.length;i++) {
    const v = values[i];
    if (!Number.isFinite(v)) continue;
    if (prev === null) {
      seed.push(v);
      if (seed.length === period) {
        prev = seed.reduce((a,b)=>a+b,0)/period;
        out[i] = prev;
      }
    } else {
      prev = (prev*(period-1)+v)/period;
      out[i] = prev;
    }
  }
  return out;
}

function calcRSI(closes, period=14) {
  const gains = Array(closes.length).fill(null);
  const losses = Array(closes.length).fill(null);
  for (let i=1;i<closes.length;i++) {
    const d = closes[i]-closes[i-1];
    gains[i] = Math.max(d,0);
    losses[i] = Math.max(-d,0);
  }
  const avgG = rma(gains,period);
  const avgL = rma(losses,period);
  return closes.map((_,i)=>{
    if (avgG[i]===null || avgL[i]===null) return null;
    if (avgL[i]===0) return 100;
    return 100-(100/(1+avgG[i]/avgL[i]));
  });
}

function calcATR(candles, period=14) {
  const tr = Array(candles.length).fill(null);
  for (let i=1;i<candles.length;i++) {
    const c = candles[i], p = candles[i-1];
    tr[i] = Math.max(
      c.h-c.l,
      Math.abs(c.h-p.c),
      Math.abs(c.l-p.c)
    );
  }
  return rma(tr,period);
}

function calcADX(candles, period=14) {
  const n = candles.length;
  const plusDM = Array(n).fill(null);
  const minusDM = Array(n).fill(null);
  const tr = Array(n).fill(null);

  for (let i=1;i<n;i++) {
    const c=candles[i], p=candles[i-1];
    const up=c.h-p.h, down=p.l-c.l;
    plusDM[i]=(up>down && up>0)?up:0;
    minusDM[i]=(down>up && down>0)?down:0;
    tr[i]=Math.max(c.h-c.l,Math.abs(c.h-p.c),Math.abs(c.l-p.c));
  }

  const smTR=rma(tr,period);
  const smP=rma(plusDM,period);
  const smM=rma(minusDM,period);
  const dx=Array(n).fill(null);

  for(let i=0;i<n;i++) {
    if(!smTR[i] || smP[i]===null || smM[i]===null) continue;
    const pdi=100*smP[i]/smTR[i];
    const mdi=100*smM[i]/smTR[i];
    dx[i]=(pdi+mdi===0)?0:100*Math.abs(pdi-mdi)/(pdi+mdi);
  }
  return rma(dx,period);
}

function calcMACD(closes) {
  const e12=ema(closes,12), e26=ema(closes,26);
  const line=closes.map((_,i)=>
    e12[i]===null || e26[i]===null ? null : e12[i]-e26[i]
  );
  const validLine=line.map(v=>v===null?0:v);
  const signal=ema(validLine,9);
  const hist=line.map((v,i)=>
    v===null || signal[i]===null ? null : v-signal[i]
  );
  return {line,signal,hist};
}

function calcBBWidth(closes, period=20, mult=2) {
  const out=Array(closes.length).fill(null);
  for(let i=period-1;i<closes.length;i++) {
    const a=closes.slice(i-period+1,i+1);
    const mean=a.reduce((s,v)=>s+v,0)/period;
    const variance=a.reduce((s,v)=>s+(v-mean)**2,0)/period;
    const sd=Math.sqrt(variance);
    const upper=mean+mult*sd, lower=mean-mult*sd;
    out[i]=mean===0?0:(upper-lower)/mean*100;
  }
  return out;
}

function makeIndicators(candles) {
  const closes=candles.map(c=>c.c);
  return {
    ema20:ema(closes,20),
    ema50:ema(closes,50),
    ema100:ema(closes,100),
    ema200:ema(closes,200),
    rsi:calcRSI(closes,14),
    atr:calcATR(candles,14),
    adx:calcADX(candles,14),
    macd:calcMACD(closes),
    bbw:calcBBWidth(closes,20)
  };
}

function getClosedCandles(raw) {
  const now=Date.now();
  return raw
    .filter(x=>Number(x[6])<now)
    .map(x=>({
      t:Number(x[0]),
      o:Number(x[1]),
      h:Number(x[2]),
      l:Number(x[3]),
      c:Number(x[4]),
      v:Number(x[5]),
      ct:Number(x[6])
    }));
}

function calcSignal(candles, h4, ticker, symbol) {
  if(candles.length<220 || h4.length<205) return null;

  const I=makeIndicators(candles);
  const H=makeIndicators(h4);
  const i=candles.length-1;
  const c=candles[i];
  const prev=candles[i-1];

  // 4 saatlik filtre: en son KAPANMIŞ 4 saatlik mum.
  const hi=h4.length-1;
  const h4Close=h4[hi].c;
  const h4EMA=H.ema200[hi];
  if(h4EMA===null) return null;
  const htf=h4Close>h4EMA;

  const e20=I.ema20[i], e50=I.ema50[i];
  const rsi=I.rsi[i], atr=I.atr[i], adx=I.adx[i];
  const macd=I.macd;
  const macdBull=macd.line[i]!==null &&
    macd.signal[i]!==null &&
    macd.hist[i]!==null &&
    macd.line[i]>macd.signal[i] && macd.hist[i]>0;

  const macdBear=macd.line[i]!==null &&
    macd.signal[i]!==null &&
    macd.hist[i]!==null &&
    macd.line[i]<macd.signal[i] && macd.hist[i]<0;

  const trend=c.c>e50 && e20>e50;

  // Önceki 20 kapanmış mumun en yüksek seviyesi.
  const prev20=candles.slice(Math.max(0,i-20),i);
  const priorHigh=Math.max(...prev20.map(x=>x.h));
  const breakout=c.c>priorHigh;

  // EMA20 çevresine geri çekilme ve yükseliş mumuyla kapanış.
  const pullback=Number.isFinite(e20) &&
    Math.abs(c.c-e20)/e20<=0.012 &&
    c.l<=e20*1.012 &&
    c.c>e20 &&
    c.c>c.o;

  const trigger=breakout || pullback;

  const slopeIndex=Math.max(0,i-10);
  const slope=I.ema20[slopeIndex] ?
    Math.abs((e20-I.ema20[slopeIndex])/I.ema20[slopeIndex]*100) : 999;

  const bbw=I.bbw[i];
  const sideways=adx!==null && adx<17 && slope<0.15 &&
    bbw!==null && bbw<3;

  const tooLate=(c.c>e20*1.025) || (rsi!==null && rsi>=72);

  let score=0;
  if(htf) score+=25;
  if(trend) score+=20;
  if(macdBull) score+=15;
  if(rsi!==null && rsi>=48 && rsi<=68) score+=15;
  if(adx!==null && adx>=20) score+=10;
  if(trigger) score+=15;

  const bearish=c.c<e50 && e20<e50;
  let sellScore=0;
  if(bearish) sellScore++;
  if(macdBear) sellScore++;
  if(rsi!==null && rsi<45) sellScore++;
  if(adx!==null && adx>=20) sellScore++;

  let signal="BEKLE";
  let reason="Alım koşulları henüz tamamlanmadı.";

  if(bearish && sellScore>=4) {
    signal="GÜÇLÜ SAT";
    reason="Düşüş trendi, MACD ve RSI satış yönünde.";
  } else if(bearish && sellScore>=3) {
    signal="SAT";
    reason="1 saatlik görünüm düşüş yönünde.";
  } else if(!htf) {
    signal="UYGUN DEĞİL";
    reason="4 saatlik fiyat EMA200 üzerinde değil.";
  } else if(sideways) {
    signal="UYGUN DEĞİL";
    reason="Yatay piyasa koşulları tespit edildi.";
  } else if(tooLate) {
    signal="UYGUN DEĞİL";
    reason="Fiyat EMA20'den fazla uzaklaşmış veya RSI çok yüksek.";
  } else if(trigger && score>=80) {
    signal="GÜÇLÜ AL";
    reason=breakout?"20 mumluk tepe kırılımı ve puan koşulları tamam.":"EMA20 geri çekilmesi ve puan koşulları tamam.";
  } else if(trigger && score>=65) {
    signal="AL";
    reason=breakout?"20 mumluk tepe kırılımı ve puan koşulları tamam.":"EMA20 geri çekilmesi ve puan koşulları tamam.";
  }

  const price=c.c;
  const stop=Number.isFinite(atr)?price-1.5*atr:null;
  const target=Number.isFinite(atr)?price+2*atr:null;

  return {
    symbol, candles, h4, I, H, signal, score, reason, price,
    h4Close,h4EMA,htf,rsi,adx,atr,stop,target,
    macdBull,macdBear,trend,trigger,breakout,pullback,
    sideways,tooLate,priorHigh,bbw,sellScore,
    volume24:Number(ticker.quoteVolume||0),
    change24:Number(ticker.priceChangePercent||0),
    lastTime:c.ct,
    lastOpen:c.o,lastHigh:c.h,lastLow:c.l,lastClose:c.c
  };
}

async function getCandles(symbol, interval, token) {
  const url=API+"/api/v3/klines?symbol="+encodeURIComponent(symbol)+
    "&interval="+interval+"&limit="+KLINE_LIMIT;
  const raw=await getJSON(url,token);
  return getClosedCandles(raw);
}

async function worker(queue, token, progress) {
  while(queue.length && scanning && token===scanToken) {
    const symbol=queue.shift();
    try {
      const h4=await getCandles(symbol,"4h",token);
      if(token!==scanToken || !scanning) return;

      // Filtre olumsuzsa 1 saatlik veriyi istemeden tarama yükünü azalt.
      const h4I=makeIndicators(h4);
      const last=h4.length-1;
      if(h4.length<205 || h4I.ema200[last]===null ||
         h4[last].c<=h4I.ema200[last]) {
        const basic={
          symbol,signal:"UYGUN DEĞİL",score:0,
          reason:"4 saatlik EMA200 filtresi olumsuz.",
          price:0,volume24:0,change24:0,
          htf:false, candles:[], h4, I:null,H:h4I
        };
        const ticker=tickerCache.get(symbol);
        if(ticker) {
          basic.price=Number(ticker.lastPrice||0);
          basic.volume24=Number(ticker.quoteVolume||0);
          basic.change24=Number(ticker.priceChangePercent||0);
        }
        marketData.push(basic);
        progress();
        continue;
      }

      const candles=await getCandles(symbol,"1h",token);
      if(token!==scanToken || !scanning) return;
      const ticker=tickerCache.get(symbol)||{};
      const result=calcSignal(candles,h4,ticker,symbol);
      if(result) {
        marketData.push(result);
        backtestCache.set(symbol,result);
      }
    } catch(err) {
      if(token===scanToken && scanning && err.message!=="Tarama durduruldu") {
        console.warn(symbol,err.message);
      }
    }
    progress();
  }
}

let tickerCache=new Map();

async function startScan() {
  if(scanning) return;
  scanning=true;
  const token=++scanToken;
  marketData=[];
  backtestCache.clear();
  selectedData=null;
  $("scanBtn").disabled=true;
  $("results").innerHTML="<tr><td colspan='13'>Piyasa bilgileri alınıyor...</td></tr>";
  $("details").textContent="Tarama devam ediyor.";
  setProgress(0);
  setStatus("Binance Spot pariteleri yükleniyor...");

  try {
    const [exchange,tickers]=await Promise.all([
      getJSON(API+"/api/v3/exchangeInfo",token),
      getJSON(API+"/api/v3/ticker/24hr",token)
    ]);
    if(token!==scanToken) return;

    tickerCache=new Map(tickers.map(t=>[t.symbol,t]));
    const symbols=exchange.symbols
      .filter(s=>s.status==="TRADING" &&
        s.quoteAsset==="USDT" &&
        s.isSpotTradingAllowed &&
        !s.symbol.endsWith("UPUSDT") &&
        !s.symbol.endsWith("DOWNUSDT") &&
        !s.symbol.endsWith("BULLUSDT") &&
        !s.symbol.endsWith("BEARUSDT"))
      .map(s=>s.symbol)
      .filter(s=>tickerCache.has(s));

    $("total").textContent=symbols.length;
    let done=0;
    const total=symbols.length;
    const progress=()=>{
      done++;
      setProgress(done/total*100);
      setStatus("Tarama sürüyor: "+done+" / "+total+
        " parite. Her coin için kapanmış 4s ve gerekirse 1s mumları kontrol ediliyor.");
      updateCounts();
      renderTable();
    };

    // Kuyruk bütün uygun USDT Spot paritelerini kapsar; sabit 40 coin sınırı yok.
    const queue=symbols.slice();
    await Promise.all(Array.from({length:WORKERS},()=>worker(queue,token,progress)));

    if(token!==scanToken) return;
    scanning=false;
    $("scanBtn").disabled=false;
    updateCounts();
    renderTable();
    populateBacktestCoins();
    setStatus("Tarama tamamlandı. "+marketData.length+
      " parite işlendi. Sinyaller yalnızca kapanmış mumlardan hesaplandı.");
    setProgress(100);
  } catch(err) {
    if(token===scanToken) {
      scanning=false;
      $("scanBtn").disabled=false;
      setStatus("Hata: "+err.message+
        " Tarama sınır nedeniyle kesildiyse biraz bekleyip tekrar dene.");
    }
  }
}

function stopScan() {
  if(!scanning) return;
  scanning=false;
  scanToken++;
  $("scanBtn").disabled=false;
  setStatus("Tarama kullanıcı tarafından durduruldu. Görünen sonuçlar korunuyor.");
}

function updateCounts() {
  $("strongBuy").textContent=marketData.filter(x=>x.signal==="GÜÇLÜ AL").length;
  $("buyCount").textContent=marketData.filter(x=>x.signal==="AL").length;
  $("sellCount").textContent=marketData.filter(x=>
    x.signal==="SAT"||x.signal==="GÜÇLÜ SAT").length;
  $("unsuitable").textContent=marketData.filter(x=>x.signal==="UYGUN DEĞİL").length;
}

function signalClass(signal) {
  if(signal==="AL"||signal==="GÜÇLÜ AL") return "buy";
  if(signal==="SAT"||signal==="GÜÇLÜ SAT") return "sell";
  if(signal==="UYGUN DEĞİL") return "warn";
  return "wait";
}

function matchesFilter(x,filter) {
  if(filter==="buy") return x.signal==="AL"||x.signal==="GÜÇLÜ AL";
  if(filter==="strong") return x.signal==="GÜÇLÜ AL";
  if(filter==="sell") return x.signal==="SAT"||x.signal==="GÜÇLÜ SAT";
  if(filter==="eligible") return x.signal==="AL"||x.signal==="GÜÇLÜ AL";
  return true;
}

function renderTable() {
  const query=$("search").value.trim().toUpperCase();
  const filter=$("filter").value;
  const sort=$("sort").value;
  let rows=marketData.filter(x=>
    x.symbol.includes(query) && matchesFilter(x,filter)
  );

  rows.sort((a,b)=>{
    if(sort==="volume") return (b.volume24||0)-(a.volume24||0);
    if(sort==="change") return (b.change24||0)-(a.change24||0);
    if(sort==="symbol") return a.symbol.localeCompare(b.symbol);
    const priority={"GÜÇLÜ AL":5,"AL":4,"SAT":3,"GÜÇLÜ SAT":3,"BEKLE":2,"UYGUN DEĞİL":1};
    return (priority[b.signal]||0)-(priority[a.signal]||0) ||
      (b.score||0)-(a.score||0);
  });

  if(!rows.length) {
    $("results").innerHTML="<tr><td colspan='13'>Bu filtreye uygun sonuç yok.</td></tr>";
    return;
  }

  $("results").innerHTML=rows.map(x=>{
    const signal=esc(x.signal||"BEKLE");
    const cls=signalClass(x.signal);
    return `<tr>
      <td><button class="secondary" style="padding:5px 7px" onclick="showDetails('${esc(x.symbol)}')">${esc(x.symbol)}</button></td>
      <td><span class="pill ${cls}">${signal}</span></td>
      <td>${Number.isFinite(x.score)?x.score:"—"}</td>
      <td>${num(x.price,8)}</td>
      <td class="${x.change24>=0?"good":"bad"}">${pct(x.change24)}</td>
      <td>${money(x.volume24)}</td>
      <td>${num(x.rsi,1)}</td>
      <td>${num(x.adx,1)}</td>
      <td>${num(x.atr,8)}</td>
      <td>${num(x.stop,8)}</td>
      <td>${num(x.target,8)}</td>
      <td class="${x.htf?"good":"bad"}">${x.htf?"ÜZERİNDE":"ALTINDA"}</td>
      <td><button class="secondary" style="padding:5px 7px" onclick="showDetails('${esc(x.symbol)}')">İncele</button></td>
    </tr>`;
  }).join("");
}

function showDetails(symbol) {
  const x=marketData.find(a=>a.symbol===symbol);
  if(!x) return;
  selectedData=x;
  const triggerText=x.breakout?"20 mum tepe kırılımı":
    x.pullback?"EMA20 geri çekilmesi":"Yok";
  $("details").innerHTML=`
    <b>${esc(x.symbol)}</b> — <span class="pill ${signalClass(x.signal)}">${esc(x.signal)}</span><br>
    <b>Sinyal zamanı:</b> ${timeText(x.lastTime)}<br>
    <b>Kapanış fiyatı:</b> ${num(x.price,8)}<br>
    <b>Toplam puan:</b> ${x.score??"—"} / 100<br>
    <b>Karar açıklaması:</b> ${esc(x.reason||"—")}<br>
    <b>4 saatlik EMA200:</b> ${x.htf?"Filtre olumlu":"Filtre olumsuz"}<br>
    <b>1 saatlik trend:</b> ${x.trend?"Olumlu":"Olumlu değil"}<br>
    <b>MACD:</b> ${x.macdBull?"Yükseliş yönünde":x.macdBear?"Düşüş yönünde":"Net yükseliş onayı yok"}<br>
    <b>RSI(14):</b> ${num(x.rsi,2)}<br>
    <b>ADX(14):</b> ${num(x.adx,2)} — trend gücü bilgisi<br>
    <b>Giriş tetikleyicisi:</b> ${triggerText}<br>
    <b>Yatay piyasa:</b> ${x.sideways?"Tespit edildi":"Tespit edilmedi"}<br>
    <b>Geç giriş kontrolü:</b> ${x.tooLate?"Engel var":"Engel yok"}<br>
    <b>ATR(14):</b> ${num(x.atr,8)}<br>
    <b>Örnek stop (1,5 ATR):</b> ${num(x.stop,8)}<br>
    <b>Örnek hedef (2 ATR):</b> ${num(x.target,8)}<br>
    <b>24 saatlik hacim:</b> ${money(x.volume24)} USDT — yalnızca bilgi, filtre değil.<br>
    <b>24 saatlik fiyat değişimi:</b> ${pct(x.change24)}<br>
    <span class="muted">Stop ve hedef örnek seviyelerdir; gerçek emir değildir. İşlem fiyatı, kayma ve komisyon sonuçları değiştirebilir.</span>
  `;
}

function populateBacktestCoins() {
  const select=$("backtestCoin");
  const old=select.value;
  const coins=Array.from(backtestCache.keys()).sort();
  select.innerHTML=coins.length
    ? coins.map(s=>`<option value="${esc(s)}">${esc(s)}</option>`).join("")
    : "<option value=''>Test edilecek veri yok</option>";
  if(coins.includes(old)) select.value=old;
}

function runBacktest() {
  const symbol=$("backtestCoin").value;
  const x=backtestCache.get(symbol);
  if(!x || !x.candles || !x.I) {
    $("backtestSummary").textContent="Bu coin için yeterli mum verisi yok. Önce taramayı tamamla.";
    return;
  }

  const candles=x.candles;
  const I=x.I;
  const trades=[];
  let inTrade=null;
  let lastEntry=-10;

  for(let i=200;i<candles.length-1;i++) {
    const c=candles[i];
    const e20=I.ema20[i],e50=I.ema50[i],rsi=I.rsi[i],adx=I.adx[i];
    const atr=I.atr[i];
    const htfOK=x.h4[x.h4.length-1].c>x.H.ema200[x.h4.length-1];

    if(!Number.isFinite(e20)||!Number.isFinite(e50)||
       !Number.isFinite(atr)||atr<=0) continue;

    // Tarihsel 4s filtreyi her mum için doğru eşleştir.
    const h4Index=x.h4.findIndex(h=>h.ct>=c.ct);
    const matchedIndex=h4Index===-1?x.h4.length-1:
      (x.h4[h4Index].ct>c.ct?Math.max(0,h4Index-1):h4Index);
    const h4Close=x.h4[matchedIndex]?.c;
    const h4EMA=x.H.ema200[matchedIndex];
    const historicalHtf=Number.isFinite(h4EMA)&&h4Close>h4EMA;
    if(!historicalHtf) continue;

    const macdBull=I.macd.line[i]!==null&&I.macd.signal[i]!==null&&
      I.macd.hist[i]!==null&&I.macd.line[i]>I.macd.signal[i]&&I.macd.hist[i]>0;
    const trend=c.c>e50&&e20>e50;
    const prev20=candles.slice(Math.max(0,i-20),i);
    const priorHigh=Math.max(...prev20.map(v=>v.h));
    const breakout=c.c>priorHigh;
    const pullback=Math.abs(c.c-e20)/e20<=0.012&&c.l<=e20*1.012&&c.c>e20&&c.c>c.o;
    const trigger=breakout||pullback;
    const bbw=I.bbw[i];
    const slopeIndex=Math.max(0,i-10);
    const slope=I.ema20[slopeIndex]?
      Math.abs((e20-I.ema20[slopeIndex])/I.ema20[slopeIndex]*100):999;
    const sideways=adx!==null&&adx<17&&slope<0.15&&bbw!==null&&bbw<3;
    const late=c.c>e20*1.025||(rsi!==null&&rsi>=72);

    let score=25;
    if(trend) score+=20;
    if(macdBull) score+=15;
    if(rsi!==null&&rsi>=48&&rsi<=68) score+=15;
    if(adx!==null&&adx>=20) score+=10;
    if(trigger) score+=15;

    if(!inTrade) {
      if(trigger&&score>=65&&!sideways&&!late&&trend&&i-lastEntry>0) {
        inTrade={
          entry:c.c,
          entryTime:c.ct,
          stop:c.c-1.5*atr,
          target:c.c+2*atr,
          index:i
        };
        lastEntry=i;
      }
    } else {
      let exitPrice=null,exitTime=c.ct,reason="";
      // Mumun en düşük ve en yüksek seviyeleri kontrol edilir.
      const stopHit=c.l<=inTrade.stop;
      const targetHit=c.h>=inTrade.target;
      if(stopHit) {
        exitPrice=inTrade.stop;
        reason="STOP";
      } else if(targetHit) {
        exitPrice=inTrade.target;
        reason="HEDEF";
      } else {
        const bearish=c.c<e50&&e20<e50;
        if(bearish) {
          exitPrice=c.c;
          reason="SAT sinyali";
        }
      }

      if(exitPrice!==null) {
        const gross=(exitPrice/inTrade.entry-1)*100;
        const net=((exitPrice/inTrade.entry)*(1-FEE)*(1-FEE)-1)*100;
        trades.push({
          entryTime:inTrade.entryTime,exitTime,
          entry:inTrade.entry,exit:exitPrice,
          gross,net,reason
        });
        inTrade=null;
      }
    }
  }

  const wins=trades.filter(t=>t.net>0).length;
  const losses=trades.filter(t=>t.net<=0).length;
  const totalNet=trades.reduce((s,t)=>s+t.net,0);
  const avg=trades.length?totalNet/trades.length:0;
  $("backtestSummary").innerHTML=`
    <b>${esc(symbol)}:</b> ${trades.length} kapanmış işlem ·
    <span class="good">Kârlı: ${wins}</span> ·
    <span class="bad">Zararlı: ${losses}</span> ·
    Net işlem yüzdelerinin toplamı: <b class="${totalNet>=0?"good":"bad"}">${pct(totalNet)}</b> ·
    İşlem başına ortalama: <b>${pct(avg)}</b><br>
    <span class="muted">Bu sonuç bileşik portföy getirisi değildir. Sinyal mum kapanışında hesaplanır; gerçek giriş fiyatı farklı olabilir. Stop/hedef aynı mumda görülürse stop önceliklidir.</span>
  `;

  $("backtestRows").innerHTML=trades.length?trades.slice().reverse().map(t=>`
    <tr>
      <td>${timeText(t.entryTime)}</td>
      <td>${timeText(t.exitTime)}</td>
      <td>${num(t.entry,8)}</td>
      <td>${num(t.exit,8)}</td>
      <td>${esc(t.reason)}</td>
      <td class="${t.net>=0?"good":"bad"}">${pct(t.net)}</td>
    </tr>
  `).join(""):"<tr><td colspan='6'>Bu veri aralığında kapanmış işlem bulunamadı.</td></tr>";
}

function exportCSV() {
  if(!marketData.length) {
    setStatus("Önce tarama yapmalısın.");
    return;
  }
  const headers=["Coin","Sinyal","Puan","Fiyat","24s Değişim %","24s Hacim USDT","RSI","ADX","ATR","Stop","Hedef","4s EMA200","Açıklama"];
  const rows=marketData.map(x=>[
    x.symbol,x.signal,x.score,x.price,x.change24,x.volume24,x.rsi,x.adx,
    x.atr,x.stop,x.target,x.htf?"ÜZERİNDE":"ALTINDA",x.reason
  ]);
  const csv=[headers,...rows].map(row=>row.map(v=>{
    const s=String(v??"");
    return '"'+s.replace(/"/g,'""')+'"';
  }).join(",")).join("\r\n");
  const blob=new Blob(["\uFEFF"+csv],{type:"text/csv;charset=utf-8;"});
  const url=URL.createObjectURL(blob);
  const a=document.createElement("a");
  a.href=url;
  a.download="kripto-spot-tarama.csv";
  a.click();
  URL.revokeObjectURL(url);
}

// Sayfa açılınca otomatik ve kontrolsüz istek atılmaz.
</script>
</body>
</html>
