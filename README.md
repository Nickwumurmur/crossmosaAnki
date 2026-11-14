# CrossMosaAnki：會背單字的 CrossMosa

> [!IMPORTANT]
> **這是個人修改版，不是原作者的版本。**
>
> - **原作者的專案**：[anki630/crossmosa](https://github.com/anki630/crossmosa)（CrossMosa，繁體中文閱讀韌體）。下面「CrossMosa」以後的說明都是原作者寫的。
> - **更上游的原始專案**：[crosspoint-reader/crosspoint-reader](https://github.com/crosspoint-reader/crosspoint-reader)（CrossPoint，作者 Dave Allie 與貢獻者們）
> - **這個版本**：[Nickwumurmur/crossmosaAnki](https://github.com/Nickwumurmur/crossmosaAnki)。在 CrossMosa 上加了 **Anki 單字卡**，其他功能與原版相同，會持續同步原作者的更新。
>
> 單字卡的問題請回報到[這個版本](https://github.com/Nickwumurmur/crossmosaAnki/issues)，**不要**回報給原作者。閱讀、字型、刷機的問題，請先看原作者的說明。

**[下載這個版本的韌體](https://github.com/Nickwumurmur/crossmosaAnki/releases/latest)** ·
[單字卡使用教學](#單字卡使用教學) ·
[原作者的 CrossMosa 說明](#crossmosa)

## 這個版本新增了什麼

| 功能 | 說明 |
|---|---|
| **單字卡（Anki 牌組）** | 把 Anki 的 `.apkg` 牌組轉換後放進 SD 卡，就能在閱讀器上翻卡、評分、複習 |
| **四鍵評分** | 前面四顆鍵直接對應 Anki 的「重來／困難／良好／簡單」，按鍵提示會寫下次多久後出現 |
| **SM-2 排程** | 跟 Anki 傳統排程一樣的間隔計算：學習步驟 1 分、10 分，畢業 1 天，凌晨 4 點換日 |
| **進度即時存檔** | 每評一張就寫進 SD 卡，隨時關機都不會掉進度；可以撤銷上一張 |
| **每副牌組各自設定** | 每日新卡數、每日複習上限都能在機器上改 |
| **電腦端轉換程式** | 去掉音檔（幾百 MB 變幾 MB），簡體自動轉臺灣正體 |
| **日文、韓文、音標字型** | 另外產生一套卡面字型，涵蓋繁中、日文假名與漢字、韓文、IPA 音標 |

已針對這幾種牌組調整過卡面，其他牌組也能用，只是排版比較陽春：

- **新标准日本语 · 初級／中級／高級（假名到释义）**
- **实用英语语境智能卡组（美音）基礎詞／常用詞／進階詞**：「讀」「寫」兩種卡各自成一副牌組

只在 **Xteink X3** 實機上測試過。韌體也有編入 X4 的支援，但沒有在 X4 上試過。

<a id="單字卡使用教學"></a>

## 單字卡使用教學

整個流程是：**電腦上轉換牌組 → 產生字型 → 放進 SD 卡 → 刷這個版本的韌體 → 在機器上複習**。
前兩步只要做一次，以後加新牌組只需要重做第 1 步。

### 事前準備

- 一台 **Windows** 電腦（產生字型要用 Windows 內建的字型；轉換牌組 macOS／Linux 也可以）
- **Python 3**：到 [python.org](https://www.python.org/downloads/) 下載安裝，安裝時勾選「Add Python to PATH」
- 這個 repo 的程式碼：按 GitHub 頁面上的 **Code → Download ZIP**，解壓縮到任一個資料夾

打開「命令提示字元」或 PowerShell，切換到解壓縮的資料夾，安裝需要的套件：

```bash
pip install opencc-python-reimplemented freetype-py fonttools
```

### 第 1 步：在電腦上轉換牌組

Anki 的 `.apkg` 是給手機和電腦用的格式（壓縮檔＋資料庫＋音檔），閱讀器讀不了，所以要先轉成 `.xdk`：

```bash
python scripts/anki/apkg2x3.py "D:/我的牌組/新标准日本语 · 初级 (假名到释义) 教材原版发音.apkg" -o out/anki
```

- 可以一次給好幾個檔案，用空白隔開；檔名有空白要加引號
- 輸出在 `out/anki/`，每副（子）牌組一個 `.xdk` 檔，畫面會列出每副有幾張卡
- 英文卡組會變成「讀」「寫」兩個檔，可以只放其中一個
- 選用參數：`--new-per-day 10`（每日新卡數，預設 20）、`--max-reviews 100`（每日複習上限，預設 200）。之後也能在機器上改

> [!NOTE]
> 從 Anki 2.1.50 以後的版本匯出的牌組，如果程式說「新版 Anki 壓縮格式」，請在 Anki 匯出時勾選「**支援舊版 Anki**」再匯出一次。

### 第 2 步：產生卡面字型（只要做一次）

閱讀器內建的字型只有繁體中文。日文假名、韓文和英文音標需要另外一套字型，用這個指令產生：

```bash
python scripts/anki/build_anki_font.py -o out/fonts/AnkiCJK
```

大約一分鐘，會產生 `AnkiCJK_14.cpfont`（內文，約 8 MB）與 `AnkiCJK_22.cpfont`（標題字，約 17 MB）。

> [!WARNING]
> 這套字型是用 Windows 內建的微軟正黑體、游ゴシック、맑은 고딕、Segoe UI 疊出來的，**授權只限自己使用**。
> 所以這裡不提供下載，請每個人在自己的電腦上產生，也不要把產生的檔案公開分享。

沒有這套字型也能用，機器會改用你的閱讀字型，但日文、韓文、音標可能顯示成方塊。

### 第 3 步：放進 SD 卡

用讀卡機（或機器的「檔案傳輸」功能）照這個結構放：

```
SD 卡
├── anki/
│   ├── 新標日·初級.xdk
│   ├── 實用英語·基礎詞·讀.xdk
│   └── ……（第 1 步產生的 .xdk）
└── fonts/
    ├── AnkiCJK/
    │   ├── AnkiCJK_14.cpfont
    │   └── AnkiCJK_22.cpfont
    └── ……（原本就有的閱讀字型）
```

### 第 4 步：刷這個版本的韌體

到這個版本的 [Releases](https://github.com/Nickwumurmur/crossmosaAnki/releases/latest) 下載 `update.bin`。

- **機器上已經是 CrossMosa**：把 `update.bin` 放進 SD 卡，到「設定 → 系統 → SD 卡韌體更新」選它
- **還是原廠韌體**：照原作者的[首次安裝](docs/install.md)步驟刷，只是把檔案換成這裡下載的 `update.bin`

刷完開不了機時，關機後按住左側上一頁鍵再按電源鍵，會進到 SD 卡韌體選擇畫面，可以改刷原作者的正式版救回來（[詳細說明](docs/update.md)）。

### 第 5 步：在機器上複習

首頁下方工具列的「**單字卡**」（Formosa Cover 主題；其他主題在首頁選單裡）。

**牌組清單**

每副牌組底下會顯示今天的「新卡／學習中／待複習」張數。

| 按鍵 | 功能 |
|---|---|
| 左、右（或側邊翻頁鍵） | 選牌組 |
| 確認 | 開始複習 |
| **長按確認** | 牌組設定：每日新卡數、每日複習上限 |
| 返回 | 回首頁 |

第一次打開一副牌組時，機器會在旁邊建一個進度檔，大牌組要等幾秒。

**複習畫面**

前面四顆鍵由左到右是：**返回、確認、左、右**。

| 畫面 | 返回 | 確認 | 左 | 右 | 側邊翻頁鍵 |
|---|---|---|---|---|---|
| 正面（題目） | 離開 | 翻面 | 翻面 | 翻面 | 翻面 |
| 背面（答案） | **重來** | **困難** | **良好** | **簡單** | 答案太長時換頁 |

- **正面長按確認**：撤銷上一張的評分（只能撤一張）
- **背面長按返回**：不評分，直接離開
- 背面的按鍵提示會寫下一次多久後出現，例如「重來 1分」「良好 3天」
- 頂端右邊是今天剩下的張數：新卡、學習中、待複習

**怎麼選評分？**

| 按鍵 | 什麼時候按 | 結果 |
|---|---|---|
| 重來 | 想不起來、答錯 | 幾分鐘後再出一次，複習卡的間隔重新開始 |
| 困難 | 想起來了，但很吃力 | 間隔稍微拉長，以後會比較常出現 |
| 良好 | 正常想起來 | 間隔照正常速度拉長（最常按這個） |
| 簡單 | 一看就知道 | 間隔拉得比較長，以後很少出現 |

學習中的卡片都還沒到期時，會出現「還有學習中的卡片」，按確認可以提早複習，或先離開，晚點再回來。

### 卡面長什麼樣子

| 牌組 | 正面 | 背面 |
|---|---|---|
| 新標日 | 假名、詞性 | 漢字寫法、假名、詞性、釋義、第幾課 |
| 實用英語「讀」 | 單字、音標、用法、一句英文例句 | 中英釋義、例句（附翻譯） |
| 實用英語「寫」 | 詞性、中英釋義、一句中文例句 | 單字、音標、釋義、例句 |
| 其他牌組 | 第一個欄位 | 其餘欄位 |

### 常見問題

<details>
<summary>進度會跟手機或電腦上的 Anki 同步嗎？</summary>

不會。進度只存在 SD 卡的 `.srs` 檔裡，跟其他裝置上的 Anki 完全獨立。

</details>
<details>
<summary>更新牌組（重新轉換）會讓進度歸零嗎？</summary>

不會。只要是同一個來源牌組，重新轉換後覆蓋 `.xdk` 就好，進度照舊。
**不要刪掉機器在 `anki/` 裡建立的 `.srs` 檔**，那就是你的進度。換成不同的牌組時，舊進度會自動改名成 `.srs.bak` 留著。

</details>
<details>
<summary>牌組清單底部寫「時鐘未校正」？</summary>

到期日是照機器的時鐘算的。請到「設定」用 Wi-Fi 校時，之後就會消失。

</details>
<details>
<summary>牌組清單底部寫「沒有 AnkiCJK 字型」？</summary>

SD 卡上找不到第 2 步產生的字型。確認資料夾是 `fonts/AnkiCJK/`，裡面有兩個 `.cpfont` 檔。

</details>
<details>
<summary>日文漢字的字形看起來像中文？</summary>

卡面字型以微軟正黑體為主，所以日文漢字會用繁中字形，例如「直」「骨」的寫法。不影響閱讀，但跟日本教科書的字形有些微差異。

</details>
<details>
<summary>英文常用詞、進階詞的卡片很多，該怎麼設？</summary>

這兩副各有兩萬多張。建議每日新卡數先設 10～20 張，在牌組清單長按確認就能改。新卡學得越多，之後每天要複習的也越多。

</details>

更多細節見 [docs/anki.md](docs/anki.md)。

---
---

<a id="crossmosa"></a>

![CrossMosa — 台灣黑熊與月牙](docs/promo/logo.png)

# CrossMosa

> 以下是原作者 [anki630/crossmosa](https://github.com/anki630/crossmosa) 的說明。裡面的「下載」連結是原作者的正式版，**沒有單字卡功能**；要單字卡請用[這個版本的下載頁](https://github.com/Nickwumurmur/crossmosaAnki/releases/latest)。

**中文書，就該這樣讀。**

為 Xteink X3 與 X4 打造。裝好之後，拿起來就是中文。<br>
零碎時間已經夠少了，不該花在跟機器搏鬥上。

**[下載 X3 版](https://github.com/anki630/crossmosa/releases/latest)** ·
**[下載 X4 版](https://github.com/anki630/crossmosa/releases/latest)** ·
[怎麼安裝？三步驟](#開始使用) ·
[English](#crossmosa-english)

免費開源・機器免接電腦<br>
觸控的 X4 Pro 不適用。

![CrossMosa：書架首頁、明體內文、名畫待機](docs/promo/hero-2026-11.jpg)

## 想讀的書，一眼就找到

首頁換上書的封面。正在讀的書、最近讀過的三本，打開就在眼前，還看得到讀了多久。

卡上的書排在書架上，最近讀的在最前面，不用再一層層打開資料夾。喜歡的書，長按就能加入最愛。沒有封面的書，也會配上一張插畫封面。

| 首頁 | 書架 |
|:---:|:---:|
| <img src="docs/promo/cover-home-photo.jpg" width="300" alt="Formosa Cover 首頁：正在讀的書與最近讀過的三本"> | <img src="docs/promo/cover-shelf-photo.jpg" width="300" alt="Formosa Cover 書架：一頁六本封面"> |

首頁的樣子，可以在「設定 → 顯示 → 介面主題」換。

## 你買它是為了讀中文書，結果打開是這樣

整頁都是方塊。裝好 CrossMosa，選單是中文，書頁清清楚楚。差別，看圖就好。

| 裝好之前 | 裝好之後 |
|:---:|:---:|
| <img src="docs/promo/compare-tofu.jpg" width="300" alt="裝好之前：滿頁方塊字"> | <img src="docs/promo/compare-serif.jpg" width="300" alt="裝好之後：正常顯示的明體內文"> |

## 孩子，也能自己讀

還不認得的字，看旁邊的注音就會念。破音字，也會照詞來標。<br>
睡前、週末、坐車的時候，拿起來就能讀完一本。

沒有遊戲，沒有廣告，也沒有通知。孩子拿起來，就是讀書。

<p align="center"><img src="docs/promo/x3-zhuyin.jpg" width="340" alt="X3 上的注音直排書頁"></p>

注音字型需要 2.1.0 以後的韌體。[下載注音字型 →](https://github.com/anki630/crossmosa/releases/tag/zhuyin-2026-09)

## 換一套字，換一種心情

明體耐讀，黑體清楚，圓體柔和，硬筆楷書像手寫。給孩子有注音，給爸媽有大杯、特大杯。字型放在 SD 卡上，隨時可以換。

| 明體 | 黑體 | 圓體 |
|:---:|:---:|:---:|
| <img src="docs/promo/fonts/serif.png" width="220" alt="明體樣張"><br>長篇首選 | <img src="docs/promo/fonts/sans.png" width="220" alt="黑體樣張"><br>字小也清楚 | <img src="docs/promo/fonts/round.png" width="220" alt="圓體樣張"><br>筆畫圓潤，看久不累 |
| **硬筆楷書** | **注音楷書** | **大杯、特大杯** |
| <img src="docs/promo/fonts/iansui.png" width="220" alt="硬筆楷書樣張"><br>像原子筆寫在紙上 | <img src="docs/promo/fonts/zhuyin.png" width="220" alt="注音楷書樣張"><br>破音字也照詞來標 | <img src="docs/promo/fonts/large.png" width="220" alt="大杯樣張"><br>明體、黑體、圓體、楷書都有 |

<details>
<summary>看直排的樣子</summary>

| 明體 | 黑體 | 圓體 |
|:---:|:---:|:---:|
| <img src="docs/promo/fonts/v-serif.png" width="220" alt="明體直排樣張"> | <img src="docs/promo/fonts/v-sans.png" width="220" alt="黑體直排樣張"> | <img src="docs/promo/fonts/v-round.png" width="220" alt="圓體直排樣張"> |
| **硬筆楷書** | **注音楷書** | **大杯、特大杯** |
| <img src="docs/promo/fonts/v-iansui.png" width="220" alt="硬筆楷書直排樣張"> | <img src="docs/promo/fonts/v-zhuyin.png" width="220" alt="注音楷書直排樣張"> | <img src="docs/promo/fonts/v-large.png" width="220" alt="大杯直排樣張"> |

</details>

樣張是模擬畫面：用機器上的字型檔，照螢幕原本的解析度排出來。範文為朱自清〈背影〉、李白〈靜夜思〉、孟浩然〈春曉〉。

## 零碎時間，拿起來就讀

通勤、午休、睡前。你還在讀這一頁，下一頁已經備好；下次打開，從上次的地方繼續。

<p align="center"><img src="docs/promo/x4-vertical.jpg" width="360" alt="X4 上的直排書頁"></p>

**直排的書，照直排讀。**由上而下、由右而左，像翻一本真正的書。

**註腳，一按就到。** 再按一下就回到原處。譯注多的書，終於好讀了。

**繪本和畫冊，整個螢幕都是畫。** 整頁是圖的書，畫面貼齊螢幕邊緣。

**書，傳過去就好。** 打開瀏覽器，把書拖進去。書都在 Calibre 的話，機器也能直接連上你的書庫。

## 口袋裡的小美術館

闔上的時候，螢幕上是一幅世界名畫。今天梵谷，明天北齋。

<p align="center">
  <img src="docs/promo/wp/great_wave.jpg" width="110" alt="神奈川沖浪裏">
  <img src="docs/promo/wp/girl_pearl_earring.jpg" width="110" alt="戴珍珠耳環的少女">
  <img src="docs/promo/wp/starry_night.jpg" width="110" alt="星夜">
  <img src="docs/promo/wp/wanderer_sea_of_fog.jpg" width="110" alt="霧海上的旅人">
  <img src="docs/promo/wp/mona_lisa.jpg" width="110" alt="蒙娜麗莎">
  <img src="docs/promo/wp/peacock_skirt.jpg" width="110" alt="孔雀裙">
</p>

<p align="center"><a href="https://anki630.github.io/crossmosa/masterpieces.html">每一幅畫的故事 →</a></p>

五十幅畫的故事，也做成了一本電子書，可以放進閱讀器慢慢看。[下載《口袋裡的小美術館》→](https://github.com/anki630/crossmosa/releases/tag/wallpapers-masterpieces-2026-10)

<a id="開始使用"></a>

## 三步驟，今晚就能開始讀

機器你已經有了，SD 卡就插在上面。剩下的只有三步。

**適用機型**：Xteink X3、X4。觸控的 X4 Pro 和其他機型都不適用。

**該下載哪一版？** 下載[最新的正式版](https://github.com/anki630/crossmosa/releases/latest)就好，X3、X4 都能直接刷。想先試還在測試的新功能，可以到[下載頁](https://github.com/anki630/crossmosa/releases)找 Beta 版。

1. **刷機**（只有第一次）：下載 `update.bin`，放進 SD 卡最外層，插回機器。關機後，按住下面兩顆鍵開機，看到載入畫面就放手。等它自己重新開機，就完成了。
   - **X3**：左側的上一頁鍵＋電源鍵
   - **X4**：右側上面那顆鍵＋電源鍵。沒有反應的話，接上 USB 電源再試一次。
2. **放字型**：下載[字型包](https://github.com/anki630/crossmosa/releases/tag/fonts-2026-09)，在 SD 卡最外層建一個 `fonts` 資料夾，把字型包裡的資料夾放進去。沒有字型，書會顯示成方塊。
3. **開始讀**：在剛剛下載韌體的那一頁，下載使用手冊 `CrossMosa.epub`，放進 SD 卡。到「設定 → 閱讀器 → 文字設定 → 字型」選一套字型，打開手冊，一邊讀一邊按。

**以後更新，不用重刷。** 把新版傳進機器，到「設定 → 系統 → SD 卡韌體更新」選它就好，不用再按鍵開機。[詳細步驟](docs/update.md)

<a id="flash-warning"></a>
第一次刷機之前，請先讀[安裝說明](docs/install.md)裡的注意事項。刷機有風險，而且不是每一次都救得回來。

<a id="screen-halted"></a><a id="螢幕停住了救援步驟"></a>

| 我想要 | 看這裡 |
|---|---|
| 第一次安裝 | [首次安裝](docs/install.md) |
| 更新到新版 | [更新到新版](docs/update.md) |
| X3 刷完畫面不動 | [螢幕停住了](docs/rescue.md) |
| 刷不進去 | [刷不進去怎麼辦](docs/flash-troubleshooting.md) |
| 挑字型、裝大杯和特大杯、給孩子的注音 | [字型](docs/fonts.md) |
| 把書放進機器 | [把書放進去](docs/books.md) |
| 換桌布 | [桌布](docs/wallpaper.md) |
| 刷回原廠系統 | [社群整理的步驟](https://pocketink.io/firmware/recovery/)（英文） |

## 猶豫的話，先看這裡

<details>
<summary>支援哪些機器？</summary>

Xteink X3 和 X4。觸控的 X4 Pro 不支援。

</details>
<details>
<summary>每次更新都要重刷嗎？</summary>

不用。只有第一次要按鍵刷機。之後出新版，用手機的瀏覽器把它傳進機器，到「設定 → 系統 → SD 卡韌體更新」選它就好，全程不用接線。

</details>
<details>
<summary>不太會用電腦，也裝得起來嗎？</summary>

裝得起來。用電腦或手機把檔案放進 SD 卡，再按住兩顆鍵開機就好，機器不用接線。

</details>
<details>
<summary>會把機器刷壞嗎？</summary>

刷機有風險，而且不是每一次都救得回來。開始之前，請先讀完[安裝說明](docs/install.md)裡的注意事項。

</details>
<details>
<summary>可以刷回原廠系統嗎？</summary>

可以。社群整理了[刷回原廠的步驟](https://pocketink.io/firmware/recovery/)（英文）：機器接得上電腦的話，用 CrossPoint 的刷機網站；USB 被鎖住的 X3，改用 SD 卡和原廠的更新檔。

</details>
<details>
<summary>要錢嗎？</summary>

免費，開源。

</details>
<details>
<summary>書從哪裡來？</summary>

CrossMosa 不提供書。請向正版管道購買沒有 DRM 的電子書，再用瀏覽器傳進機器。有 DRM 的書，這台機器讀不了。

</details>
<details>
<summary>跟原版 CrossPoint 差在哪？</summary>

CrossPoint 是很好的開源起點。CrossMosa 在它上面，把中文閱讀做完整。

</details>

## 為什麼會有它

我是電子書閱讀器的愛用者。有工作、有小孩，能讀書的時間都是擠出來的零碎片段——通勤的路上、孩子睡著後的半小時。X3 這種放得進口袋的機器，就是為這些片段準備的。

但買來才發現，它不是為中文讀者做的：選單是英文，中文書打開是滿頁方塊。零碎時間已經夠少了，不該再花在跟機器搏鬥上。

所以我從開源的 CrossPoint 出發，為自己改了一版。一版一版地改，改到我覺得可以拿出來了。

它叫 CrossMosa：Cross 來自它的起點，Mosa 來自 Formosa。開機畫面上那隻是台灣黑熊，牠頭上有一彎月牙。

如果你的閱讀時間也是擠出來的，這一版就是為你調的。

—— [anki630](https://github.com/anki630)，CrossMosa 維護者

## 如果它讓你的閱讀器變好用了

推薦給也有 X3、X4 的朋友；到[討論區](../../discussions)說說你讀了什麼書；或請維護者喝杯咖啡（連結籌備中）。
缺字或遇到問題，歡迎[回報](../../issues)。

**[下載 X3 版](https://github.com/anki630/crossmosa/releases/latest)** ·
**[下載 X4 版](https://github.com/anki630/crossmosa/releases/latest)**

**接下來，把零碎時間還給書。**

---

## 給開發者

- [自行建置、與原版 CrossPoint 的差異、介面字型怎麼做](docs/developers.md)
- [變更紀錄](CHANGELOG.md)
- [第三方元件與授權](NOTICE.md)

CrossMosa 建立在 [CrossPoint](https://github.com/crosspoint-reader/crosspoint-reader) 上面，原版作者是 Dave Allie 與 CrossPoint 的貢獻者們。原版的問題，請回報到[原版的專案](https://github.com/crosspoint-reader/crosspoint-reader/issues)。

## 免責聲明

- 本專案不提供任何書籍內容，也不內建任何書源。請從正版管道取得沒有 DRM 的電子書，支持正版，尊重創作者。
- 刷機有風險，風險自負。已經有機器變磚、照著救援步驟也沒救回來的案例。開始之前，請先假設它可能救不回來，詳見[安裝說明](docs/install.md)。
- 與 Xteink 無關，與原版 CrossPoint 專案也沒有隸屬關係。
- 測試用的是維護者自己的 X3 與 X4，沒有自動化的硬體測試。
- 沒有遙測。設定、閱讀進度和書籤只存在你的 SD 卡上。機器不會主動連到本專案或原版的伺服器，只有在你要求時才會連網，例如連上 Wi-Fi 後對時、連你設定的書庫。
- 依現狀提供，不附任何擔保，詳見 `LICENSE`。

## 授權

- 原版 CrossPoint：MIT，Copyright (c) 2025 Dave Allie（`LICENSE` 原封保留）。
- CrossMosa 的修改：MIT，Copyright (c) 2026 CrossMosa contributors。
- 內含的第三方程式庫與字型各有授權，完整清單見 [`NOTICE.md`](NOTICE.md)。其中有 GPLv2 與 LGPL-2.1 的元件會一起編進發佈的韌體，要再散布的話，請先讀 NOTICE 的「發佈義務」。

維護：CrossMosa contributors。

---
---

![CrossMosa — a Formosan black bear and a crescent moon](docs/promo/logo.png)

# CrossMosa (English)

**Chinese books, the way they should be read.**

Made for the Xteink X3 and X4. Install it, and the whole device speaks Chinese.<br>
Reading time is short enough. It shouldn't go to fighting the device.

**[Download for X3](https://github.com/anki630/crossmosa/releases/latest)** ·
**[Download for X4](https://github.com/anki630/crossmosa/releases/latest)** ·
[How to install: three steps](#getting-started)

Free and open source · No computer connection needed<br>
Not for the touchscreen X4 Pro.

![CrossMosa: a bookshelf home screen, serif body text, and a painting on the sleep screen](docs/promo/hero-2026-11.jpg)

## Find the book you want, at a glance

The home screen shows your covers. The book you're reading and the last three you opened are right there when you pick it up, along with how long you've been reading.

Your books sit in one library, most recent first. No more opening folder after folder. Hold a book to add it to Favorites. Books without a cover get an illustrated one.

| Home | Library |
|:---:|:---:|
| <img src="docs/promo/cover-home-photo.jpg" width="300" alt="Formosa Cover home: the current book and the last three"> | <img src="docs/promo/cover-shelf-photo.jpg" width="300" alt="Formosa Cover library: six covers per page"> |

You can change how the home screen looks in Settings → Display → UI Theme.

## You bought it to read Chinese books. Then you opened one.

A whole page of boxes. With CrossMosa, the menus are in Chinese and every page is clear. The pictures say the rest.

| Before | After |
|:---:|:---:|
| <img src="docs/promo/compare-tofu.jpg" width="300" alt="Before: a page full of boxes"> | <img src="docs/promo/compare-serif.jpg" width="300" alt="After: serif Chinese text displayed properly"> |

## Children can read on their own

Zhuyin sits beside every character, so new words can be sounded out. Characters with more than one reading get the one that fits the word.<br>
At bedtime, on weekends, in the car: pick it up and read a whole book.

No games, no ads, no notifications. When children pick it up, they read.

<p align="center"><img src="docs/promo/x3-zhuyin.jpg" width="340" alt="An X3 showing a vertical page with zhuyin"></p>

The zhuyin font needs firmware 2.1.0 or later. [Get the zhuyin font →](https://github.com/anki630/crossmosa/releases/tag/zhuyin-2026-09)

## A new typeface, a new mood

Serif for long reads, sans for clarity, rounded for comfort, handwritten for warmth. Zhuyin for children, Large and Extra Large for parents. The fonts live on the SD card, so you can switch anytime.

| Serif | Sans serif | Rounded |
|:---:|:---:|:---:|
| <img src="docs/promo/fonts/serif.png" width="220" alt="Serif sample"><br>Made for long reads | <img src="docs/promo/fonts/sans.png" width="220" alt="Sans-serif sample"><br>Clear even when small | <img src="docs/promo/fonts/round.png" width="220" alt="Rounded sample"><br>Soft strokes, easy on the eyes |
| **Handwritten** | **Zhuyin** | **Large and Extra Large** |
| <img src="docs/promo/fonts/iansui.png" width="220" alt="Handwritten sample"><br>Like ballpoint pen on paper | <img src="docs/promo/fonts/zhuyin.png" width="220" alt="Zhuyin sample"><br>Readings that fit the word | <img src="docs/promo/fonts/large.png" width="220" alt="Large sample"><br>In serif, sans serif, rounded, and handwritten |

<details>
<summary>See them in vertical text</summary>

| Serif | Sans serif | Rounded |
|:---:|:---:|:---:|
| <img src="docs/promo/fonts/v-serif.png" width="220" alt="Serif sample, vertical"> | <img src="docs/promo/fonts/v-sans.png" width="220" alt="Sans-serif sample, vertical"> | <img src="docs/promo/fonts/v-round.png" width="220" alt="Rounded sample, vertical"> |
| **Handwritten** | **Zhuyin** | **Large and Extra Large** |
| <img src="docs/promo/fonts/v-iansui.png" width="220" alt="Handwritten sample, vertical"> | <img src="docs/promo/fonts/v-zhuyin.png" width="220" alt="Zhuyin sample, vertical"> | <img src="docs/promo/fonts/v-large.png" width="220" alt="Large sample, vertical"> |

</details>

The samples are simulated: the device's own font files, laid out at the screen's native resolution. Texts: Zhu Ziqing's "Back View", Li Bai's "Quiet Night Thoughts", and Meng Haoran's "Spring Morning".

## Spare moments, ready to read

On the commute, at lunch, before bed. While you read this page, the next one is already prepared. Open it later and pick up where you left off.

<p align="center"><img src="docs/promo/x4-vertical.jpg" width="360" alt="An X4 showing a vertical page"></p>

**Vertical books, read vertically.** Top to bottom, right to left, like turning the pages of a real book.

**Footnotes, one press away.** Press again to return to where you were. Books full of translator's notes are finally easy to read.

**Picture books, edge to edge.** Pages that are all picture fill the whole screen.

**Books, just send them over.** Open a browser and drag them in. If your books are in Calibre, the device can connect to your library directly.

## A small museum in your pocket

When you close it, the screen shows a famous painting. Van Gogh today, Hokusai tomorrow.

<p align="center">
  <img src="docs/promo/wp/great_wave.jpg" width="110" alt="The Great Wave off Kanagawa">
  <img src="docs/promo/wp/girl_pearl_earring.jpg" width="110" alt="Girl with a Pearl Earring">
  <img src="docs/promo/wp/starry_night.jpg" width="110" alt="The Starry Night">
  <img src="docs/promo/wp/wanderer_sea_of_fog.jpg" width="110" alt="Wanderer above the Sea of Fog">
  <img src="docs/promo/wp/mona_lisa.jpg" width="110" alt="Mona Lisa">
  <img src="docs/promo/wp/peacock_skirt.jpg" width="110" alt="The Peacock Skirt">
</p>

<p align="center"><a href="https://anki630.github.io/crossmosa/masterpieces.html">See all fifty paintings →</a></p>

The stories of all fifty paintings are also an e-book (in Chinese) you can read on the device. [Get the e-book →](https://github.com/anki630/crossmosa/releases/tag/wallpapers-masterpieces-2026-10)

<a id="getting-started"></a>

## Three steps. Start reading tonight.

You already have the device, and the SD card is already in it. Three steps are all that's left.

**Compatible devices:** Xteink X3 and X4. The touchscreen X4 Pro and other devices are not supported.

**Which version should I download?** Just get the [latest release](https://github.com/anki630/crossmosa/releases/latest); it works on both the X3 and X4. To try new features that are still being tested, look for the beta on the [releases page](https://github.com/anki630/crossmosa/releases).

1. **Flash** (first time only): Download `update.bin`, put it in the root of the SD card, and put the card back. Turn the device off, then hold the two keys below while powering it on, and let go when the loading screen appears. It restarts by itself when it's done.
   - **X3:** the previous-page key on the left edge + the power button
   - **X4:** the upper key on the right edge + the power button. If nothing happens, connect USB power and try again.
2. **Add fonts:** Download the [font pack](https://github.com/anki630/crossmosa/releases/tag/fonts-2026-09). Create a `fonts` folder in the root of the SD card and put the folders from the pack inside it. Without fonts, books show up as boxes.
3. **Start reading:** The device starts in Traditional Chinese. To switch to English, go to 設定 → 系統 → 語言 → English. Choose a font under Settings → Reader → Text Settings → Font. The release page also has a manual, `CrossMosa.epub`. It is in Chinese, but its pictures show the main steps, so you can follow along.

**Later updates don't need a reflash.** Copy the new `update.bin` to the card, from a phone or computer browser with the device's File Transfer page or with a card reader, then go to Settings → System → SD Card Firmware Update and pick the file. No key combination. [Detailed steps](docs/update.md#updating-english)

Before your first flash, read the precautions in the [install guide](docs/install.md#install-english). Flashing carries risks, and recovery is not always possible.

| I want to… | Go to |
|---|---|
| Install CrossMosa for the first time | [Install](docs/install.md#install-english) |
| Update CrossMosa | [Updating](docs/update.md#updating-english) |
| Fix an X3 screen that stopped updating | [Screen stopped updating](docs/rescue.md#screen-stopped-updating-rescue-english) |
| Fix a flash that won't go through | [Flash troubleshooting](docs/flash-troubleshooting.md#flash-troubleshooting-english) |
| Choose fonts, install Large or Extra Large, or add zhuyin | [Fonts](docs/fonts.md#fonts-english) |
| Check why characters are missing | [Missing characters](docs/fonts.md#character-set-limits-english) |
| Add books | [Adding books](docs/books.md#adding-books-english) |
| Change the wallpaper | [Wallpapers](docs/wallpaper.md#wallpapers-english) |
| Restore the stock firmware | [Community recovery guide](https://pocketink.io/firmware/recovery/) |

## Still deciding?

<details>
<summary>Which devices are supported?</summary>

The Xteink X3 and X4. The touchscreen X4 Pro is not supported.

</details>
<details>
<summary>Do I have to reflash for every update?</summary>

No. Only the first install uses the key combination. When a new version comes out, send it to the device from your phone's browser, then go to Settings → System → SD Card Firmware Update and pick it. No cable needed.

</details>
<details>
<summary>I'm not good with computers. Can I still install it?</summary>

Yes. Put the files on the SD card with a computer or a phone, then hold two keys while turning the device on. The device never needs to be connected to anything.

</details>
<details>
<summary>Can flashing break my device?</summary>

Flashing carries risks, and recovery is not always possible. Before you begin, read the precautions in the [install guide](docs/install.md#install-english).

</details>
<details>
<summary>Can I go back to the stock firmware?</summary>

Yes. The community has a [guide to restoring the stock firmware](https://pocketink.io/firmware/recovery/). If your device connects to a computer, use CrossPoint's web flasher; on an X3 with locked USB, use the SD card and the stock update file.

</details>
<details>
<summary>Does it cost anything?</summary>

It's free and open source.

</details>
<details>
<summary>Where do the books come from?</summary>

CrossMosa doesn't provide books. Buy DRM-free ebooks from legitimate stores, then send them to the device from a browser. Books with DRM can't be read on this device.

</details>
<details>
<summary>How is it different from the original CrossPoint?</summary>

CrossPoint is a great open-source starting point. CrossMosa builds on it and makes Chinese reading complete.

</details>

## Why it exists

I love e-readers. Between work and kids, my reading time comes in scraps: the commute, the half hour after the kids fall asleep. A pocket-sized device like the X3 is made for those moments.

Then I bought one and found it wasn't made for Chinese readers. The menus were in English, and Chinese books opened as pages of boxes. Reading time is short enough. It shouldn't go to fighting the device.

So I started from the open-source CrossPoint and made a version for myself. I kept refining it, one version after another, until it felt ready to share.

It's called CrossMosa: Cross from where it started, Mosa from Formosa. The animal on the startup screen is a Formosan black bear, with a crescent moon above it.

If your reading time also comes in scraps, this was made for you.

— [anki630](https://github.com/anki630), CrossMosa maintainer

## If it made your e-reader better

Tell a friend who has an X3 or X4; tell me what you're reading in [Discussions](../../discussions); or buy the maintainer a coffee (link coming).
Missing characters or other problems are welcome as [issues](../../issues).

**[Download for X3](https://github.com/anki630/crossmosa/releases/latest)** ·
**[Download for X4](https://github.com/anki630/crossmosa/releases/latest)**

**Now give your spare moments back to books.**

---

## For developers

- [Build instructions, differences from CrossPoint, and interface font details](docs/developers.md#for-developers-english)
- [Changelog](CHANGELOG.md)
- [Third-party components and licenses](NOTICE.md)

CrossMosa is built on [CrossPoint](https://github.com/crosspoint-reader/crosspoint-reader) by Dave Allie and the CrossPoint contributors. Report upstream issues to the [CrossPoint project](https://github.com/crosspoint-reader/crosspoint-reader/issues).

## Disclaimer

- This project provides no book content and includes no book sources. Obtain DRM-free ebooks through legitimate channels. Support legitimate publishing and respect creators.
- Flashing carries risks, which you accept yourself. Devices have been bricked, and some could not be recovered even with the rescue steps. Before you begin, assume recovery may not be possible. See the [install guide](docs/install.md#install-english).
- This project is not affiliated with Xteink and is not part of the upstream CrossPoint project.
- Testing is done on the maintainer's own X3 and X4. There is no automated hardware testing.
- There is no telemetry. Settings, reading progress, and bookmarks stay on your SD card. The device never contacts this project's or the upstream project's servers on its own; it goes online only when you ask it to, for example to set the clock after joining Wi-Fi or to reach a library you configured.
- Provided as is, without warranty. See `LICENSE`.

## License

- Upstream CrossPoint: MIT, Copyright (c) 2025 Dave Allie. Its `LICENSE` is kept unchanged.
- CrossMosa modifications: MIT, Copyright (c) 2026 CrossMosa contributors.
- Bundled third-party libraries and fonts have their own licenses; the complete list is in [`NOTICE.md`](NOTICE.md). Components under GPLv2 and LGPL-2.1 are built into the released firmware. Before redistributing it, read section 2 of NOTICE (distribution obligations; the file is in Chinese).

Maintained by CrossMosa contributors.
