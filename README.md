<a id="top"></a>

# SKINGEN — Minecraft Skin Generator

画像から Minecraft のスキン（64×64 / 128×128 / 256×256）を生成するブラウザツールです。
`index.html` を開くだけで動き、インストールもサーバもネット接続も不要です。

**日本語** | [English](#english)

---

## できること

- 全身が写った正面写真を、スキンの前面（頭・胴・腕・脚）にそのまま貼る
- 側面・上面・底面は前面の端のピクセルを引き伸ばして自動生成
- 背面は別画像を指定、または正面を少し暗くして自動生成
- 顔写真モード（顔だけを頭に貼り、体はぼかした色で塗る）
- 顔と体を別画像で指定するモード
- Steve（標準腕 4px）/ Alex（細腕 3px）対応
- 重ね着レイヤー（第 2 レイヤー）は透明のまま出力 → そのままアップロード可能
- ドット絵向けの最近傍補間オプション
- 設定を変えると即座にプレビューが更新され、PNG で保存できる

## ファイル構成

```
SKINGEN/
├── index.html   # ツール本体（1ファイル完結）
└── README.md
```

---

## 使い方

1. `index.html` をダブルクリックしてブラウザで開く（Chrome / Edge / Firefox 推奨）
2. **Front image** の枠をクリックして画像を選ぶ、またはドラッグ & ドロップ
3. 必要なら Mode / Size / Fit などを変更する（変更すると自動で再生成されます）
4. **Download skin (PNG)** を押して `<元のファイル名>_skin.png` を保存
5. Minecraft に適用する

右側には 2 つのプレビューが出ます。

- **Skin texture** … 保存される PNG そのもの（展開図）
- **Front preview** … 正面から見たときの見た目

### モード

| モード | 動作 |
| --- | --- |
| Whole body photo | 全身写真をスキン全体に貼る（基本） |
| Face only | 顔を頭の前面に貼り、体はその画像を強くぼかした色で塗る |
| Face + body | 顔画像を頭に、体画像を胴・腕・脚に貼る（体画像が未指定なら顔のぼかしで代用） |

### 設定項目

| 項目 | 説明 |
| --- | --- |
| Front image | 正面（または顔）の画像。必須 |
| Body image | `Face + body` のときだけ表示。体用の画像 |
| Back image | 背面用の画像。未指定なら正面を暗くして使用 |
| Mode | 上表の 3 モード |
| Size | 64×64（Java）/ 128×128 / 256×256 |
| Fit | `contain`（余白あり・全体を収める）/ `cover`（はみ出しを中央で切り取り）/ `stretch`（縦横比を無視して引き伸ばす） |
| Slim arms (Alex) | 腕を 3px 幅にする。オフで Steve（4px） |
| Pixel art | 縮小時に補間せず最近傍で縮小（ドット絵向け） |
| Transparent margins | 余白を透明にする。オフにすると右のカラーピッカーの色で塗る |

`Face only` のときは Fit / 背景色 / Back image は使いません（自動で無効化されます）。

### 適用方法

- **Java Edition**: Launcher → Skins → New skin → 保存した PNG を選択（64×64）
- **Bedrock Edition**: ランチャーのスキン画面、またはゲーム内のキャラクター作成から PNG をインポート（64 / 128 / 256 可）

---

## サイズとエディション

| サイズ | 向き | 備考 |
| --- | --- | --- |
| 64×64 | Java Edition（1.8 以降） | Java で使えるのはこのサイズのみ。最初はこれで確認するのが無難 |
| 128×128 | Bedrock Edition | 統合版の推奨サイズ。細部がきれい |
| 256×256 | Bedrock Edition（高解像度） | さらに精細。ファイルサイズは増える |

腕の太さはスキン側の形式で決まります（Steve = 4px / Alex = 3px）。アップロード時に選ぶモデルと必ず揃えてください。合っていないと腕が 1px ずれて表示されます。

---

## 仕組み

1. 入力画像を **16×32**（1 ブロック = 1px @64×64）のキャンバスに `Fit` の指定で収める
2. そのキャンバスから部位を切り出す
   - 頭 `(4,0)-(12,8)` / 胴 `(4,8)-(12,20)` / 右腕 `(0,8)-(4,20)` / 左腕 `(12,8)-(16,20)` / 右脚 `(4,20)-(8,32)` / 左脚 `(8,20)-(12,32)`
3. 各部位を直方体の展開図としてスキンに書き込む。前面はそのまま、それ以外の面は次のように作る
   - 背面 … 指定画像、なければ前面を 0.85 倍に暗く
   - 側面 … 前面の左右端 1px を横に引き伸ばして 0.85 倍
   - 上面 … 前面の上端 1px を引き伸ばして 1.1 倍
   - 底面 … 前面の下端 1px を引き伸ばして 0.6 倍
4. 重ね着レイヤー（第 2 レイヤー）は透明のまま

`Face only` / `Face + body`（体画像なし）では、頭の前面だけ元画像（`cover` で正方形に切り出し）、頭の側面と体は画像全体を強くぼかしたもの（半径 = 長辺 / 12）から作ります。毛色のグラデーションが全身に回るので、ペットやオリキャラ風のスキンになります。

---

## コツと制限

- **正面・全身・背景が単純な写真**が一番きれいに出ます。斜め向き、腕を組んだポーズ、体の一部が隠れている写真は崩れます
- 背景は除去しません。**切り抜き済みの透過 PNG** を入れると余計な色が入りません
- 側面・背面は端の色を引き伸ばしただけの「それらしい」色です。正面からの情報しかありません
- 顔モードは顔が画像いっぱいに入っているものほど良い結果になります。顔が小さいと髪や背景が前面を占めます
- 64×64 で細部が潰れる場合は 128×128 で作る（Bedrock のみ）か、`cover` / `Pixel art` を試してください

## トラブルシューティング

| 症状 | 対処 |
| --- | --- |
| 何も表示されない / Download が押せない | Front image に画像を入れたか確認。`Face + body` で体画像が空でも生成されます |
| スキンが真っ透明 / 真っ黒になる | 入力が透過 PNG でないか確認。`Transparent margins` をオフにして色を指定してみる |
| 表示がぼやける | `Pixel art` をオンにする、または元画像を大きめにする |
| 腕がずれる | `Slim arms (Alex)` とアップロード時のモデル選択を一致させる |
| 色が変 / 縞になる | `Fit` を `stretch` から `contain` か `cover` に変える |
| 保存した PNG のサイズが想定と違う | `Size` の選択を確認（Java は 64×64） |
| スマホで開くとドラッグできない | 枠をタップしてファイル選択から指定する |

---

# English

A browser tool that turns a photo into a Minecraft skin (64×64 / 128×128 / 256×256). Open `index.html` and it just works — no install, no server, no network.

## Features

- Maps a full-body front photo straight onto the front faces of the skin (head, torso, arms, legs)
- Generates the side, top and bottom faces by stretching the border pixels of the front face
- Back face: use your own back image, or auto-generate it from a darkened front
- Face mode: prints a face on the head and paints the body with a blurred copy
- Separate face and body images
- Steve (4 px arms) and Alex (3 px arms) models
- The overlay (second) layer is left transparent, so the PNG is ready to upload
- Nearest-neighbour option for pixel-art sources
- Live previews on every setting change, plus PNG export

## Files

```
SKINGEN/
├── index.html   # The whole tool, in one file
└── README.md
```

## Usage

1. Double-click `index.html` to open it in a browser (Chrome / Edge / Firefox recommended)
2. Click the **Front image** box to pick an image, or drag & drop one onto it
3. Adjust Mode / Size / Fit etc. — the previews regenerate automatically
4. Click **Download skin (PNG)** to save `<original-name>_skin.png`
5. Apply it in Minecraft

Two previews appear on the right:

- **Skin texture** — the exact PNG that gets saved (the unwrapped texture)
- **Front preview** — how the skin looks from the front

### Modes

| Mode | Behaviour |
| --- | --- |
| Whole body photo | Wraps the whole-body photo around the entire skin (default) |
| Face only | Prints the face on the head; the body is painted with a strongly blurred copy |
| Face + body | Face image on the head, body image on torso, arms and legs (falls back to the blurred face if no body image is given) |

### Settings

| Setting | Description |
| --- | --- |
| Front image | The front (or face) image. Required |
| Body image | Shown only in `Face + body`; the image used for the body |
| Back image | The back image. When empty, a darkened copy of the front is used |
| Mode | One of the three modes above |
| Size | 64×64 (Java) / 128×128 / 256×256 |
| Fit | `contain` (fit whole image, add margins) / `cover` (fill and crop the centre) / `stretch` (ignore aspect ratio) |
| Slim arms (Alex) | 3 px arms instead of the 4 px Steve arms |
| Pixel art | Nearest-neighbour downscaling for pixel-art sources |
| Transparent margins | Leave margins transparent; turn off to fill them with the colour picker value |

In `Face only` mode, Fit, background colour and Back image are ignored (they are disabled automatically).

### Applying the skin

- **Java Edition**: Launcher → Skins → New skin → pick the saved PNG (64×64)
- **Bedrock Edition**: import the PNG from the launcher's skin screen or the in-game character creator (64 / 128 / 256 supported)

## Size and edition

| Size | Target | Notes |
| --- | --- | --- |
| 64×64 | Java Edition (1.8+) | The only size Java accepts. Best for a first check |
| 128×128 | Bedrock Edition | Recommended for Bedrock; more detail |
| 256×256 | Bedrock Edition (high-res) | Sharper still; larger file |

Arm width is part of the skin format (Steve = 4 px, Alex = 3 px). Match the model you pick when uploading; a mismatch shifts the arms by one pixel.

## How it works

1. The input is fitted into a **16×32** canvas (1 block = 1 px at 64×64) according to `Fit`
2. Parts are cropped from that canvas
   - head `(4,0)-(12,8)`, torso `(4,8)-(12,20)`, right arm `(0,8)-(4,20)`, left arm `(12,8)-(16,20)`, right leg `(4,20)-(8,32)`, left leg `(8,20)-(12,32)`
3. Each part is written as the unwrapped faces of a box. The front is copied verbatim; the other faces are built as follows
   - back … your back image, or the front darkened to 0.85
   - sides … one border column of the front, stretched, at 0.85
   - top … one border row of the front, stretched, at 1.1
   - bottom … one border row of the front, stretched, at 0.6
4. The overlay (second) layer stays transparent

In `Face only` / `Face + body` without a body image, only the head front uses the original image (centre-cropped with `cover`); the head sides and the body come from a strongly blurred copy (radius = longest side / 12), which spreads the dominant fur/hair colour over the whole skin.

## Tips and limitations

- Straight-on, full-body photos with a simple background work best. Angled shots, folded arms and occluded limbs break the mapping
- Backgrounds are not removed. A pre-cut transparent PNG gives the cleanest result
- Sides and back are just stretched border colours — there is no information beyond the front image
- Face mode works best when the face fills most of the frame; small faces let hair and background take over the head
- If 64×64 loses too much detail, build at 128×128 (Bedrock only) or try `cover` / `Pixel art`

## Troubleshooting

| Symptom | Fix |
| --- | --- |
| Nothing shows up / Download stays disabled | Load a Front image. `Face + body` still generates without a body image |
| Skin comes out fully transparent or black | Check whether the input is a transparent PNG; turn off `Transparent margins` and pick a colour |
| Everything looks blurry | Turn on `Pixel art`, or use a larger source image |
| Arms are shifted | Make `Slim arms (Alex)` match the model you select when uploading |
| Weird colours or banding | Change `Fit` from `stretch` to `contain` or `cover` |
| Saved PNG has an unexpected size | Check the `Size` selection (Java needs 64×64) |
| Drag & drop does not work on mobile | Tap the box and use the file picker |

[↑ Back to top](#top)

---

## License

No license has been specified for this repository yet.
