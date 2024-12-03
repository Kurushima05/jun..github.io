<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8"> <!-- 文字エンコーディングをUTF-8に設定 -->
    <title>ゴミ箱マップ</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"> <!-- スマホ対応 -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <style>
        /* 地図の表示を適切にする */
        #map {
            width: 100%;
            height: 60vh; /* 地図の高さを設定 */
        }

        /* ボタンのデザイン */
        button {
            display: block;
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            font-size: 16px;
            background-color: #4CAF50;
            color: white;
            border: none;
            cursor: pointer;
            text-align: center;
        }

        button:hover {
            background-color: #45a049;
        }

        /* スマホでの余白調整 */
        body {
            margin: 0;
            font-family: Arial, sans-serif;
            padding: 10px;
            background-color: #f4f4f4;
        }

        h1 {
            font-size: 24px;
            text-align: center;
        }

        p {
            font-size: 16px;
            text-align: center;
        }

        /* モバイル用のセンタリング */
        .container {
            text-align: center;
        }

    </style>
</head>
<body>
    <div class="container">
        <h1>ゴミ箱マップ</h1>
        <div id="map"></div> <!-- 地図表示部分 -->
        
        <p>地図をタップしてゴミ箱を登録してください。</p>
        <p id="location-status"></p>

        <button id="locate-me">現在地を表示</button>
    </div>

    <script>
        // 地図の初期設定（松山城の位置）
        var map = L.map('map').setView([35.3380, 132.7609], 13); // 松山城の位置に設定
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '© OpenStreetMap contributors'
        }).addTo(map);

        // ローカルストレージからゴミ箱データを読み込む
        var trashBins = JSON.parse(localStorage.getItem('trashBins')) || [];

        // ゴミ箱マーカーを地図に追加
        var markers = []; // マーカーを保持する配列
        trashBins.forEach(function(bin, index) {
            var marker = L.marker([bin.lat, bin.lng]).addTo(map).bindPopup(bin.description + '<br><button onclick="deleteBin(' + index + ')">削除</button>').openPopup();
            marker.binData = bin; // マーカーにデータを紐付け
            markers.push(marker);
        });

        // 地図をタップしてゴミ箱を登録
        map.on('click', function(e) {
            var lat = e.latlng.lat;
            var lng = e.latlng.lng;

            // ゴミ箱の説明を入力
            var description = prompt("ゴミ箱の説明を入力してください:", "新しいゴミ箱");
            if (!description) {
                alert("登録がキャンセルされました。");
                return;
            }

            // ゴミ箱データを追加
            var newBin = { lat: lat, lng: lng, description: description };
            trashBins.push(newBin);

            // 地図にマーカーを追加
            var marker = L.marker([lat, lng]).addTo(map).bindPopup(description + '<br><button onclick="deleteBin(' + (trashBins.length - 1) + ')">削除</button>').openPopup();
            marker.binData = newBin; // マーカーにデータを紐付け

            markers.push(marker);

            // ローカルストレージにゴミ箱データを保存
            localStorage.setItem('trashBins', JSON.stringify(trashBins));

            console.log("登録されたゴミ箱:", trashBins);
        });

        // 現在地取得ボタンのクリックイベント
        document.getElementById('locate-me').addEventListener('click', function() {
            var status = document.getElementById('location-status');
            status.textContent = "現在地を取得中...";

            if (!navigator.geolocation) {
                status.textContent = "お使いのブラウザは現在地取得に対応していません。";
                return;
            }

            navigator.geolocation.getCurrentPosition(
                function(position) {
                    var lat = position.coords.latitude;
                    var lng = position.coords.longitude;

                    // 赤いアイコンを設定
                    var redIcon = L.icon({
                        iconUrl: 'https://unpkg.com/leaflet@1.9.4/dist/images/marker-icon-2x.png', // 標準のアイコン
                        iconSize: [25, 41], // サイズ設定
                        iconAnchor: [12, 41], // アイコンの位置調整
                        popupAnchor: [1, -34], // ポップアップの位置調整
                        shadowSize: [41, 41]
                    });

                    // 現在地を赤いアイコンでマップに表示
                    L.marker([lat, lng], { icon: redIcon }).addTo(map).bindPopup("あなたの現在地").openPopup();
                    map.setView([lat, lng], 15); // 現在地にズーム
                    status.textContent = "現在地を表示しました。";
                },
                function() {
                    status.textContent = "現在地を取得できませんでした。";
                }
            );
        });

        // ゴミ箱を削除する関数
        window.deleteBin = function(index) {
            if (confirm("このゴミ箱を削除しますか？")) {
                var binToDelete = trashBins[index];
                trashBins.splice(index, 1); // 配列から削除

                // ローカルストレージにデータを更新
                localStorage.setItem('trashBins', JSON.stringify(trashBins));

                // 地図上のマーカーを削除
                map.eachLayer(function(layer) {
                    if (layer instanceof L.Marker && layer.binData === binToDelete) {
                        map.removeLayer(layer); // マーカーを地図から削除
                    }
                });

                console.log("ゴミ箱が削除されました:", binToDelete);
            }
        };
    </script>
</body>
</html>
