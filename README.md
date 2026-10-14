# 2026_ds3
// 演奏会データ
// 各演奏会を { title: "演奏会名", songs: ["曲名／作曲者"] } の形式で追加
const archive = [
  {
    title: "第1回定期演奏会 1965.01.18",
    songs: [
      "浜辺の歌／成田為三",
      "民謡「木曾節」による小狂想曲／池谷一郎",
      "童謡集「幼き日の思い出」／赤木淳",
      "峠／鈴木静一",
      "山峡／川崎貞利",
      "「五ツ木の子守り歌」による幻想曲／中川信良",
      "カスタネットの娘／マレンゴ",
      "ナポリ／メッツァカーポ",
      "ラノビア／プリエット",
      "ゴンドラ漕ぎの娘／アンゲリス",
      "ラ・コンバルサ／レクオーナ",
      "不思議な夢／サルベッティ",
      "宝曲の舞曲／ムニエル"
    ]
  },
  {
    title: "第2回定期演奏会 1966.01.10",
    songs: [
      "セレナータ第二番／平山英三郎",
      "西域より／川崎貞利",
      "小川のつぶやき／小池正夫",
      "微風／小池正夫",
      "古戦場の秋／小池正夫",
      "そよ風と私／レクオーナ",
      "マラゲーニャ／レクオーナ",
      "愛の誓い／ケンブフェルト",
      "さらば草原／カナロ",
      "ノスタルジー／シルヴェストリ",
      "序曲第1番イ長調／ヴェルキ",
      "幻想曲「村祭り」／ジュリアン",
      "交響詩「エカーブの嘆き」／ラウダス"
    ]
  }

  // ここに残りの演奏会データを同じ形式で追加
];

// HTML要素を取得
const searchInput = document.getElementById("search");
const yearSelect = document.getElementById("year");
const eventsContainer = document.getElementById("events");
const countDisplay = document.getElementById("count");

// 演奏会名から西暦を取得
function getYear(title) {
  const match = title.match(/(?:19|20)\d{2}/);
  return match ? match[0] : "年代不明";
}

// 年代の選択肢を作成
function setupYearOptions() {
  const years = [
    ...new Set(
      archive
        .map(event => getYear(event.title))
        .filter(year => year !== "年代不明")
    )
  ].sort((a, b) => Number(b) - Number(a));

  years.forEach(year => {
    const option = document.createElement("option");
    option.value = year;
    option.textContent = `${year}年`;
    yearSelect.appendChild(option);
  });
}

// 演奏会一覧を表示
function renderEvents() {
  const keyword = searchInput.value.trim().toLocaleLowerCase();
  const selectedYear = yearSelect.value;

  // 検索条件に一致する演奏会を抽出
  const filteredEvents = archive.filter(event => {
    const matchesYear =
      !selectedYear || getYear(event.title) === selectedYear;

    const searchableText = [
      event.title,
      ...event.songs
    ].join(" ").toLocaleLowerCase();

    const matchesKeyword =
      !keyword || searchableText.includes(keyword);

    return matchesYear && matchesKeyword;
  });

  // 件数を更新
  countDisplay.textContent =
    `${filteredEvents.length}件の演奏会（全${archive.length}件）`;

  // 表示内容をリセット
  eventsContainer.replaceChildren();

  // 該当する演奏会がない場合
  if (filteredEvents.length === 0) {
    const message = document.createElement("p");
    message.className = "empty";
    message.textContent =
      "該当する演奏会・曲目が見つかりませんでした。";

    eventsContainer.appendChild(message);
    return;
  }

  // 演奏会ごとに表示を作成
  filteredEvents.forEach(event => {
    const details = document.createElement("details");
    details.className = "event";

    // 演奏会タイトル
    const summary = document.createElement("summary");
    summary.className = "event-title";

    const yearBadge = document.createElement("span");
    yearBadge.className = "year";
    yearBadge.textContent = getYear(event.title);

    summary.appendChild(yearBadge);
    summary.appendChild(document.createTextNode(event.title));

    details.appendChild(summary);

    // 曲目一覧
    const songList = document.createElement("ul");
    songList.className = "songs";

    if (event.songs.length === 0) {
      const item = document.createElement("li");
      item.textContent = "曲目データはありません。";
      songList.appendChild(item);
    } else {
      event.songs.forEach(song => {
        const item = document.createElement("li");
        item.textContent = song;
        songList.appendChild(item);
      });
    }

    details.appendChild(songList);

    // 検索中は該当する演奏会の曲目を開いて表示
    if (keyword) {
      details.open = true;
    }

    eventsContainer.appendChild(details);
  });
}

// イベントを設定
searchInput.addEventListener("input", renderEvents);
yearSelect.addEventListener("change", renderEvents);

// 初期化
setupYearOptions();
renderEvents();
