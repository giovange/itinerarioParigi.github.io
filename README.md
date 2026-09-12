
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
    --bg: #0E1526;
    --panel: #141C31;
    --panel-2: #1B2439;
    --line: #2A3450;
    --ink: #F3EFE4;
    --ink-dim: #9AA6C0;
    --gold: #C9A227;
    --day1: #C08497;
    --day2: #C9A227;
    --day3: #4C93A6;
    --day4: #8C6FA6;
    --free: #7FB88A;
    --paid: #D08A4F;
  }
  *{box-sizing:border-box;}
  html,body{height:100%;}
  body{
    margin:0;
    background:var(--bg);
    color:var(--ink);
    font-family:'Inter',sans-serif;
    overflow:hidden;
  }
  .app{
    display:grid;
    grid-template-rows:auto auto 1fr;
    height:100vh;
  }

  /* Header */
  header{
    padding:22px 28px 14px;
    border-bottom:1px solid var(--line);
    display:flex;
    align-items:baseline;
    justify-content:space-between;
    flex-wrap:wrap;
    gap:8px;
  }
  header .title{
    font-family:'Fraunces',serif;
    font-weight:600;
    font-size:26px;
    letter-spacing:0.2px;
  }
  header .title span{color:var(--gold);}
  header .sub{
    color:var(--ink-dim);
    font-size:13.5px;
  }

  /* Ticket-style day tabs */
  nav.tabs{
    display:flex;
    gap:10px;
    padding:14px 28px;
    border-bottom:1px solid var(--line);
    overflow-x:auto;
  }
  .tab{
    position:relative;
    flex:0 0 auto;
    display:flex;
    align-items:center;
    gap:10px;
    padding:9px 16px 9px 14px;
    background:var(--panel);
    border:1px solid var(--line);
    border-radius:3px;
    cursor:pointer;
    transition:border-color .15s ease, transform .15s ease;
    -webkit-tap-highlight-color:transparent;
  }
  .tab:hover{ transform:translateY(-1px); }
  .tab::after{
    content:"";
    position:absolute;
    right:-6px; top:50%;
    width:11px; height:11px;
    background:var(--bg);
    border-radius:50%;
    transform:translateY(-50%);
    border-left:1px solid var(--line);
  }
  .tab:last-child::after{display:none;}
  .tab .num{
    font-family:'Fraunces',serif;
    font-size:19px;
    font-weight:600;
    color:var(--dot);
  }
  .tab .lbl{
    display:flex;
    flex-direction:column;
    line-height:1.25;
  }
  .tab .lbl b{font-size:12.5px; font-weight:600;}
  .tab .lbl small{font-size:11px; color:var(--ink-dim);}
  .tab .dot{
    width:8px; height:8px; border-radius:50%;
    flex:0 0 auto;
  }
  .tab.active{
    border-color: var(--accent);
    background: var(--panel-2);
  }
  .tab.active .lbl b{color:var(--ink);}

  /* Main layout */
  .main{
    display:grid;
    grid-template-columns:380px 1fr;
    min-height:0;
  }
  .sidebar{
    border-right:1px solid var(--line);
    overflow-y:auto;
    padding:18px 0 40px;
  }
  .day-heading{
    padding:2px 22px 14px;
  }
  .day-heading h2{
    font-family:'Fraunces',serif;
    font-weight:600;
    font-size:20px;
    margin:0 0 4px;
  }
  .day-heading p{
    margin:0;
    color:var(--ink-dim);
    font-size:13px;
  }

  .timeline{
    position:relative;
    padding:6px 22px 0 84px;
  }
  .timeline::before{
    content:"";
    position:absolute;
    left:54px; top:6px; bottom:20px;
    width:1px;
    background:var(--line);
  }
  .stop{
    position:relative;
    padding-bottom:22px;
    cursor:default;
  }
  .stop.clickable{cursor:pointer;}
  .stop .time{
    position:absolute;
    left:-84px; top:1px;
    width:42px;
    text-align:right;
    font-variant-numeric:tabular-nums;
    font-size:12px;
    color:var(--gold);
    font-weight:600;
  }
  .stop .node{
    position:absolute;
    left:-30px; top:3px;
    width:9px;height:9px;
    border-radius:50%;
    background:var(--accent);
    border:2px solid var(--bg);
    box-shadow:0 0 0 1px var(--accent);
  }
  .stop.meal .node{
    background:var(--bg);
    box-shadow:0 0 0 1px var(--line);
  }
  .stop h3{
    margin:0 0 3px;
    font-size:14.5px;
    font-weight:600;
    font-family:'Inter',sans-serif;
  }
  .stop.clickable h3:hover{color:var(--gold);}
  .stop p{
    margin:0;
    font-size:12.8px;
    line-height:1.5;
    color:var(--ink-dim);
  }
  .stop .price{
    display:inline-flex;
    align-items:center;
    gap:5px;
    margin-top:6px;
    font-size:11.5px;
    padding:2px 8px 2px 6px;
    border-radius:20px;
    background:var(--panel-2);
    border:1px solid var(--line);
  }
  .price .pdot{width:6px;height:6px;border-radius:50%;}
  .price.free .pdot{background:var(--free);}
  .price.paid .pdot{background:var(--paid);}

  /* Map */
  #map{ width:100%; height:100%; background:var(--panel); }
  .leaflet-popup-content-wrapper{
    background:var(--panel-2);
    color:var(--ink);
    border-radius:6px;
  }
  .leaflet-popup-tip{background:var(--panel-2);}
  .leaflet-popup-content{margin:12px 14px; font-family:'Inter',sans-serif;}
  .popup-title{font-family:'Fraunces',serif; font-weight:600; font-size:15px; margin-bottom:3px;}
  .popup-time{color:var(--gold); font-size:12px; font-weight:600; margin-bottom:6px;}
  .popup-desc{font-size:12.5px; color:var(--ink-dim); line-height:1.5;}

  /* legend */
  .legend{
    position:absolute;
    left:398px; bottom:18px;
    z-index:400;
    background:rgba(20,28,49,.92);
    border:1px solid var(--line);
    border-radius:8px;
    padding:10px 14px;
    font-size:11.5px;
    color:var(--ink-dim);
    display:flex;
    gap:14px;
    backdrop-filter:blur(3px);
  }
  .legend span{display:flex;align-items:center;gap:6px;}
  .legend .pdot{width:7px;height:7px;border-radius:50%;}

  ::-webkit-scrollbar{width:8px;}
  ::-webkit-scrollbar-thumb{background:var(--line); border-radius:4px;}

  @media (max-width:820px){
    html,body{height:auto; overflow:visible;}
    .app{height:auto; display:block;}
    header{padding:18px 18px 12px;}
    nav.tabs{padding:12px 18px;}
    .main{display:block;}
    #map{
      order:0;
      height:42vh;
      min-height:260px;
      width:100%;
    }
    .sidebar{
      order:1;
      border-right:none;
      border-top:1px solid var(--line);
      overflow-y:visible;
      max-height:none;
      height:auto;
      -webkit-overflow-scrolling:touch;
    }
    .legend{left:14px; right:14px; bottom:auto; top:calc(42vh + 6px); transform:translateY(-100%);}
    header .title{font-size:21px;}
  }
</style>
</head>
<body>
<div class="app">

  <header>
    <div>
      <div class="title">Parigi <span>·</span> 6&ndash;9 Dicembre</div>
      <div class="sub">Itinerario di viaggio &mdash; base: 7 Rue des Innocents, 75001</div>
    </div>
    <div class="sub" id="dayCount"></div>
  </header>

  <nav class="tabs" id="tabs"></nav>

  <div class="main">
    <aside class="sidebar" id="sidebar"></aside>
    <div id="map"></div>
  </div>

</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.js"></script>
<script>
const HOME = { name:"Appartamento", lat:48.8603534, lng:2.3477552, desc:"7 Rue des Innocents &mdash; base del gruppo per tutto il soggiorno." };

const DAYS = [
  {
    id:1, color:"#C08497", label:"Île de la Cité, Marais", date:"Sab 6 dic",
    stops:[
      {time:"08:30", name:"Arrivo aeroporto ORY", desc:"Trasferimento verso l'appartamento (Orlybus + RER B, o taxi ~30-35 min).", meal:true},
      {time:"09:45", name:"Check-in appartamento", desc:"Deposito bagagli, sistemazione veloce.", meal:true},
      {time:"10:30", name:"Notre-Dame", lat:48.8529682, lng:2.3499021, desc:"Esterno e interno della cattedrale. Ingresso gratuito, nessuna prenotazione per il piano terra.", duration:"~45 min", price:"free", priceLabel:"Ingresso gratuito"},
      {time:"11:15", name:"Sainte-Chapelle", lat:48.855375, lng:2.3449609, desc:"Vetrate gotiche tra le più belle d'Europa. Prenotazione slot fortemente consigliata.", duration:"~45-60 min", price:"free", priceLabel:"Gratis under 26 UE · altrim. ~13€"},
      {time:"12:15", name:"Pranzo", desc:"Île Saint-Louis o Quartiere Latino.", meal:true},
      {time:"13:30", name:"Le Marais / Place des Vosges", lat:48.8556114, lng:2.3655266, desc:"Passeggiata nel quartiere storico: boutique vintage, street art, Place des Vosges.", duration:"~2h"},
      {time:"15:30", name:"Pausa merenda/caffè", desc:"Scelta libera nel Marais.", meal:true},
      {time:"19:30", name:"Cena", desc:"Zona Marais o Les Halles.", meal:true},
      {time:"21:00", name:"Bar / locali", desc:"Scelta libera per la serata.", meal:true}
    ]
  },
  {
    id:2, color:"#C9A227", label:"Louvre, Concorde, Champs-Élysées", date:"Dom 7 dic",
    stops:[
      {time:"09:30", name:"Colazione", desc:"Scelta libera.", meal:true},
      {time:"10:30", name:"Museo del Louvre", lat:48.8606111, lng:2.337644, desc:"Prenotazione slot obbligatoria su louvre.fr, anche se gratuito.", duration:"3h", price:"free", priceLabel:"Gratis under 26 UE · altrim. 22€"},
      {time:"13:30", name:"Pranzo", desc:"Zona Palais-Royal / Rue de Rivoli.", meal:true},
      {time:"14:30", name:"Jardin des Tuileries", lat:48.8634916, lng:2.3274943, desc:"Passeggiata nel giardino tra Louvre e Concorde.", duration:"~30 min"},
      {time:"15:00", name:"Musée de l'Orangerie", lat:48.8637884, lng:2.3226724, desc:"Le Ninfee di Monet nelle sale ovali. Prenotazione online consigliata.", duration:"1h", price:"free", priceLabel:"Gratis under 26 UE · altrim. 14,50€"},
      {time:"16:00", name:"Place de la Concorde", lat:48.8656331, lng:2.3212357, desc:"Obelisco di Luxor, foto panoramiche.", duration:"~30 min"},
      {time:"17:15", name:"Arc de Triomphe", lat:48.8737917, lng:2.2950275, desc:"Salita alla terrazza panoramica, splendida al tramonto. Prenotazione consigliata.", duration:"~45 min", price:"free", priceLabel:"Gratis under 26 UE · altrim. ~16€"},
      {time:"19:30", name:"Cena", desc:"Zona Champs-Élysées.", meal:true},
      {time:"21:00", name:"Bar / locali", desc:"Scelta libera per la serata.", meal:true}
    ]
  },
  {
    id:3, color:"#4C93A6", label:"Montmartre, Orsay, Tour Eiffel", date:"Lun 8 dic",
    stops:[
      {time:"09:30", name:"Colazione", desc:"Scelta libera.", meal:true},
      {time:"10:45", name:"Sacré-Cœur", lat:48.8867046, lng:2.3431043, desc:"Basilica (ingresso gratuito) e vista panoramica dal sagrato. Cupola a pagamento a parte.", duration:"~35 min", price:"free", priceLabel:"Basilica gratuita · cupola ~8-10€"},
      {time:"12:00", name:"Le Mur des Je t'aime", lat:48.884856, lng:2.3385644, desc:"Square Jehan-Rictus: il muro con \"ti amo\" scritto in decine di lingue.", duration:"~20 min", price:"free", priceLabel:"Gratuito"},
      {time:"12:30", name:"Moulin Rouge", lat:48.8841232, lng:2.3322519, desc:"Foto dell'esterno, zona Pigalle.", duration:"~15 min"},
      {time:"13:00", name:"Pranzo", desc:"Zona Pigalle / Montmartre.", meal:true},
      {time:"14:30", name:"Musée d'Orsay", lat:48.8599614, lng:2.3265614, desc:"Capolavori impressionisti in un'ex stazione ferroviaria. Prenotazione slot consigliata.", duration:"2h", price:"free", priceLabel:"Gratis under 26 UE · altrim. 16€"},
      {time:"17:30", name:"Tour Eiffel", lat:48.8583701, lng:2.2944813, desc:"Salita di sera, illuminata. Prenotazione obbligatoria su toureiffel.paris.", duration:"~1h30", price:"paid", priceLabel:"14,80€ &ndash; 36,70€ (nessuna gratuità)"},
      {time:"19:00", name:"Trocadéro", lat:48.8627982, lng:2.2876042, desc:"La miglior vista della torre illuminata dall'altra sponda della Senna.", duration:"~30 min"},
      {time:"19:30", name:"Cena", desc:"Zona Trocadéro / Eiffel.", meal:true},
      {time:"21:00", name:"Bar / locali", desc:"Scelta libera per la serata.", meal:true}
    ]
  },
  {
    id:4, color:"#8C6FA6", label:"Panthéon, Madeleine, Lafayette + partenza", date:"Mar 9 dic",
    stops:[
      {time:"09:30", name:"Colazione", desc:"Scelta libera.", meal:true},
      {time:"10:45", name:"Panthéon", lat:48.8462218, lng:2.3464138, desc:"Cripta con Voltaire, Rousseau, Hugo, Marie Curie. Prenotazione consigliata.", duration:"1h", price:"free", priceLabel:"Gratis under 26 UE · altrim. ~13€"},
      {time:"11:45", name:"Quartiere Latino", desc:"Sorbona, Rue Mouffetard, libreria Shakespeare & Company.", meal:true},
      {time:"13:00", name:"Pranzo", desc:"Quartiere Latino.", meal:true},
      {time:"14:30", name:"Place de la Madeleine", lat:48.8699468, lng:2.3248903, desc:"Chiesa neoclassica e boutique gourmet.", duration:"~45 min", price:"free", priceLabel:"Gratuito"},
      {time:"15:15", name:"Galeries Lafayette", lat:48.8736224, lng:2.3321099, desc:"La celebre cupola Art Nouveau e la terrazza panoramica sul tetto.", duration:"1h", price:"free", priceLabel:"Ingresso e terrazza gratuiti"},
      {time:"16:45", name:"Palais-Royal", lat:48.8637569, lng:2.3371261, desc:"Cortile con le colonne di Buren, giardino.", duration:"~30 min", price:"free", priceLabel:"Gratuito"},
      {time:"18:00", name:"Rientro in appartamento", desc:"Recupero bagagli.", meal:true},
      {time:"19:00", name:"Aeroporto Paris-Orly", lat:48.7299182, lng:2.3732304, desc:"Arrivo 2h prima della partenza, come richiesto.", price:"none", priceLabel:"Partenza ore 21:00"}
    ]
  }
];

// ---------- Map setup ----------
const map = L.map('map', { zoomControl:true }).setView([48.8608, 2.3376], 13);
L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png', {
  attribution:'&copy; OpenStreetMap &copy; CARTO',
  subdomains:'abcd', maxZoom:19
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

function renderDay(day){
  markerLayer.clearLayers();
  lineLayer.clearLayers();
  markerRefs = {};

  // Home marker
  L.marker([HOME.lat, HOME.lng], {icon: homeIcon('#F3EFE4')})
    .bindPopup(`<div class="popup-title">${HOME.name}</div><div class="popup-desc">${HOME.desc}</div>`)
    .addTo(markerLayer);

  const geoStops = day.stops.filter(s => s.lat);
  const latlngs = [[HOME.lat, HOME.lng]];

  geoStops.forEach((s, i) => {
    const marker = L.marker([s.lat, s.lng], {icon: numIcon(i+1, day.color)});
    let priceHtml = '';
    if(s.priceLabel){
      priceHtml = `<div class="popup-desc" style="margin-top:6px;">${s.priceLabel}</div>`;
    }
    marker.bindPopup(
      `<div class="popup-time">${s.time}${s.duration ? ' · ' + s.duration : ''}</div>
       <div class="popup-title">${s.name}</div>
       <div class="popup-desc">${s.desc}</div>${priceHtml}`
    );
    marker.addTo(markerLayer);
    markerRefs[s.name] = marker;
    latlngs.push([s.lat, s.lng]);
  });

  L.polyline(latlngs, {color: day.color, weight:2, opacity:.6, dashArray:'2 8'}).addTo(lineLayer);

  const bounds = L.latLngBounds(latlngs);
  map.fitBounds(bounds, {padding:[50,50]});
}

// ---------- Tabs ----------
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

// ---------- Sidebar ----------
const sidebarEl = document.getElementById('sidebar');
function renderSidebar(day){
  let html = `<div class="day-heading">
                <h2>Giorno ${day.id} &mdash; ${day.date}</h2>
                <p>${day.label}</p>
              </div>
              <div class="timeline" style="--accent:${day.color}">`;
  day.stops.forEach(s => {
    const clickable = !!s.lat;
    let price = '';
    if(s.price === 'free') price = `<div class="price free"><span class="pdot"></span>${s.priceLabel}</div>`;
    else if(s.price === 'paid') price = `<div class="price paid"><span class="pdot"></span>${s.priceLabel}</div>`;
    else if(s.priceLabel) price = `<div class="price"><span class="pdot" style="background:var(--ink-dim)"></span>${s.priceLabel}</div>`;

    html += `<div class="stop ${s.meal ? 'meal' : ''} ${clickable ? 'clickable' : ''}" ${clickable ? `data-name="${s.name}"` : ''}>
                <span class="time">${s.time}</span>
                <span class="node" style="background:${s.meal ? 'transparent' : day.color}"></span>
                <h3>${s.name}${s.duration ? ` <span style="color:var(--ink-dim);font-weight:400;font-size:12px;">&middot; ${s.duration}</span>` : ''}</h3>
                <p>${s.desc}</p>
                ${price}
              </div>`;
  });
  html += `</div>`;
  sidebarEl.innerHTML = html;

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

document.getElementById('dayCount').textContent = `${DAYS.length} giorni · ${DAYS.reduce((a,d)=>a+d.stops.filter(s=>s.lat).length,0)} tappe`;
selectDay(1);

window.addEventListener('resize', () => setTimeout(() => map.invalidateSize(), 150));
setTimeout(() => map.invalidateSize(), 200);
</script>
</body>
</html>
