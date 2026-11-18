<a id="top"></a>

# SKINGEN — Minecraft Skin Generator

画像から Minecraft のスキン（64×64 / 128×128 / 256×256）を生成するツールです。
ブラウザだけで動く `index.html` と、CLI の `skin_gen.py` の 2 つが入っています。

**日本語** | [English](#english)

---

## できること

- 全身が写った正面写真を、スキンの前面（頭・胴・腕・脚）にそのまま貼る
- 側面・上面・底面は前面の端のピクセルを引き伸ばして自動生成
- 背面は別画像を指定、または正面を少し暗くして自動生成
- 顔写真モード（顔だけを頭に貼り、体はぼかした色で塗る）
- ブラウザ版のみ「顔 + 体」モード（顔と体を別画像で指定）
- Steve（標準腕 4px）/ Alex（細腕 3px）対応
- 重ね着レイヤー（第 2 レイヤー）は透明のまま出力 → そのままアップロード可能
- ドット絵向けの最近傍補間オプション

## ファイル構成

```
SKINGEN/
├── index.html     # ブラウザ版（1ファイル完結・オフライン動作）
├── skin_gen.py    # CLI版（Python 3 + Pillow）
├── 1.jpg          # サンプル入力画像
└── 1_skin.png     # サンプル出力（128×128）
```

---

## ブラウザ版の使い方

1. `index.html` をダブルクリックしてブラウザで開く（サーバ不要・ネット接続不要）
2. **Front image** の枠に画像をクリックで選択、またはドラッグ & ドロップ
3. 設定を変えると即座にプレビューが更新される
4. **Download skin (PNG)** で `<元のファイル名>_skin.png` を保存
5. Minecraft に適用する

### モード

| モード | 動作 |
| --- | --- |
| Whole body photo | 全身写真をスキン全体に貼る（基本） |
| Face only | 顔を頭の前面に貼り、体はその画像を強くぼかした色で塗る |
| Face + body | 顔画像を頭に、体画像を胴・腕・脚に貼る（体画像が未指定なら顔のぼかしで代用） |

### 設定項目

| 項目 | 説明 |
| --- | --- |
| Size | 64×64（Java）/ 128×128 / 256×256 |
| Fit | `contain`（余白あり・全体を収める）/ `cover`（はみ出しを中央で切り取り）/ `stretch`（縦横比無視で引き伸ばし） |
| Slim arms (Alex) | 腕を 3px 幅にする。オフで Steve（4px） |
| Pixel art | 縮小時に補間せず最近傍で縮小（ドット絵向け） |
| Transparent margins | 余白を透明にする。オフにすると右のカラーピッカーの色で塗る |

`Face only` のときは Fit / 背景色 / 背面画像は使いません（自動で無効化されます）。

### 適用方法

- **Java Edition**: Launcher → Skins → New skin → ダウンロードした PNG を選択（サイズは 64×64）
- **Bedrock Edition**: ランチャーのスキン画面、またはゲーム内のキャラクター作成から PNG をインポート（64 / 128 / 256 可）

---

## CLI版の使い方

### 準備

```bash
pip install pillow
```

### 実行

```bash
# 基本: 128x128 で <入力名>_skin.png を出力
python skin_gen.py input.png

# Java 用の 64x64、出力名を指定
python skin_gen.py input.png -s 64 -o myskin.png

# 背面画像あり + 細腕(Alex)
python skin_gen.py front.png --back back.png --slim

# 顔写真モード
python skin_gen.py face.jpg --face

# ドット絵風 + 白背景 + cover
python skin_gen.py input.png --pixel --bg "#ffffff" --fit cover
```

### オプション

| オプション | 既定 | 説明 |
| --- | --- | --- |
| `input` | — | 正面の全身画像（必須） |
| `--back IMG` | なし | 背面画像。省略時は正面を 0.85 倍に暗くして背面に使用 |
| `-o, --output PATH` | `<入力名>_skin.png` | 出力先 |
| `-s, --size {64,128,256}` | `128` | 出力サイズ（64 の倍数） |
| `--slim` | オフ | 細腕（Alex、3px）。オフで Steve（4px） |
| `--fit {contain,cover,stretch}` | `contain` | 画像の収め方（下記） |
| `--bg COLOR` | `none` | 余白色 `#ffffff` 形式、`none` で透明 |
| `--pixel` | オフ | 縮小時に最近傍補間を使う |
| `--face` | オフ | 顔写真モード（`--back` `--fit` `--bg` は無視） |

`--fit` の意味はブラウザ版と同じです。
`contain` は全身が入る代わりに上下左右に余白ができ、`cover` は余白なしで端が切れます。

> CLI 版には「顔 + 体」モードはありません。必要な場合は `index.html` を使ってください。

---

## サイズとエディション

| サイズ | 向き | 備考 |
| --- | --- | --- |
| 64×64 | Java Edition（1.8 以降） | Java で使えるのはこのサイズのみ。最初はこれで確認するのが無難 |
| 128×128 | Bedrock Edition | 統合版の推奨サイズ。細部がきれい |
| 256×256 | Bedrock Edition（高解像度） | さらに精細。ファイルサイズは増える |

腕の太さはスキン側の形式で決まります（Steve = 4px / Alex = 3px）。アップロード時に選択するモデルと揃えてください。合っていないと腕が 1px ずれて表示されます。

---

## 仕組み

1. 入力画像を **16×32**（1 ブロック = 1px @64×64）のキャンバスに `fit` の指定で収める
2. そのキャンバスから部位を切り出す
   - 頭 `(4,0)-(12,8)` / 胴 `(4,8)-(12,20)` / 右腕 `(0,8)-(4,20)` / 左腕 `(12,8)-(16,20)` / 右脚 `(4,20)-(8,32)` / 左脚 `(8,20)-(12,32)`
3. 各部位を直方体の展開図としてスキンに書き込む。前面はそのまま、それ以外の面は次のように作る
   - 背面 … 指定画像、なければ前面を 0.85 倍
   - 側面 … 前面の左右端 1px を横に引き伸ばして 0.85 倍
   - 上面 … 前面の上端 1px を引き伸ばして 1.1 倍
   - 底面 … 前面の下端 1px を引き伸ばして 0.6 倍
4. 重ね着レイヤー（第 2 レイヤー）は透明のまま

顔写真モードでは、頭の前面だけ元画像（`cover` で正方形に切り出し）、頭の側面と体は画像全体を強くぼかしたもの（半径 = 長辺 / 12）から作ります。毛色のグラデーションが全身に回るので、ペットやオリキャラ風のスキンになります。

---

## コツと制限

- **正面・全身・背景が単純な写真**が一番きれいに出ます。斜め向き、腕を組んだポーズ、体の一部が隠れている写真は崩れます
- 背景は除去しません。**切り抜き済みの透過 PNG** を入れると余計な色が入りません
- 側面・背面は端の色を引き伸ばしただけの「それらしい」色です。正面からの情報しかありません
- 顔写真モードは顔が画像いっぱいに入っているものほど良い結果になります。顔が小さいと髪や背景が前面を占めます
- 64×64 で細部が潰れる場合は 128×128 で作る（Bedrock のみ）か、`cover` / `--pixel` を試してください

## トラブルシューティング

| 症状 | 対処 |
| --- | --- |
| `ModuleNotFoundError: No module named 'PIL'` | `pip install pillow`（環境によっては `python -m pip` / `py -m pip`） |
| スキンが真っ透明 / 真っ黒になる | 入力が透過 PNG か、`--bg` を指定していないか確認。`--bg "#808080"` などで塗って確認 |
| 表示がぼやける | `--pixel` を付ける、または元画像を大きめにする |
| 腕がずれる | `--slim`（Alex）とアップロード時のモデル選択を一致させる |
| 色が変 / 縞になる | `--fit stretch` をやめて `contain` か `cover` にする |
| Mac / Linux で `python` が無い | `python3 skin_gen.py ...` を使う |

---

# English

A tool that turns a photo into a Minecraft skin (64×64 / 128×128 / 256×256). It ships with two front ends: `index.html`, which runs entirely in the browser, and the `skin_gen.py` CLI.

## Features

- Maps a full-body front photo straight onto the front faces of the skin (head, torso, arms, legs)
- Generates the side, top and bottom faces by stretching the border pixels of the front face
- Back face: use your own back image, or auto-generate it from a darkened front
- Face mode: prints a face on the head and paints the body with a blurred copy
- "Face + body" mode (browser version only) for separate face and body images
- Steve (4 px arms) and Alex (3 px arms) models
- The overlay (second) layer is left transparent, so the PNG is ready to upload
- Nearest-neighbour option for pixel-art inputs

## Files

```
SKINGEN/
├── index.html     # Browser version (single file, works offline)
├── skin_gen.py    # CLI (Python 3 + Pillow)
├── 1.jpg          # Sample input
└── 1_skin.png     # Sample output (128×128)
```

## Browser version

1. Double-click `index.html` to open it (no server, no network needed)
2. Click or drag & drop an image onto **Front image**
3. Change any setting and the previews update instantly
4. Click **Download skin (PNG)** to save `<original-name>_skin.png`
5. Apply it in Minecraft

### Modes

| Mode | Behaviour |
| --- | --- |
| Whole body photo | Wraps the whole-body photo around the entire skin (default) |
| Face only | Prints the face on the head; the body is painted with a strongly blurred copy |
| Face + body | Face image on the head, body image on torso, arms and legs (falls back to the blurred face if no body image is given) |

### Settings

| Setting | Description |
| --- | --- |
| Size | 64×64 (Java) / 128×128 / 256×256 |
| Fit | `contain` (fit whole image, add margins) / `cover` (fill and crop the centre) / `stretch` (ignore aspect ratio) |
| Slim arms (Alex) | 3 px arms instead of the 4 px Steve arms |
| Pixel art | Nearest-neighbour downscaling for pixel-art sources |
| Transparent margins | Leave margins transparent; turn off to fill them with the colour picker value |

Fit, background and back image are ignored in `Face only` mode (they are disabled automatically).

### Applying the skin

- **Java Edition**: Launcher → Skins → New skin → pick the downloaded PNG (64×64)
- **Bedrock Edition**: import the PNG from the launcher's skin screen or the in-game character creator (64 / 128 / 256 supported)

## CLI version

### Setup

```bash
pip install pillow
```

### Usage

```bash
# Basic: write <input>_skin.png at 128x128
python skin_gen.py input.png

# Java-friendly 64x64 with a custom name
python skin_gen.py input.png -s 64 -o myskin.png

# Separate back image and slim (Alex) arms
python skin_gen.py front.png --back back.png --slim

# Face mode
python skin_gen.py face.jpg --face

# Pixel-art look, white margins, cover
python skin_gen.py input.png --pixel --bg "#ffffff" --fit cover
```

### Options

| Option | Default | Description |
| --- | --- | --- |
| `input` | — | Front full-body image (required) |
| `--back IMG` | none | Back image; when omitted, the front is darkened by 0.85 |
| `-o, --output PATH` | `<input>_skin.png` | Output path |
| `-s, --size {64,128,256}` | `128` | Output size (multiple of 64) |
| `--slim` | off | Slim Alex arms (3 px) instead of Steve (4 px) |
| `--fit {contain,cover,stretch}` | `contain` | How the image is fitted |
| `--bg COLOR` | `none` | Margin colour as `#ffffff`; `none` keeps it transparent |
| `--pixel` | off | Use nearest-neighbour resampling |
| `--face` | off | Face mode (ignores `--back`, `--fit`, `--bg`) |

`--fit` behaves exactly like in the browser version: `contain` keeps the whole body but adds margins, `cover` fills the area and crops the edges.

> The CLI has no "Face + body" mode. Use `index.html` for that.

## Size and edition

| Size | Target | Notes |
| --- | --- | --- |
| 64×64 | Java Edition (1.8+) | The only size Java accepts. Best for a first check |
| 128×128 | Bedrock Edition | Recommended for Bedrock; more detail |
| 256×256 | Bedrock Edition (high-res) | Sharper still; larger file |

Arm width is part of the skin format (Steve = 4 px, Alex = 3 px). Match `--slim` with the model you pick when uploading; a mismatch shifts the arms by one pixel.

## How it works

1. The input is fitted into a **16×32** canvas (1 block = 1 px at 64×64) according to `fit`
2. Parts are cropped from that canvas
   - head `(4,0)-(12,8)`, torso `(4,8)-(12,20)`, right arm `(0,8)-(4,20)`, left arm `(12,8)-(16,20)`, right leg `(4,20)-(8,32)`, left leg `(8,20)-(12,32)`
3. Each part is written as the unwrapped faces of a box. The front is copied verbatim; the other faces are built as follows
   - back … your back image, or the front at 0.85
   - sides … one border column of the front, stretched, at 0.85
   - top … one border row of the front, stretched, at 1.1
   - bottom … one border row of the front, stretched, at 0.6
4. The overlay (second) layer stays transparent

In face mode only the head front uses the original image (centre-cropped with `cover`); the head sides and the body come from a strongly blurred copy (radius = longest side / 12), which spreads the dominant fur/hair colour over the whole skin.

## Tips and limitations

- Straight-on, full-body photos with a simple background work best. Angled shots, folded arms and occluded limbs break the mapping
- Backgrounds are not removed. A pre-cut transparent PNG gives the cleanest result
- Sides and back are just stretched border colours — there is no information beyond the front image
- Face mode works best when the face fills most of the frame; small faces let hair and background take over the head
- If 64×64 loses too much detail, build at 128×128 (Bedrock only) or try `cover` / `--pixel`

## Troubleshooting

| Symptom | Fix |
| --- | --- |
| `ModuleNotFoundError: No module named 'PIL'` | `pip install pillow` (or `python -m pip` / `py -m pip`) |
| Skin comes out fully transparent or black | Check whether the input is a transparent PNG, and try `--bg "#808080"` |
| Everything looks blurry | Add `--pixel`, or use a larger source image |
| Arms are shifted | Make `--slim` (Alex) match the model you select when uploading |
| Weird colours or banding | Avoid `--fit stretch`; use `contain` or `cover` |
| No `python` on macOS / Linux | Use `python3 skin_gen.py ...` |

[↑ Back to top](#top)

---

## License

No license has been specified for this repository yet.
