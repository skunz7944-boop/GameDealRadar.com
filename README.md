<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Game Deal Radar - All Platforms</title>
    <style>
        body { font-family: 'Segoe UI', sans-serif; background: #0f172a; color: white; margin: 0; padding: 20px; text-align: center; }
        .container { max-width: 900px; margin: auto; }
        h1 { color: #38bdf8; font-size: 2.5rem; text-shadow: 0 0 10px rgba(56, 189, 248, 0.5); }
        .search-box { margin-bottom: 30px; display: flex; gap: 10px; justify-content: center; }
        input { padding: 12px; border-radius: 8px; border: none; width: 60%; font-size: 1rem; background: #1e293b; color: white; border: 1px solid #334155; }
        button { padding: 12px 20px; border-radius: 8px; border: none; background: #38bdf8; color: #0f172a; font-weight: bold; cursor: pointer; transition: 0.3s; }
        button:hover { background: #7dd3fc; transform: scale(1.05); }
        #results { display: grid; grid-template-columns: repeat(auto-fill, minmax(250px, 1fr)); gap: 20px; margin-top: 20px; }
        .card { background: #1e293b; padding: 15px; border-radius: 15px; border: 1px solid #334155; text-align: left; position: relative; }
        .card img { width: 100%; border-radius: 10px; margin-bottom: 10px; height: 120px; object-fit: cover; }
        .price-tag { background: #4ade80; color: #064e3b; padding: 5px 10px; border-radius: 5px; font-weight: bold; font-size: 1.1rem; }
        .platform-buttons { display: flex; flex-wrap: wrap; gap: 5px; margin-top: 15px; border-top: 1px solid #334155; padding-top: 10px; }
        .btn-p { padding: 5px 8px; border-radius: 4px; text-decoration: none; font-size: 0.75rem; font-weight: bold; color: white; }
        .ps { background: #003791; } /* PlayStation Blau */
        .xbox { background: #107c10; } /* Xbox Grün */
        .switch { background: #e60012; } /* Nintendo Rot */
        .pc { background: #38bdf8; color: #0f172a; } /* PC Hellblau */
    </style>
</head>
<body>
    <div class="container">
        <h1>🎮 Game Deal Radar</h1>
        <p>Preise checken auf PC, PlayStation, Xbox & Switch</p>
        
        <div class="search-box">
            <input type="text" id="gameSearch" placeholder="Spielname (z.B. GTA, FIFA, Zelda)...">
            <button onclick="searchGames()">Deals finden</button>
        </div>

        <div id="results">
            <!-- Ergebnisse kommen hier rein -->
        </div>
    </div>

    <script>
        async function searchGames() {
            const query = document.getElementById('gameSearch').value;
            const resultsDiv = document.getElementById('results');
            if (!query) return;

            resultsDiv.innerHTML = "<p>Suche auf allen Plattformen...</p>";

            try {
                // Wir nutzen die CheapShark API für PC-Daten als Basis
                const response = await fetch(`https://www.cheapshark.com/api/1.0/deals?title=${query}&limit=12`);
                const deals = await response.json();

                resultsDiv.innerHTML = "";

                if (deals.length === 0) {
                    resultsDiv.innerHTML = "<p>Kein Spiel mit diesem Namen gefunden. Probier's mal mit einem anderen Titel!</p>";
                    return;
                }

                deals.forEach(deal => {
                    const encodedTitle = encodeURIComponent(deal.title);
                    const card = document.createElement('div');
                    card.className = 'card';
                    card.innerHTML = `
                        <img src="${deal.thumb}" alt="${deal.title}">
                        <h3 style="margin: 0 0 10px 0; font-size: 1.1rem;">${deal.title}</h3>
                        <div style="margin-bottom: 10px;">
                            <span class="price-tag">${deal.salePrice}€</span>
                            <small style="color: #94a3b8; margin-left: 10px;">(PC Deal)</small>
                        </div>
                        
                        <div class="platform-buttons">
                            <a href="https://www.cheapshark.com/redirect?dealID=${deal.dealID}" target="_blank" class="btn-p pc">PC Shop</a>
                            <a href="https://store.playstation.com/de-de/search/${encodedTitle}" target="_blank" class="btn-p ps">PS Store</a>
                            <a href="https://www.xbox.com/de-DE/search?q=${encodedTitle}" target="_blank" class="btn-p xbox">Xbox</a>
                            <a href="https://www.nintendo.de/Suche-/Suche-299117.html?q=${encodedTitle}" target="_blank" class="btn-p switch">Switch</a>
                        </div>
                    `;
                    resultsDiv.appendChild(card);
                });
            } catch (error) {
                resultsDiv.innerHTML = "<p>Ups! Da gab es ein Problem. Check mal deine Internetverbindung.</p>";
            }
        }
    </script>
</body>
</html>
