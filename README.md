const sheetUrls = [
    // 1-й слайд: таблица
    https://docs.google.com/spreadsheets/d/1WhQxRGitgLLuTnhUtk2BVbg8vOaJbtfGV07PIO-2y5w/edit?usp=sharing,
    // 2-й слайд: лидер дня
    https://docs.google.com/spreadsheets/d/1WhQxRGitgLLuTnhUtk2BVbg8vOaJbtfGV07PIO-2y5w/edit?usp=sharing
];

let currentIndex = 0;
let slides = [];

async function loadAllSheets() {
    const container = document.getElementById('slider-content');
    container.innerHTML = '';
    slides = [];

    try {
        for (let i = 0; i < sheetUrls.length; i++) {
            const data = await loadCSV(sheetUrls[i]);
            let content;

            if (i === 0) {
                content = renderTable(data);
            } else {
                content = renderLeaderCard(data);
            }

            const slide = document.createElement('div');
            slide.classList.add('slide');
            slide.appendChild(content);

            container.appendChild(slide);
            slides.push(slide);
        }

        if (slides.length > 0) {
            showSlide(0);
        }
    } catch (error) {
        console.error('Ошибка при загрузке данных:', error);
    }
}

async function loadCSV(url) {
    try {
        const res = await fetch(url + '&t=' + Date.now());
        if (!res.ok) {
            throw new Error(`HTTP ошибка: ${res.status}`);
        }
        const text = await res.text();
        const rows = text.trim().split(/\r?\n/);
        return rows.map(r => r.split(/[,;\t]/));
    } catch (e) {
        console.error('Ошибка загрузки CSV:', e);
        return [['Ошибка загрузки данных']];
    }
}

function renderTable(data) {
    const tbl = document.createElement('table');
    tbl.style.width = '100%';
    tbl.style.borderCollapse = 'collapse';

    data.forEach((row, i) => {
        const tr = document.createElement('tr');

        row.forEach(cell => {
            const el = document.createElement(i === 0 ? 'th' : 'td');
            el.style.padding = '8px';
            el.style.border = '1px solid #ddd';
            el.textContent = cell;
            tr.appendChild(el);
        });

        tbl.appendChild(tr);
    });

    return tbl;
}

function renderLeaderCard(data) {
    if (!data || !data.length) {
        return document.createTextNode('Данные отсутствуют');
    }

    const headers = data[0];
    const rows = data.slice(1);

    // Поиск колонки с очками
    const scoreIndex = headers.findIndex(h => /очк|score/i.test(h));
    if (scoreIndex === -1) {
        return document.createTextNode("Не найдена колонка 'Очки'");
    }

    // Поиск колонки с именем
    const nameIndex = headers.findIndex(h => /имя|name/i.test(h));

    // Поиск лидера
    let leader = rows[0];
    let maxScore = parseFloat(rows[0][scoreIndex]) || 0;

    for (let r of rows) {
        const score = parseFloat(r[scoreIndex]) || 0;
        if (score > maxScore) {
            maxScore = score;
            leader = r;
        }
    }

    // Создание карточки
    const card = document.createElement('div');
    card.classList.add('leader-card');
    card.style.padding = '20px';
    card.style.borderRadius = '8px';
    card.style.boxShadow = '0 2px 5px rgba(0,0,0,0.2)';

    const title = document.createElement('h2');
    title.textContent = 'Лидер дня';

    const player = document.createElement('p');
    player.textContent = `Игрок: ${nameIndex !== -1 ? leader[nameIndex] : 'Неизвестный'}`;

    const score = document.createElement('p');
    score.classList.add('score');
    score.style.fontSize = '24px';
    score.style.color =
