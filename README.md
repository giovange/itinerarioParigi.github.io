<!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Parigi · 6-9 Dicembre</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.css">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,400;9..144,500;9..144,600;9..144,700&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#0E1526; --panel:#141C31; --panel-2:#1B2439; --line:#2A3450;
    --ink:#F3EFE4; --ink-dim:#9AA6C0; --gold:#C9A227;
    --free:#7FB88A; --paid:#D08A4F; --warn:#E0B04F;
  }
  *{box-sizing:border-box;}
  html,body{height:100%;}
  body{margin:0;background:var(--bg);color:var(--ink);font-family:'Inter',sans-serif;overflow:hidden;}
  .app{display:grid;grid-template-rows:auto auto 1fr;height:100vh;}

  header{padding:22px 28px 14px;border-bottom:1px solid var(--line);display:flex;align-items:baseline;justify-content:space-between;flex-wrap:wrap;gap:8px;}
  header .title{font-family:'Fraunces',serif;font-weight:600;font-size:26px;letter-spacing:.2px;}
  header .title span{color:var(--gold);}
  header .sub{color:var(--ink-dim);font-size:13.5px;}

  nav.tabs{display:flex;gap:10px;padding:14px 28px;border-bottom:1px solid var(--line);overflow-x:auto;}
  .tab{position:relative;flex:0 0 auto;display:flex;align-items:center;gap:10px;padding:9px 16px 9px 14px;background:var(--panel);border:1px solid var(--line);border-radius:3px;cursor:pointer;transition:border-color .15s ease,transform .15s ease;-webkit-tap-highlight-color:transparent;}
  .tab:hover{transform:translateY(-1px);}
  .tab::after{content:"";position:absolute;right:-6px;top:50%;width:11px;height:11px;background:var(--bg);border-radius:50%;transform:translateY(-50%);border-left:1px solid var(--line);}
  .tab:last-child::after{display:none;}
  .tab .num{font-family:'Fraunces',serif;font-size:19px;font-weight:600;color:var(--dot);}
  .tab .lbl{display:flex;flex-direction:column;line-height:1.25;}
  .tab .lbl b{font-size:12.5px;font-weight:600;}
  .tab .lbl small{font-size:11px;color:var(--ink-dim);}
  .tab .dot{width:8px;height:8px;border-radius:50%;flex:0 0 auto;}
  .tab.active{border-color:var(--accent);background:var(--panel-2);}

  .main{display:grid;grid-template-columns:400px 1fr;min-height:0;position:relative;}
  .sidebar{border-right:1px solid var(--line);overflow-y:auto;padding:18px 0 60px;}
  .day-heading{padding:2px 22px 12px;}
  .day-heading h2{font-family:'Fraunces',serif;font-weight:600;font-size:20px;margin:0 0 4px;}
  .day-heading p{margin:0;color:var(--ink-dim);font-size:13px;}
  .day-note{margin:12px 22px 4px;padding:10px 12px;font-size:12.3px;line-height:1.5;color:var(--ink-dim);background:var(--panel);border:1px solid var(--line);border-left:3px solid var(--warn);border-radius:4px;}
  .day-note b{color:var(--ink);}

  .timeline{position:relative;padding:10px 22px 0 84px;}
  .timeline::before{content:"";position:absolute;left:54px;top:10px;bottom:20px;width:1px;background:var(--line);}
  .stop{position:relative;padding-bottom:22px;cursor:default;}
  .stop.clickable{cursor:pointer;}
  .stop .time{position:absolute;left:-84px;top:1px;width:42px;text-align:right;font-variant-numeric:tabular-nums;font-size:12px;color:var(--gold);font-weight:600;}
  .stop .node{position:absolute;left:-30px;top:3px;width:9px;height:9px;border-radius:50%;border:2px solid var(--bg);box-shadow:0 0 0 1px var(--accent);}
  .stop.meal .node{box-shadow:0 0 0 1px var(--line);}
  .stop h3{margin:0 0 3px;font-size:14.5px;font-weight:600;}
  .stop.clickable h3:hover{color:var(--gold);}
  .stop p{margin:0;font-size:12.8px;line-height:1.5;color:var(--ink-dim);}
  a.booklink{color:var(--gold);font-weight:600;text-decoration:none;white-space:nowrap;border-bottom:1px solid rgba(201,162,39,.4);}
  a.booklink:hover{border-bottom-color:var(--gold);}
  .badges{display:flex;flex-wrap:wrap;gap:6px;margin-top:7px;}
  .badge{display:inline-flex;align-items:center;gap:5px;font-size:11.3px;line-height:1.3;padding:3px 9px 3px 7px;border-radius:20px;background:var(--panel-2);border:1px solid var(--line);}
  .badge .pdot{width:6px;height:6px;border-radius:50%;flex:0 0 auto;}
  .badge.free .pdot{background:var(--free);}
  .badge.paid .pdot{background:var(--paid);}
  .badge.open .pdot{background:var(--free);}
  .badge.warn .pdot{background:var(--warn);}
  .badge.book .pdot{background:#6FA8DC;}

  #map{width:100%;height:100%;background:var(--panel);}
  .leaflet-popup-content-wrapper{background:var(--panel-2);color:var(--ink);border-radius:6px;}
  .leaflet-popup-tip{background:var(--panel-2);}
  .leaflet-popup-content{margin:12px 14px;font-family:'Inter',sans-serif;}
  .popup-title{font-family:'Fraunces',serif;font-weight:600;font-size:15px;margin-bottom:3px;}
  .popup-time{color:var(--gold);font-size:12px;font-weight:600;margin-bottom:6px;}
  .popup-desc{font-size:12.5px;color:var(--ink-dim);line-height:1.5;}

  .legend{position:absolute;left:418px;bottom:18px;z-index:400;background:rgba(20,28,49,.92);border:1px solid var(--line);border-radius:8px;padding:10px 14px;font-size:11.5px;color:var(--ink-dim);display:flex;flex-wrap:wrap;gap:6px 14px;backdrop-filter:blur(3px);max-width:calc(100% - 440px);}
  .legend span{display:flex;align-items:center;gap:6px;}
  .legend .pdot{width:7px;height:7px;border-radius:50%;}
  .foot{padding:6px 22px 0;font-size:11.2px;line-height:1.5;color:var(--ink-dim);}

  ::-webkit-scrollbar{width:8px;}
  ::-webkit-scrollbar-thumb{background:var(--line);border-radius:4px;}

  @media (max-width:820px){
    html,body{height:auto;overflow:visible;}
    .app{height:auto;display:block;}
    header{padding:18px 18px 12px;}
    header .title{font-size:21px;}
    nav.tabs{padding:12px 18px;}
    .main{display:block;}
    #map{height:42vh;min-height:260px;width:100%;}
    .sidebar{border-right:none;border-top:1px solid var(--line);overflow-y:visible;height:auto;}
    .legend{position:static;max-width:none;border-radius:0;border-left:none;border-right:none;}
  }
</style>
</head>
<body>
<div class="app">
  <header>
    <div>
      <div class="title">Parigi <span>·</span> 6&ndash;9 Dicembre 2026</div>
      <div class="sub">Itinerario di viaggio &mdash; base: 7 Rue des Innocents, 75001</div>
    </div>
    <div class="sub" id="dayCount"></div>
  </header>
  <nav class="tabs" id="tabs"></nav>
  <div class="main">
    <aside class="sidebar" id="sidebar"></aside>
    <div id="map"></div>
    <div class="legend">
      <span><span class="pdot" style="background:#7FB88A"></span>Gratis under 26 UE / aperto</span>
      <span><span class="pdot" style="background:#D08A4F"></span>Sempre a pagamento</span>
      <span><span class="pdot" style="background:#6FA8DC"></span>Prenotazione consigliata/obbligatoria</span>
      <span><span class="pdot" style="background:#E0B04F"></span>Attenzione</span>
    </div>
  </div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.js"></script>
<script>
const HOME = { name:"Appartamento", lat:48.8603534, lng:2.3477552, desc:"7 Rue des Innocents &mdash; base del gruppo per tutto il soggiorno." };

/* Campi di ogni tappa:
   open = orari di apertura nel giorno della visita (verificati nov 2026)
   book = come/dove prenotare
   warn = avvertenza
   price: 'free' | 'paid' */
const DAYS = [
  {
    id:1, color:"#C08497", label:"Sainte-Chapelle, Notre-Dame, Marais", date:"Dom 6 dic",
    note:"<b>Prima domenica del mese:</b> ingresso gratuito per tutti in molti monumenti, quindi più folla del solito. Ho invertito l'ordine rispetto alla prima versione: <b>Sainte-Chapelle prima, Notre-Dame nel primo pomeriggio</b>, perché la domenica mattina in cattedrale ci sono le messe delle 10:00 e delle 11:30 e la visita è più limitata in quegli orari.",
    stops:[
      {time:"08:30", name:"Arrivo aeroporto ORY", desc:"Trasferimento verso l'appartamento (Orlybus + RER B, o taxi ~30-35 min).", meal:true},
      {time:"09:30", name:"Check-in appartamento", desc:"Deposito bagagli, sistemazione veloce.", meal:true},
      {time:"10:30", name:"Sainte-Chapelle", lat:48.855375, lng:2.3449609, desc:"Vetrate gotiche tra le più belle d'Europa. Controllo di sicurezza all'ingresso: arrivate 15-20 min prima dello slot.", duration:"~45-60 min", price:"free", priceLabel:"Gratis under 26 UE · intero 13€", open:"Dom 9:00–17:00 (ultimo ingresso 16:30)", book:"Slot gratuito su monuments-nationaux.fr"},
      {time:"11:45", name:"Pranzo", desc:"Île Saint-Louis o Quartiere Latino.", meal:true},
      {time:"13:30", name:"Notre-Dame", lat:48.8529682, lng:2.3499021, desc:"Esterno e interno della cattedrale. Il percorso di visita è a senso unico.", duration:"~45 min", price:"free", priceLabel:"Ingresso gratuito", open:"Dom 8:15–19:30", book:"Prenotazione gratuita consigliata (non obbligatoria) su notredamedeparis.fr", warn:"Messe alle 10:00, 11:30, 17:15 e 18:00: evitate quegli orari"},
      {time:"14:45", name:"Le Marais / Place des Vosges", lat:48.8556114, lng:2.3655266, desc:"Passeggiata nel quartiere storico: boutique vintage, street art, Place des Vosges. La domenica i negozi del Marais sono aperti.", duration:"~2h"},
      {time:"16:45", name:"Pausa merenda/caffè", desc:"Scelta libera nel Marais.", meal:true},
      {time:"19:30", name:"Cena", desc:"Zona Marais o Les Halles.", meal:true},
      {time:"21:00", name:"Bar / locali", desc:"Scelta libera per la serata.", meal:true}
    ]
  },
  {
    id:2, color:"#C9A227", label:"Louvre, Concorde, Champs-Élysées", date:"Lun 7 dic",
    note:"<b>Lunedì:</b> il Louvre è aperto (chiude il martedì) ma l'Orsay è chiuso, quindi il Louvre è più affollato: prenotate lo slot delle 10:30 il prima possibile.",
    stops:[
      {time:"09:30", name:"Colazione", desc:"Scelta libera.", meal:true},
      {time:"10:30", name:"Museo del Louvre", lat:48.8606111, lng:2.337644, desc:"Prenotazione di una fascia oraria obbligatoria per tutti, anche per l'ingresso gratuito.", duration:"3h", price:"free", priceLabel:"Gratis under 26 UE · intero 22€", open:"Lun 9:00–18:00 (ultimo ingresso 17:00)", book:"Obbligatoria su louvre.fr"},
      {time:"13:30", name:"Pranzo", desc:"Zona Palais-Royal / Rue de Rivoli.", meal:true},
      {time:"14:30", name:"Jardin des Tuileries", lat:48.8634916, lng:2.3274943, desc:"Passeggiata nel giardino tra Louvre e Concorde.", duration:"~30 min", open:"Ogni giorno 7:30–19:30 (inverno)"},
      {time:"15:00", name:"Musée de l'Orangerie", lat:48.8637884, lng:2.3226724, desc:"Le Ninfee di Monet nelle sale ovali.", duration:"1h", price:"free", priceLabel:"Gratis under 26 UE · intero 12,50€", open:"Lun 9:00–18:00 (ultimo ingresso 17:15)", book:"Prenotazione online obbligatoria su musee-orangerie.fr"},
      {time:"16:00", name:"Place de la Concorde", lat:48.8656331, lng:2.3212357, desc:"Obelisco di Luxor, foto panoramiche.", duration:"~30 min"},
      {time:"17:15", name:"Arc de Triomphe", lat:48.8737917, lng:2.2950275, desc:"Salita alla terrazza panoramica (circa 280 gradini a piedi, nessun ascensore per i visitatori). A quest'ora è già buio: vedrete la città illuminata.", duration:"~45 min", price:"free", priceLabel:"Gratis under 26 UE · intero 16€", open:"Lun 10:00–22:30 (ultimo ingresso 21:45)", book:"Prenotazione consigliata su paris-arc-de-triomphe.fr / monuments-nationaux.fr"},
      {time:"19:30", name:"Cena", desc:"Zona Champs-Élysées.", meal:true},
      {time:"21:00", name:"Bar / locali", desc:"Scelta libera per la serata.", meal:true}
    ]
  },
  {
    id:3, color:"#4C93A6", label:"Montmartre, Orsay, Tour Eiffel", date:"Mar 8 dic",
    note:"<b>Martedì:</b> Louvre e Orangerie sono chiusi, ma voi non ci andate. L'Orsay è aperto (chiude il lunedì). L'Eiffel è aperta ogni giorno.",
    stops:[
      {time:"09:30", name:"Colazione", desc:"Scelta libera.", meal:true},
      {time:"10:45", name:"Sacré-Cœur", lat:48.8867046, lng:2.3431043, desc:"Basilica (ingresso gratuito) e vista panoramica dal sagrato. La cupola è a pagamento (~8€) e ha circa 280 gradini.", duration:"~35 min", price:"free", priceLabel:"Basilica gratuita · cupola ~8€", open:"Basilica 6:30–22:30 · cupola in inverno circa 10:00–17:00", book:"Nessuna prenotazione: biglietto cupola in loco"},
      {time:"12:00", name:"Le Mur des Je t'aime", lat:48.884856, lng:2.3385644, desc:"Square Jehan-Rictus: il muro con \"ti amo\" scritto in decine di lingue.", duration:"~20 min", price:"free", priceLabel:"Gratuito"},
      {time:"12:30", name:"Moulin Rouge", lat:48.8841232, lng:2.3322519, desc:"Foto dell'esterno, zona Pigalle.", duration:"~15 min"},
      {time:"13:00", name:"Pranzo", desc:"Zona Pigalle / Montmartre.", meal:true},
      {time:"14:30", name:"Musée d'Orsay", lat:48.8599614, lng:2.3265614, desc:"Capolavori impressionisti in un'ex stazione ferroviaria.", duration:"2h", price:"free", priceLabel:"Gratis under 26 UE · intero 16€", open:"Mar 9:30–18:00 (ultimo ingresso 17:00)", book:"Prenotazione online obbligatoria su musee-orsay.fr"},
      {time:"17:30", name:"Tour Eiffel", lat:48.8583701, lng:2.2944813, desc:"Salita di sera, illuminata. Lo scintillio parte allo scattare di ogni ora.", duration:"~1h30", price:"paid", priceLabel:"Adulti 14,80–36,70€ · tariffa giovani 12-24 circa metà (verifica al pagamento)", open:"Ogni giorno · ascensori fino alle 23:00", book:"Obbligatoria su toureiffel.paris (slot aperti 60 giorni prima: già disponibili)"},
      {time:"19:00", name:"Trocadéro", lat:48.8627982, lng:2.2876042, desc:"La miglior vista della torre illuminata dall'altra sponda della Senna.", duration:"~30 min"},
      {time:"19:30", name:"Cena", desc:"Zona Trocadéro / Eiffel.", meal:true},
      {time:"21:00", name:"Bar / locali", desc:"Scelta libera per la serata.", meal:true}
    ]
  },
  {
    id:4, color:"#8C6FA6", label:"Panthéon, Madeleine, Lafayette + partenza", date:"Mer 9 dic",
    note:"<b>Mercoledì:</b> tutte le tappe sono aperte. La terrazza delle Galeries Lafayette può chiudere con pioggia o vento forte.",
    stops:[
      {time:"09:30", name:"Colazione", desc:"Scelta libera.", meal:true},
      {time:"10:45", name:"Panthéon", lat:48.8462218, lng:2.3464138, desc:"Cripta con Voltaire, Rousseau, Hugo, Marie Curie.", duration:"1h", price:"free", priceLabel:"Gratis under 26 UE · intero 13€", open:"Mer 10:00–18:00 (ultimo ingresso 17:15)", book:"Prenotazione consigliata su paris-pantheon.fr / monuments-nationaux.fr"},
      {time:"11:45", name:"Quartiere Latino", desc:"Sorbona, Rue Mouffetard, libreria Shakespeare & Company.", meal:true},
      {time:"13:00", name:"Pranzo", desc:"Quartiere Latino.", meal:true},
      {time:"14:30", name:"Place de la Madeleine", lat:48.8699468, lng:2.3248903, desc:"Chiesa neoclassica e boutique gourmet.", duration:"~45 min", price:"free", priceLabel:"Gratuito"},
      {time:"15:15", name:"Galeries Lafayette", lat:48.8736224, lng:2.3321099, desc:"La celebre cupola Art Nouveau e la terrazza panoramica sul tetto, senza prenotazione.", duration:"1h", price:"free", priceLabel:"Ingresso e terrazza gratuiti", open:"Mer circa 10:00–20:00 · terrazza fino alle 19:30", warn:"Terrazza chiusa con pioggia o vento forte"},
      {time:"16:45", name:"Palais-Royal", lat:48.8637569, lng:2.3371261, desc:"Cortile con le colonne di Buren, giardino.", duration:"~30 min", price:"free", priceLabel:"Gratuito"},
      {time:"18:00", name:"Rientro in appartamento", desc:"Recupero bagagli.", meal:true},
      {time:"19:00", name:"Aeroporto Paris-Orly", lat:48.7299182, lng:2.3732304, desc:"Arrivo 2h prima della partenza, come richiesto.", priceLabel:"Partenza ore 21:00"}
    ]
  }
];

/* Link ufficiali di prenotazione (siti ufficiali dei monumenti/musei) */
const LINKS = {
  "Sainte-Chapelle":      "https://www.sainte-chapelle.fr",
  "Notre-Dame":           "https://www.notredamedeparis.fr",
  "Museo del Louvre":     "https://ticket.louvre.fr/",
  "Musée de l'Orangerie": "https://billetterie.musee-orangerie.fr",
  "Arc de Triomphe":      "https://www.paris-arc-de-triomphe.fr",
  "Musée d'Orsay":        "https://billetterie.musee-orsay.fr",
  "Tour Eiffel":          "https://www.toureiffel.paris/fr",
  "Panthéon":             "https://www.paris-pantheon.fr"
};
function linkHtml(s){
  const url = LINKS[s.name];
  if(!url) return '';
  const host = url.replace(/^https?:\/\//,'').replace(/\/.*$/,'').replace(/^www\./,'');
  return ` <a class="booklink" href="${url}" target="_blank" rel="noopener">Prenota: ${host} &rarr;</a>`;
}

/* ---------- Mappa ---------- */
const map = L.map('map', { zoomControl:true }).setView([48.8608, 2.3376], 13);
L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png', {
  attribution:'&copy; OpenStreetMap &copy; CARTO', subdomains:'abcd', maxZoom:19
}).addTo(map);

let markerLayer = L.layerGroup().addTo(map);
let lineLayer = L.layerGroup().addTo(map);
let markerRefs = {};

function homeIcon(color){
  return L.divIcon({
    className:'',
    html:`<div style="width:14px;height:14px;border-radius:50%;background:${color};border:2px solid #0E1526;box-shadow:0 0 0 1px ${color}"></div>`,
    iconSize:[14,14], iconAnchor:[7,7]
  });
}
function numIcon(n, color){
  return L.divIcon({
    className:'',
    html:`<div style="width:24px;height:24px;border-radius:50% 50% 50% 0;transform:rotate(-45deg);background:${color};border:2px solid #0E1526;display:flex;align-items:center;justify-content:center;">
            <span style="transform:rotate(45deg);color:#0E1526;font-family:Inter,sans-serif;font-weight:700;font-size:11px;">${n}</span>
          </div>`,
    iconSize:[24,24], iconAnchor:[12,24], popupAnchor:[0,-24]
  });
}

function badgesHtml(s){
  let out = '';
  if(s.price === 'free') out += `<span class="badge free"><span class="pdot"></span>${s.priceLabel}</span>`;
  else if(s.price === 'paid') out += `<span class="badge paid"><span class="pdot"></span>${s.priceLabel}</span>`;
  else if(s.priceLabel) out += `<span class="badge"><span class="pdot" style="background:var(--ink-dim)"></span>${s.priceLabel}</span>`;
  if(s.open) out += `<span class="badge open"><span class="pdot"></span>${s.open}</span>`;
  if(s.book) out += `<span class="badge book"><span class="pdot"></span>${s.book}</span>`;
  if(s.warn) out += `<span class="badge warn"><span class="pdot"></span>${s.warn}</span>`;
  return out ? `<div class="badges">${out}</div>` : '';
}

function renderDay(day){
  markerLayer.clearLayers();
  lineLayer.clearLayers();
  markerRefs = {};

  L.marker([HOME.lat, HOME.lng], {icon: homeIcon('#F3EFE4')})
    .bindPopup(`<div class="popup-title">${HOME.name}</div><div class="popup-desc">${HOME.desc}</div>`)
    .addTo(markerLayer);

  const geoStops = day.stops.filter(s => s.lat);
  const latlngs = [[HOME.lat, HOME.lng]];

  geoStops.forEach((s, i) => {
    const marker = L.marker([s.lat, s.lng], {icon: numIcon(i+1, day.color)});
    let extra = '';
    if(s.priceLabel) extra += `<div class="popup-desc" style="margin-top:6px;">${s.priceLabel}</div>`;
    if(s.open) extra += `<div class="popup-desc" style="margin-top:4px;color:#7FB88A;">${s.open}</div>`;
    if(s.book) extra += `<div class="popup-desc" style="margin-top:4px;color:#6FA8DC;">${s.book}</div>`;
    if(s.warn) extra += `<div class="popup-desc" style="margin-top:4px;color:#E0B04F;">${s.warn}</div>`;
    marker.bindPopup(
      `<div class="popup-time">${s.time}${s.duration ? ' · ' + s.duration : ''}</div>
       <div class="popup-title">${s.name}</div>
       <div class="popup-desc">${s.desc}${linkHtml(s)}</div>${extra}`
    );
    marker.addTo(markerLayer);
    markerRefs[s.name] = marker;
    latlngs.push([s.lat, s.lng]);
  });

  L.polyline(latlngs, {color: day.color, weight:2, opacity:.6, dashArray:'2 8'}).addTo(lineLayer);
  map.fitBounds(L.latLngBounds(latlngs), {padding:[50,50]});
}

/* ---------- Tab ---------- */
const tabsEl = document.getElementById('tabs');
DAYS.forEach(day => {
  const tab = document.createElement('div');
  tab.className = 'tab';
  tab.style.setProperty('--accent', day.color);
  tab.style.setProperty('--dot', day.color);
  tab.innerHTML = `<span class="dot" style="background:${day.color}"></span>
                   <span class="num">${day.id}</span>
                   <span class="lbl"><b>${day.date}</b><small>${day.label}</small></span>`;
  tab.addEventListener('click', () => selectDay(day.id));
  tab.dataset.id = day.id;
  tabsEl.appendChild(tab);
});

/* ---------- Sidebar ---------- */
const sidebarEl = document.getElementById('sidebar');
function renderSidebar(day){
  let html = `<div class="day-heading">
                <h2>Giorno ${day.id} &mdash; ${day.date}</h2>
                <p>${day.label}</p>
              </div>
              <div class="day-note">${day.note}</div>
              <div class="timeline" style="--accent:${day.color}">`;
  day.stops.forEach(s => {
    const clickable = !!s.lat;
    html += `<div class="stop ${s.meal ? 'meal' : ''} ${clickable ? 'clickable' : ''}" ${clickable ? `data-name="${s.name}"` : ''}>
               <span class="time">${s.time}</span>
               <span class="node" style="background:${s.meal ? 'var(--bg)' : day.color}"></span>
               <h3>${s.name}${s.duration ? ` <span style="color:var(--ink-dim);font-weight:400;font-size:12px;">&middot; ${s.duration}</span>` : ''}</h3>
               <p>${s.desc}${linkHtml(s)}</p>
               ${badgesHtml(s)}
             </div>`;
  });
  html += `</div>
           <div class="foot">Orari e prezzi verificati a novembre 2026 su siti ufficiali e guide: ricontrollate a ridosso della partenza (scioperi, chiusure straordinarie). Per gli ingressi gratuiti servono carta d'identità e residenza/cittadinanza nello Spazio economico europeo, con meno di 26 anni.</div>`;
  sidebarEl.innerHTML = html;
  sidebarEl.scrollTop = 0;

  sidebarEl.querySelectorAll('.stop.clickable').forEach(el => {
    el.addEventListener('click', () => {
      const m = markerRefs[el.dataset.name];
      if(m){ map.panTo(m.getLatLng()); m.openPopup(); }
    });
  });
}

function selectDay(id){
  const day = DAYS.find(d => d.id === id);
  document.querySelectorAll('.tab').forEach(t => t.classList.toggle('active', +t.dataset.id === id));
  renderSidebar(day);
  renderDay(day);
}

document.getElementById('dayCount').textContent =
  `${DAYS.length} giorni · ${DAYS.reduce((a,d)=>a+d.stops.filter(s=>s.lat).length,0)} tappe`;
selectDay(1);

window.addEventListener('resize', () => setTimeout(() => map.invalidateSize(), 150));
setTimeout(() => map.invalidateSize(), 200);
</script>
</body>
</html>
