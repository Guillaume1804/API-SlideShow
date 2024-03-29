<!DOCTYPE html> avec les icones rouge orange vert
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Carte des Trottinettes à Marseille</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
    <link href="./css/baseCustom.css" rel="stylesheet">
    <link rel="stylesheet" href="./css/style.css">
    <link rel="stylesheet" href="./css/baseCustom.css" />
    <script src="./js/script.js" ></script>

    <style>
        #map {
            width: 800px;
            height: 600px;
        }
    </style>
</head>
<body>
    <div id="map"></div>

    <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
    <script>

    ```javascript
        var map = L.map('map').setView([43.2965, 5.3698], 13);

        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '&copy; Contributeurs OpenStreetMap'
        }).addTo(map);

        var apiKey = "MjE0ZDNmMGEtNGFkZS00M2FlLWFmMWItZGNhOTZhMWQyYzM2"; 

        // Fonction pour obtenir les informations sur les stations
        function stationInformation() {
            return fetch("https://api.omega.fifteen.eu/gbfs/2.2/marseille/en/station_information.json?key=" + apiKey)
                .then(response => response.json())
                .then(data => data.data.stations);
        }

        // Fonction pour obtenir les status des stations (vélos disponibles)
        function stationStatus() {
            return fetch("https://api.omega.fifteen.eu/gbfs/2.2/marseille/en/station_status.json?key=" + apiKey)
                .then(response => response.json())
                .then(data => data.data.stations);
        }

        // Fonction pour obtenir l'icône de vélo en fonction du nombre de vélos disponibles
        function getBikeIcon(numBikesAvailable) {
            if (numBikesAvailable > 5) {
                return L.icon({
                    iconUrl: 'file:///C:/Users/simplon/ez-bike/EZ-BIKE/css/images/bike-green.svg',
                    iconSize: [32, 32],
                    iconAnchor: [16, 32],
                    popupAnchor: [0, -32]
                });
            } else if (numBikesAvailable > 0) {
                return L.icon({
                    iconUrl: 'file:///C:/Users/simplon/ez-bike/EZ-BIKE/css/images/bike-orange.svg',
                    iconSize: [32, 32],
                    iconAnchor: [16, 32],
                    popupAnchor: [0, -32]
                });
            } else {
                return L.icon({
                    iconUrl: 'file:///C:/Users/simplon/ez-bike/EZ-BIKE/css/images/bike-red.svg',
                    iconSize: [32, 32],
                    iconAnchor: [16, 32],
                    popupAnchor: [0, -32]
                });
            }
        }

        // Fonction pour ajouter les marqueurs/ popup sur la carte avec les informations
        Promise.all([stationInformation(), stationStatus()])
            .then(([stationsInfo, stationsStatus]) => {
                stationsInfo.forEach(stationInfo => {
                    var stationStatusInfo = stationsStatus.find(status => status.station_id === stationInfo.station_id);
                    console.log("========>",stationStatusInfo);
                    console.log(stationInfo); //données de la station ici
                    if (stationInfo.lat && stationInfo.lon && stationStatusInfo) {
                        var marker = L.marker([parseFloat(stationInfo.lat), parseFloat(stationInfo.lon)], {
                            icon: getBikeIcon(stationStatusInfo.num_bikes_available)
                        }).addTo(map);
                        marker.bindPopup('<b>Nom de la station:</b> ' + stationInfo.name + '<br><b>Vélos disponibles:</b> ' + stationStatusInfo.num_bikes_available);
                    }
                });
            })
            .catch(error => console.error('Erreur lors de la récupération des données:', error));
    </script>
</body>
</html>