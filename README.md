---
layout: default
---
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>망각전야 랜덤 팀 편성기</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@400;700&display=swap');
        :root { --chaos: #ffd700; --deep: #00f2ff; --blood: #ff3c3c; --hyper: #da70d6; }
        
        body {
            /* 경로 수정: '프로그램/' 제거 */
            background: url('image_dec78e.jpg') no-repeat center center fixed;
            background-size: cover;
            color: white; font-family: 'Noto Sans KR', sans-serif; margin: 0; padding: 0;
            display: flex; flex-direction: column; align-items: center; min-height: 100vh;
        }
        body::before {
            content: ""; position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.75); z-index: -1;
        }

        header { text-align: center; padding: 30px 20px; }
        .logo { max-width: 300px; margin-bottom: 10px; filter: drop-shadow(0 0 10px rgba(255,255,255,0.3)); }

        .container {
            width: 90%; max-width: 1000px; background: rgba(20, 20, 20, 0.9);
            border-radius: 20px; padding: 30px; margin-bottom: 50px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.8); border: 1px solid #333;
        }

        .option-section {
            background: #222; border: 2px solid #444; border-radius: 12px;
            padding: 15px; margin-bottom: 25px; text-align: center;
        }
        .option-group { display: flex; justify-content: center; gap: 20px; margin-top: 10px; }
        .option-group label { cursor: pointer; font-size: 1.1rem; display: flex; align-items: center; gap: 8px; }

        h3 { border-left: 4px solid #ffd700; padding-left: 15px; margin-bottom: 20px; }
        
        .faction-section { margin-bottom: 25px; border: 1px solid #444; border-radius: 12px; overflow: hidden; }
        .faction-head { 
            background: #2a2a2a; padding: 12px 20px; 
            display: flex; justify-content: space-between; align-items: center; 
        }
        .faction-info { display: flex; align-items: center; gap: 10px; font-size: 1.2rem; font-weight: bold; }
        .faction-icon-small { width: 24px; height: 24px; object-fit: contain; }
        
        .btn-toggle-group { display: flex; gap: 8px; }
        .btn-toggle { 
            background: #444; color: white; border: none; padding: 5px 10px; 
            border-radius: 4px; cursor: pointer; font-size: 0.8rem; transition: 0.2s;
        }
        .btn-toggle:hover { background: #666; }

        .grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(130px, 1fr)); gap: 10px; padding: 15px; background: #151515; }
        .char-item { font-size: 0.85rem; cursor: pointer; display: flex; align-items: center; gap: 8px; padding: 5px; border-radius: 5px; }

        .btn-gen {
            display: block; width: 280px; padding: 18px; margin: 30px auto;
            background: linear-gradient(135deg, #ffd700, #ff8c00);
            border: none; border-radius: 50px; font-weight: bold; font-size: 1.3rem;
            cursor: pointer; box-shadow: 0 5px 20px rgba(255,215,0,0.3); transition: 0.3s;
        }
        .btn-gen:hover { transform: translateY(-3px); box-shadow: 0 10px 30px rgba(255,215,0,0.5); }

        .result-area { display: flex; justify-content: center; gap: 15px; flex-wrap: wrap; margin-top: 30px; }
        .card { 
            width: 170px; background: #222; border-radius: 15px; overflow: hidden; 
            border-bottom: 5px solid #444; text-align: center; padding-bottom: 12px;
            transition: 0.3s; box-shadow: 0 8px 20px rgba(0,0,0,0.5);
        }
        .card img.profile { width: 100%; height: 230px; object-fit: cover; border-bottom: 1px solid #333; }
        .card-name { font-weight: bold; font-size: 1rem; margin-top: 10px; margin-bottom: 5px; }
        .card-faction { font-size: 0.8rem; display: flex; align-items: center; justify-content: center; gap: 5px; opacity: 0.8; }
        .faction-icon-card { width: 16px; height: 16px; object-fit: contain; }

        .card-chaos { border-color: var(--chaos); } .card-deep { border-color: var(--deep); } 
        .card-blood { border-color: var(--blood); } .card-hyper { border-color: var(--hyper); }
        .text-chaos { color: var(--chaos); } .text-deep { color: var(--deep); }
        .text-blood { color: var(--blood); } .text-hyper { color: var(--hyper); }
    </style>
</head>
<body>

<header>
    <img src="image_decaf2.png" alt="MORIMENS LOGO" class="logo">
    <p style="opacity:0.7">나만의 최적화된 조합을 확인하세요</p>
</header>

<div class="container">
    <div class="option-section">
        <strong>⚙️ 팀 편성 방식 선택</strong>
        <div class="option-group">
            <label><input type="radio" name="mix_type" value="1" checked> 1개 계역</label>
            <label><input type="radio" name="mix_type" value="2"> 2개 계역</label>
            <label><input type="radio" name="mix_type" value="0"> 랜덤</label>
        </div>
    </div>

    <h3>🛡️ 보유 각성체 설정</h3>
    <div id="settings-box"></div>

    <button class="btn-gen" onclick="makeTeam()">팀 편성 시작</button>

    <div id="result" class="result-area"></div>
</div>

<script>
    // 모든 이미지 파일명에서 '프로그램/' 접두사 제거
    const characterData = {
        '혼돈': { icon: 'icon_chaos.png', cls: 'card-chaos', txt: 'text-chaos',
            list: [
                {n:'회귀•라모나', i:'image_dd08da.png'}, {n:'융해•돌', i:'image_dd0c24.png'}, {n:'라이커', i:'image_dd0cdf.png'}, {n:'24', i:'image_dd0fe0.png'},
                {n:'노틸라', i:'image_dd103c.png'}, {n:'님피아', i:'image_dd1098.png'}, {n:'판디아', i:'image_dd10db.png'}, {n:'릴리', i:'image_dd13a5.png'},
                {n:'엘바', i:'image_dd13fc.png'}, {n:'카렌', i:'image_dd145e.png'}, {n:'타비', i:'image_dd6a98.png'}, {n:'카티구라', i:'image_dd6ad1.png'},
                {n:'모샤', i:'image_dd6d9a.png'}, {n:'하멜른', i:'image_dd6dd8.png'}, {n:'라모나', i:'image_dd6e33.png'}, 
                {n:'돌', i:'image_dd6e72.png'}, {n:'오지에', i:'image_dd6e98.png'}, {n:'로탄', i:'image_dd715b.png'}
            ]
        },
        '심해': { icon: 'icon_deepsea.png', cls: 'card-deep', txt: 'text-deep',
            list: [
                {n:'탄망•머피', i:'image_dd7270.png'}, {n:'툴루', i:'image_dd7522.png'}, {n:'머피', i:'image_dd7577.png'}, {n:'파로스', i:'image_dd75db.png'},
                {n:'카이커', i:'image_dd7633.png'}, {n:'골리아', i:'image_dd791a.png'}, {n:'오레타', i:'image_dd795f.png'}, {n:'미리암', i:'image_dd799c.png'},
                {n:'산', i:'image_dd79da.png'}, {n:'셀레스트', i:'image_dd7cbb.png'}, {n:'코퍼산트', i:'image_dd845b.png'}, {n:'모스', i:'image_dd84a1.png'}
            ]
        },
        '혈육': { icon: 'icon_blood.png', cls: 'card-blood', txt: 'text-blood',
            list: [
                {n:'혈쇄•시로', i:'image_dd88d6.png'}, {n:'타이스', i:'image_dd8c1f.png'}, {n:'살바도르', i:'image_dd8c59.png'}, {n:'아이기스', i:'image_dd8c7d.png'},
                {n:'소렐', i:'image_dd8c9b.png'}, {n:'시로', i:'image_dd8cbb.png'}, {n:'아그리파', i:'image_ddde3a.png'}, {n:'우부하시', i:'image_ddde7a.png'},
                {n:'레이아', i:'image_dddeb6.png'}, {n:'파인트', i:'image_ddded9.png'}, {n:'도어세인', i:'image_ddee54.png'}, {n:'픽맨', i:'image_ddf102.png'}, {n:'서', i:'image_ddf13f.png'}
            ]
        },
        '초차원': { icon: 'icon_hyper.png', cls: 'card-hyper', txt: 'text-hyper',
            list: [
                {n:'리즈', i:'image_ddf5b8.png'}, {n:'다포닐', i:'image_ddf89b.png'}, {n:'틴커트', i:'image_ddf8bb.png'}, {n:'완다', i:'image_ddf8d9.png'},
                {n:'웬코르', i:'image_ddf8fd.png'}, {n:'오를라', i:'image_ddf91e.png'}, {n:'젠킨', i:'image_ddf93e.png'}, {n:'에리카', i:'image_ddf95e.png'},
                {n:'카시아', i:'image_ddf998.png'}, {n:'카스토르', i:'image_ddfc45.png'}, {n:'클레멘타인', i:'image_ddfd1b.png'}, {n:'풀룩스', i:'image_ddfd39.png'}
            ]
        }
    };

    const settingsBox = document.getElementById('settings-box');
    for (const f in characterData) {
        const config = characterData[f];
        let section = `
            <div class="faction-section">
                <div class="faction-head">
                    <div class="faction-info ${config.txt}">
                        <img src="${config.icon}" class="faction-icon-small"> ${f}
                    </div>
                    <div class="btn-toggle-group">
                        <button class="btn-toggle" onclick="toggleAll('${f}', true)">전체 활성화</button>
                        <button class="btn-toggle" onclick="toggleAll('${f}', false)">전체 비활성화</button>
                    </div>
                </div>
                <div class="grid" id="grid-${f}">`;
        
        config.list.forEach(c => {
            section += `<label class="char-item"><input type="checkbox" class="chk chk-${f}" data-f="${f}" data-n="${c.n}" data-i="${c.i}" checked> ${c.n}</label>`;
        });
        section += `</div></div>`;
        settingsBox.innerHTML += section;
    }

    function toggleAll(faction, state) {
        document.querySelectorAll(`.chk-${faction}`).forEach(el => el.checked = state);
    }

    function makeTeam() {
        const mixType = document.querySelector('input[name="mix_type"]:checked').value;
        const availableFactions = [];
        for (const f in characterData) {
            if (document.querySelectorAll(`.chk-${f}:checked`).length > 0) availableFactions.push(f);
        }

        if (availableFactions.length === 0) return alert("캐릭터를 최소 한 명 이상 선택해주세요.");

        let numFactionsToPick = parseInt(mixType);
        if (numFactionsToPick === 0) numFactionsToPick = Math.random() < 0.5 ? 1 : 2;
        numFactionsToPick = Math.min(numFactionsToPick, availableFactions.length);

        const pickedFactions = [];
        const tempFactions = [...availableFactions];
        for (let i = 0; i < numFactionsToPick; i++) {
            pickedFactions.push(tempFactions.splice(Math.floor(Math.random() * tempFactions.length), 1)[0]);
        }

        let candidatePool = [];
        pickedFactions.forEach(f => {
            const checkedInFaction = Array.from(document.querySelectorAll(`.chk-${f}:checked`)).map(el => ({
                n: el.dataset.n, f: el.dataset.f, i: el.dataset.i
            }));
            candidatePool.push(...checkedInFaction);
        });

        if (candidatePool.length < 4) return alert("선택된 계역들에 보유한 캐릭터가 4명 미만입니다.");

        let team = [];
        let attempts = 0;
        while (team.length < 4 && attempts < 1000) {
            let pick = candidatePool[Math.floor(Math.random() * candidatePool.length)];
            if (!team.some(t => t.n === pick.n)) {
                // 라모나 중복 방지 (회귀•라모나 포함)
                if (pick.n.includes('라모나') && team.some(t => t.n.includes('라모나'))) { attempts++; continue; }
                team.push(pick);
            }
            attempts++;
        }

        const resDiv = document.getElementById('result');
        resDiv.innerHTML = '';
        team.forEach(t => {
            const config = characterData[t.f];
            resDiv.innerHTML += `
                <div class="card ${config.cls}">
                    <img src="${t.i}" class="profile" onerror="this.src='https://via.placeholder.com/180x240?text=No+Image'">
                    <div class="card-info">
                        <div class="card-name">${t.n}</div>
                        <div class="card-faction ${config.txt}">
                            <img src="${config.icon}" class="faction-icon-card"> ${t.f}
                        </div>
                    </div>
                </div>`;
        });
    }
</script>
</body>
</html>
