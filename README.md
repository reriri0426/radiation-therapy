# radiation-therapy
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>放射線治療を体験しよう！</title>

    <style>
        body {
            background-color: #f0f0f0;
            text-align: center;
        }
        #game-container {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            align-items: center;
            width: 400px;
            height: 400px;
            margin: 0 auto;
            border: 2px solid #333;
            background-color: #ffb6c1;
            position: relative;
            overflow: hidden; /* 放射線を切り取るために必要 */
        }
        .cell {
            width: 100px;
            height: 100px;
            border-radius: 50%;
            margin: 10px;
            position: relative;
        }
        #cancer-cell {
            background-color: red;
            width: 200px;
            height: 200px;
            margin: auto; /* 中央配置 */
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            z-index: 1;
            opacity: 1; /* 初期透明度 */
        }
        .normal-cell {
            background-color: green;
            position: absolute;
            z-index: 1;
            opacity: 1; /* 初期透明度 */
        }
        /* 放射線のスタイル */
        .radiation {
            position: absolute;
            width: 100%; /* 大きくする */
            height: 200%; /* 大きくする */
            clip-path: polygon(50% 0%, 0% 100%, 100% 100%); /* 逆扇状に変更 */
             /* 黄色の放射線 */
            transform-origin: center top;
            background-color: rgba(255, 255, 0, 0.5);
            transform: scaleY(0);
            transition: transform 1s ease-in-out; /* アニメーション効果 */
            z-index: 1;
        } 

        .radiation2 {
            position: absolute;
            width: 100%; /* 大きくする */
            height: 200%; /* 大きくする */
            clip-path: polygon(50% 100%, 0% 0%, 100% 0%); /* 逆扇状に変更 */
           /* 黄色の放射線 */
            background-color: rgba(255, 255, 0, 0.5);
            transform-origin: center bottom;
            transform: scaleY(0);
            transition: transform 1s ease-in-out; /* アニメーション効果 */
            z-index: 1;
        }
        .radiation3 {
            position: absolute;
            width: 100%; /* 大きくする */
            height: 100%; /* 大きくする */
            clip-path: polygon(0% 50%, 100% 100%, 100% 0%); /* 逆扇状に変更 */
             /* 黄色の放射線 */
            background-color: rgba(255, 255, 0, 0.5);
            transform-origin: center left;
            transform: scaleY(0);
            transition: transform 1s ease-in-out; /* アニメーション効果 */
            z-index: 1;
        } 

        .radiation4 {
            position: absolute;
            width: 100%; /* 大きくする */
            height: 100%; /* 大きくする */
            clip-path: polygon(0% 0%, 0% 100%, 100% 50%); /* 逆扇状に変更 */
            /* 黄色の放射線 */
            background-color: rgba(255, 255, 0, 0.5);
            transform-origin: right;
            transform: scaleY(0);
            transition: transform 1s ease-in-out; /* アニメーション効果 */
            z-index: 1;
        } 


    </style>
</head>
<body>
    <h1>放射線治療を体験しよう！</h1>
    <button onclick="shootRadiation(1)">Shoot Up</button>
    <button onclick="shootRadiation(2)">Shoot Down</button>
    <button onclick="shootRadiation(3)">Shoot Left</button>
    <button onclick="shootRadiation(4)">Shoot Right</button>
    <button onclick="resetGame()">Reset Game</button>
    <p>線源はＸ線　赤い丸はがん細胞　緑の丸は正常細胞</p>
    <div id="game-container">
        <!-- 周囲に正常細胞を均等に配置 -->
        <div class="normal-cell cell" style="top: 75px; left: 0;"></div>
        <div class="normal-cell cell" style="top: 0; left: 75px;"></div>
        <div class="normal-cell cell" style="top: 75px; right: 0;"></div>
        <div class="normal-cell cell" style="top: 0; right: 75px;"></div>
        <div class="normal-cell cell" style="bottom: 75px; left: 0;"></div>
        <div class="normal-cell cell" style="bottom: 0; left: 75px;"></div>
        <div class="normal-cell cell" style="bottom: 75px; right: 0;"></div>
        <div class="normal-cell cell" style="bottom: 0; right: 75px;"></div>
        <!-- 中心にがん細胞を配置 -->
        <div id="cancer-cell" class="cell"></div>
        <!-- 放射線を表示する要素 -->
        <div class="radiation" id="radiation-up"></div>
        <div class="radiation2" id="radiation-down"></div>
        <div class="radiation3" id="radiation-left"></div>
        <div class="radiation4" id="radiation-right"></div>
    </div>

    <script>
        let radiationCount = 0;
        let radiationCount2 = 0;
        let radiationCount3 = 0;
        let radiationCount4 = 0;
        const maxRadiationCount = 8; // 各正常細胞が消える放射線の最大回数
        
        function shootRadiation(direction) {
            // 放射線の要素を取得
            const radiation = document.getElementById(`radiation-up`);
            const radiation2 = document.getElementById(`radiation-down`);
            const radiation3 = document.getElementById(`radiation-left`);
            const radiation4 = document.getElementById(`radiation-right`);
            
            // すべての放射線を非表示にする
            radiation.style.transform = 'scaleY(0)';
            radiation2.style.transform = 'scaleY(0)';
            radiation3.style.transform = 'scaleY(0)';
            radiation4.style.transform = 'scaleY(0)';
            
            // クリックされたボタンに対応する放射線を表示
            if (direction === 1) {
                radiation.style.transform = 'scaleY(1)';
                // 上部の正常細胞だけを薄くする
                document.querySelectorAll('.normal-cell:not([style*="bottom"])').forEach(function(cell) {
                    let opacity = parseFloat(cell.style.opacity || 1);
                    if (opacity > 0) {
                        opacity -= 1 / maxRadiationCount;
                        cell.style.opacity = opacity;
                        // 透明度が一定以下になったら非表示
                        if (opacity <= 0) {
                            cell.style.display = 'none';
                        }
                    }
                });
                radiationCount++;
            } else if (direction === 2) {
                radiation2.style.transform = 'scaleY(1)';
                // 下部の正常細胞だけを薄くする
                document.querySelectorAll('.normal-cell:not([style*="top"])').forEach(function(cell) {
                    let opacity = parseFloat(cell.style.opacity || 1);
                    if (opacity > 0) {
                        opacity -= 1 / maxRadiationCount;
                        cell.style.opacity = opacity;
                        // 透明度が一定以下になったら非表示
                        if (opacity <= 0) {
                            cell.style.display = 'none';
                        }
                    }
                });
                radiationCount2++;
            } else if (direction === 3) {
                radiation3.style.transform = 'scaleY(1)';
                // 左部の正常細胞だけを薄くする
                document.querySelectorAll('.normal-cell:not([style*="right"])').forEach(function(cell) {
                    let opacity = parseFloat(cell.style.opacity || 1);
                    if (opacity > 0) {
                        opacity -= 1 / maxRadiationCount;
                        cell.style.opacity = opacity;
                        // 透明度が一定以下になったら非表示
                        if (opacity <= 0) {
                            cell.style.display = 'none';
                        }
                    }
                });
                radiationCount3++;
            } else if (direction === 4) {
                radiation4.style.transform = 'scaleY(1)';
                // 右部の正常細胞だけを薄くする
                document.querySelectorAll('.normal-cell:not([style*="left"])').forEach(function(cell) {
                    let opacity = parseFloat(cell.style.opacity || 1);
                    if (opacity > 0) {
                        opacity -= 1 / maxRadiationCount;
                        cell.style.opacity = opacity;
                        // 透明度が一定以下になったら非表示
                        if (opacity <= 0) {
                            cell.style.display = 'none';
                        }
                    }
                });
                radiationCount4++;
            }

            const cancerCell = document.getElementById('cancer-cell');
           
            // がん細胞の透明度を設定して薄くする
            let cancerOpacity = parseFloat(cancerCell.style.opacity || 1);
            if (cancerOpacity > 0) {
                cancerOpacity -= 1 / maxRadiationCount;
                cancerCell.style.opacity = cancerOpacity;
                // がん細胞の透明度が一定以下になったら非表示
                if (cancerOpacity <= 0) {
                    cancerCell.style.display = 'none';
                }
            }
        }

        function resetGame() {
            // すべての放射線を非表示にする
            const radiation = document.getElementById('radiation-up');
            const radiation2 = document.getElementById('radiation-down');
            const radiation3 = document.getElementById('radiation-left');
            const radiation4 = document.getElementById('radiation-right');
            radiation.style.transform = 'scaleY(0)';
            radiation2.style.transform = 'scaleY(0)';
            radiation3.style.transform = 'scaleY(0)';
            radiation4.style.transform = 'scaleY(0)';

            // がん細胞を再表示し、透明度をリセットする
            const cancerCell = document.getElementById('cancer-cell');
            cancerCell.style.display = 'block';
            cancerCell.style.opacity = 1;

            // 正常細胞を再表示し、透明度をリセットする
            const normalCells = document.querySelectorAll('.normal-cell');
            normalCells.forEach(function(cell) {
                cell.style.display = 'block';
                cell.style.opacity = 1;
            });

            // 放射線が当たった回数をリセット
            radiationCount = 0;
            radiationCount2 = 0;
            radiationCount3 = 0;
            radiationCount4 = 0;
        }
    </script>
</body>
</html>
