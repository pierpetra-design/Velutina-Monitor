# Velutina-Monitor
Monitoraggio Velutina 
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Monitoraggio Vespa Velutina</title>

<link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css"/>

<style>

body{
font-family:Arial;
margin:20px;
background:#f2f2f2;
}

h1{
text-align:center;
}

.card{
background:white;
padding:20px;
margin-bottom:20px;
border-radius:10px;
}

input,button{
padding:8px;
margin:5px;
}

#map{
height:400px;
}

table{
width:100%;
border-collapse:collapse;
}

td,th{
border:1px solid #ccc;
padding:5px;
}

</style>

</head>

<body>

<h1>Monitoraggio Vespa Velutina</h1>

<div class="card">

<h3>Nuova Trappola</h3>

ID trappola<br>
<input id="id"><br>

Regine<br>
<input id="regine" type="number" value="0"><br>

Operaie<br>
<input id="operaie" type="number" value="0"><br>

Maschi<br>
<input id="maschi" type="number" value="0"><br>

<button onclick="gps()">Ottieni GPS</button>

<p id="posizione"></p>

<button onclick="salva()">Salva trappola</button>

</div>

<div class="card">

<h3>Trappole registrate</h3>

<button onclick="exportCSV()">Esporta Excel</button>

<table id="tabella">

<tr>
<th>ID</th>
<th>Regine</th>
<th>Operaie</th>
<th>Maschi</th>
<th>Lat</th>
<th>Lon</th>
<th>Data</th>
</tr>

</table>

</div>

<div class="card">

<h3>Mappa trappole</h3>

<div id="map"></div>

</div>

<script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>

<script>

let lat
let lon

let map=L.map('map').setView([45,9],5)

L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',{
maxZoom:19
}).addTo(map)

function gps(){

navigator.geolocation.getCurrentPosition(function(pos){

lat=pos.coords.latitude
lon=pos.coords.longitude

document.getElementById("posizione").innerHTML=
lat+" , "+lon

map.setView([lat,lon],14)

})

}

function salva(){

let dati=JSON.parse(localStorage.getItem("trappole")||"[]")

let t={

id:document.getElementById("id").value,
regine:document.getElementById("regine").value,
operaie:document.getElementById("operaie").value,
maschi:document.getElementById("maschi").value,
lat:lat,
lon:lon,
data:new Date().toISOString()

}

dati.push(t)

localStorage.setItem("trappole",JSON.stringify(dati))

carica()

}

function carica(){

let dati=JSON.parse(localStorage.getItem("trappole")||"[]")

let tab=document.getElementById("tabella")

tab.innerHTML=`<tr>
<th>ID</th>
<th>Regine</th>
<th>Operaie</th>
<th>Maschi</th>
<th>Lat</th>
<th>Lon</th>
<th>Data</th>
</tr>`

dati.forEach(t=>{

tab.innerHTML+=`
<tr>
<td>${t.id}</td>
<td>${t.regine}</td>
<td>${t.operaie}</td>
<td>${t.maschi}</td>
<td>${t.lat}</td>
<td>${t.lon}</td>
<td>${t.data}</td>
</tr>`

if(t.lat){

L.marker([t.lat,t.lon])
.addTo(map)
.bindPopup("Trappola "+t.id)

}

})

}

function exportCSV(){

let dati=JSON.parse(localStorage.getItem("trappole")||"[]")

let csv="ID,Regine,Operaie,Maschi,Lat,Lon,Data\n"

dati.forEach(t=>{

csv+=${t.id},${t.regine},${t.operaie},${t.maschi},${t.lat},${t.lon},${t.data}\n

})

let blob=new Blob([csv])

let a=document.createElement("a")

a.href=URL.createObjectURL(blob)

a.download="trappole_velutina.csv"

a.click()

}

carica()

</script>

</body>
</html>
