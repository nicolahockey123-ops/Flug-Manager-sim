<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<title>Flight Manager 2025</title>
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"/>
<style>
  body { font-family: Arial, sans-serif; margin:0; padding:0; }
  #map { height: 600px; width: 100%; }
  .control { padding:10px; background:#f0f0f0; }
  select, button, input { margin:5px; padding:5px; }
  .plane { font-size: 24px; }
</style>
</head>
<body>

<div class="control">
  <label>Von: <select id="fromSelect"></select></label>
  <label>Nach: <select id="toSelect"></select></label>
  <label>Flugzeug auswählen: <select id="planeSelect"></select></label>
  <button onclick="startFlight()">Flug starten</button>
  <button onclick="buyPlane()">Flugzeug kaufen (1000€)</button>
  <button onclick="upgradePlane()">Upgrade Flugzeug (500€)</button>
  <br>
  <label>Code eingeben: <input id="codeInput" type="text">
  <button onclick="applyCode()">Einlösen</button></label>
  <p>Geld: <span id="money">5000</span>€ | Fluggeschwindigkeit: <span id="speed">1x</span></p>
  <p>Flugzeuge: <span id="planeCount">0</span></p>
</div>

<div id="map"></div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script>
// Flughäfen
const cities = {
  "Zürich":[47.3769,8.5417], "Bern":[46.9481,7.4474], "Paris":[48.8566,2.3522],
  "Berlin":[52.5200,13.4050], "London":[51.5074,-0.1278], "Madrid":[40.4168,-3.7038],
  "Wien":[48.2082,16.3738], "Helsinki":[60.1695,24.9354], "Brüssel":[50.8503,4.3517],
  "Kopenhagen":[55.6761,12.5683], "Rom":[41.9028,12.4964], "Warschau":[52.2297,21.0122],
  "Reykjavik":[64.1355,-21.8954], "LA":[34.0522,-118.2437], "Beijing":[39.9042,116.4074],
  "Moskau":[55.7558,37.6173]
};

const europeCities = ["Zürich","Bern","Paris","Berlin","London","Madrid","Wien","Helsinki","Brüssel","Kopenhagen","Rom","Warschau","Reykjavik"];

let planes = []; // alle Flugzeuge
let money = 5000;
let speedMultiplier = 1;

const map = L.map('map').setView([50, 10], 4);
L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);

// Flughäfen markieren
for(let city in cities){
  L.marker(cities[city]).addTo(map).bindPopup(city);
}

// Dropdowns befüllen
const fromSelect = document.getElementById('fromSelect');
const toSelect = document.getElementById('toSelect');
const planeSelect = document.getElementById('planeSelect');

for(let city in cities){
  const opt1 = document.createElement('option'); opt1.value = city; opt1.text=city; fromSelect.appendChild(opt1);
  const opt2 = document.createElement('option'); opt2.value = city; opt2.text=city; toSelect.appendChild(opt2);
}

function updateMoney(){ document.getElementById('money').innerText = money; }
function updateSpeedDisplay(){ document.getElementById('speed').innerText = speedMultiplier + "x"; }
function updatePlaneList(){
  planeSelect.innerHTML="";
  planes.forEach((p,i)=>{
    const opt = document.createElement('option');
    opt.value=i;
    opt.text="Flugzeug " + (i+1) + " (Level "+p.level+")";
    planeSelect.appendChild(opt);
  });
  document.getElementById('planeCount').innerText = planes.length;
}

function buyPlane(){
  if(money>=1000){
    money -= 1000;
    planes.push({level:1});
    updateMoney();
    updatePlaneList();
    alert("Flugzeug gekauft!");
  } else alert("Nicht genug Geld!");
}

function upgradePlane(){
  if(planes.length===0){ alert("Du hast kein Flugzeug!"); return; }
  if(money>=500){
    money -= 500;
    const selected = planeSelect.value;
    planes[selected].level +=1;
    updateMoney();
    updatePlaneList();
    alert("Flugzeug auf Level " + planes[selected].level + " gebracht!");
  } else alert("Nicht genug Geld!");
}

function startFlight(){
  if(planes.length===0){ alert("Du hast kein Flugzeug!"); return; }
  const selectedPlane = planeSelect.value;
  const fromCity = fromSelect.value;
  const toCity = toSelect.value;
  if(fromCity===toCity){ alert("Start und Ziel müssen unterschiedlich sein!"); return; }

  const plane = planes[selectedPlane];
  if(plane.marker) map.removeLayer(plane.marker);

  plane.marker = L.marker(cities[fromCity], {icon:L.divIcon({className:'plane', html:'✈️'})}).addTo(map);

  let steps = 100;
  let step = 0;
  const fromLatLng = L.latLng(cities[fromCity]);
  const toLatLng = L.latLng(cities[toCity]);

  // Geschwindigkeit Europa 1x, außerhalb 2x + Flugzeug-Level
  speedMultiplier = (europeCities.includes(toCity) ? 1 : 2) * plane.level;
  updateSpeedDisplay();

  const interval = setInterval(()=>{
    step++;
    const lat = fromLatLng.lat + (toLatLng.lat - fromLatLng.lat) * (step/steps);
    const lng = fromLatLng.lng + (toLatLng.lng - fromLatLng.lng) * (step/steps);
    plane.marker.setLatLng([lat,lng]);
    if(step>=steps){
      clearInterval(interval);
      let earnings = 1000 * (europeCities.includes(toCity)?1:2) * plane.level;
      money += earnings;
      updateMoney();
      alert("Flug angekommen! Einnahmen: " + earnings + "€");
    }
  }, 50);
}

// Codes für Geld-Multiplikator
const codes = {
  "X2": ()=> {money*=2; updateMoney(); alert("Geld verdoppelt!");},
  "X5": ()=> {money*=5; updateMoney(); alert("Geld verfünffacht!");},
  "BONUS": ()=> {money+=2000; updateMoney(); alert("Bonus 2000€ erhalten!");}
};

function applyCode(){
  const code = document.getElementById('codeInput').value.toUpperCase();
  if(codes[code]) codes[code]();
  else alert("Code ungültig!");
}

updateMoney();
updateSpeedDisplay();
updatePlaneList();
</script>
</body>
</html>
