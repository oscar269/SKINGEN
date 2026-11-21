<a id="top"></a>

# SKINGEN — Minecraft Skin Generator

画像から Minecraft のスキン（64×64 / 128×128 / 256×256）を生成するブラウザツールです。
`index.html` を開くだけで動き、インストールもサーバもネット接続も不要です。

**▶ [ブラウザですぐ使う（GitHub Pages）](https://oscar269.github.io/SKINGEN/)**
｜ ソース: [github.com/oscar269/SKINGEN](https://github.com/oscar269/SKINGEN)

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
- 3D プレビューをドラッグでぐるぐる回せる（ホイールで拡大、ダブルクリックでリセット）
- 設定を変えると即座にプレビューが更新され、PNG で保存できる

## ファイル構成

```
SKINGEN/
├── index.html   # ツール本体（1ファイル完結）
└── README.md
```

---

## 使い方

1. [ブラウザ版（GitHub Pages）](https://oscar269.github.io/SKINGEN/) を開く。ローカルで動かすなら `index.html` をダブルクリック（Chrome / Edge / Firefox 推奨）
2. 画像の枠をクリックして選ぶ、またはドラッグ & ドロップ（既定の `Face + body` では、Front に顔画像、Body に体の画像を入れる）
3. 必要なら Mode / Size / Fit などを変更する（変更すると自動で再生成されます）
4. **Download skin (PNG)** を押して `<元のファイル名>_skin.png` を保存
5. Minecraft に適用する

右側には 2 つのプレビューが出ます。

- **Skin texture** … 保存される PNG そのもの（展開図）
- **3D preview** … 実際のモデル。ドラッグで回転、ホイール（ピンチ）で拡大縮小、ダブルクリック / Reset view で正面に戻る。Auto rotate でゆっくり自動回転

### モード

| モード | 動作 |
| --- | --- |
| Whole body photo | 全身写真をスキン全体に貼る |
| Face only | 顔を頭の前面に貼り、体はその画像を強くぼかした色で塗る |
| Face + body | 顔画像を頭に、体画像を胴・腕・脚に貼る（体画像が未指定なら顔のぼかしで代用）。既定はこれ |

### 設定項目

| 項目 | 説明 |
| --- | --- |
| Front image | 正面（または顔）の画像。必須。右のセレクトでこの画像の Fit を指定 |
| Body image | `Face + body` のときだけ表示。体用の画像。右のセレクトでこの画像の Fit を指定 |
| Back image | 背面用の画像。未指定なら正面を暗くして使用。右のセレクトでこの画像の Fit を指定 |
| Mode | 上表の 3 モード |
| Size | 64×64（Java）/ 128×128 / 256×256 |
| Fit（画像ごと） | `contain`（全体を収め、余白は端のピクセルを引き伸ばして埋める）/ `cover`（はみ出しを中央で切り取り）/ `stretch`（縦横比を無視して引き伸ばす）。Front / Body / Back で独立して指定できる |
| Slim arms (Alex) | 腕を 3px 幅にする。オフで Steve（4px） |
| Pixel art | 縮小時に補間せず最近傍で縮小（ドット絵向け） |
| Transparent margins | 元画像の透過部分を透明のままにする。オフにすると右のカラーピッカーの色で塗る |
| Face on sides / Face on back | `Face only` / `Face + body` のときだけ表示。それぞれオン（既定）なら頭の側面 / 背面に顔画像そのものを貼る。オフにするとその面は体画像（なければぼかした顔）の頭部分から作る |

`Face only` では Body / Back 画像と背景色は使いません（自動で無効化されます）。Front の Fit は頭の前面の切り出し方として効きます。

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

1. 入力画像を **16×32**（1 ブロック = 1px @64×64）のキャンバスに `Fit` の指定で収める。`contain` で余白が出る場合は端の 1px を引き伸ばして埋める（透明な余白は脚や頭に穴が空いてしまうため）
2. そのキャンバスから部位を切り出す
   - 頭 `(4,0)-(12,8)` / 胴 `(4,8)-(12,20)` / 右腕 `(0,8)-(4,20)` / 左腕 `(12,8)-(16,20)` / 右脚 `(4,20)-(8,32)` / 左脚 `(8,20)-(12,32)`
3. 各部位を直方体の展開図としてスキンに書き込む。前面はそのまま、それ以外の面は次のように作る
   - 背面 … 背面画像があればそれを使う（後ろから撮った写真では左右が逆になるので、腕・脚は反対側から切り出す）。なければ前面を左右反転して 0.85 倍に暗く
   - 側面 … 前面の左右端 1px を横に引き伸ばして 0.85 倍
   - 上面 … 前面の上端 1px を引き伸ばして 1.1 倍
   - 底面 … 前面の下端 1px を引き伸ばして 0.6 倍
4. 重ね着レイヤー（第 2 レイヤー）は透明のまま

`Face only` / `Face + body`（体画像なし）では、頭の前面だけ元画像（Front の Fit で切り出し）、頭の側面・背面も同じ顔画像から作ります（背面は左右反転、側面は 0.85 倍に暗く）。`Face on sides` / `Face on back` をオフにすると、その面だけ体画像の頭部分から作ります（上面・底面は側面の設定に従う）。体は画像全体を強くぼかしたもの（半径 = 長辺 / 12）から作るので、毛色のグラデーションが全身に回り、ペットやオリキャラ風のスキンになります。

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
| 背中で腕・脚の左右が逆になる | `Back image` に正面の写真を入れると鏡像になります。必ず「後ろから撮った写真」を指定してください |
| 色が変 / 縞になる | `Fit` を `stretch` から `contain` か `cover` に変える |
| 保存した PNG のサイズが想定と違う | `Size` の選択を確認（Java は 64×64） |
| スマホで開くとドラッグできない | 枠をタップしてファイル選択から指定する |

---

## 変更履歴

ツール右上の **Changelog** ボタンからも見られます。

### 2026-11-21

- 3D プレビュー（ドラッグで回転、ホイール / ピンチで拡大、自動回転、ダブルクリックでリセット）
- `Fit` を画像ごとに分離（Front / Body / Back）
- 顔の回り込みを `Face on sides` / `Face on back` の 2 つに分割
- 既定モードを `Face + body` に変更
- 修正: `contain` で脚と頭の上部に透明な穴が空いていた
- 修正: Alex（細腕）の腕の底面が 1px ずれていた
- 修正: `Back image` があると `Face on back` が効かなかった
- 修正: 頭の側面に顔が乗らず、端の色を伸ばした帯になっていた

### 2026-11-18

- 背面の左右反転、側面が空になる問題、頭がぼける問題を修正
- 顔と体を別画像で指定する `Face + body` モードを追加
- 日本語 / 英語の README を追加
- ブラウザだけで動くスキン生成ツール（64 / 128 / 256）

---

# English

A browser tool that turns a photo into a Minecraft skin (64×64 / 128×128 / 256×256). Open `index.html` and it just works — no install, no server, no network.

**▶ [Use it in your browser (GitHub Pages)](https://oscar269.github.io/SKINGEN/)**
— Source: [github.com/oscar269/SKINGEN](https://github.com/oscar269/SKINGEN)

## Features

- Maps a full-body front photo straight onto the front faces of the skin (head, torso, arms, legs)
- Generates the side, top and bottom faces by stretching the border pixels of the front face
- Back face: use your own back image, or auto-generate it from a darkened front
- Face mode: prints a face on the head and paints the body with a blurred copy
- Separate face and body images
- Steve (4 px arms) and Alex (3 px arms) models
- The overlay (second) layer is left transparent, so the PNG is ready to upload
- Nearest-neighbour option for pixel-art sources
- A 3D preview you can spin with the mouse (wheel to zoom, double-click to reset)
- Live previews on every setting change, plus PNG export

## Files

```
SKINGEN/
├── index.html   # The whole tool, in one file
└── README.md
```

## Usage

1. Open the [hosted version (GitHub Pages)](https://oscar269.github.io/SKINGEN/), or double-click `index.html` locally (Chrome / Edge / Firefox recommended)
2. Click an image box to pick a file, or drag & drop one onto it (the default `Face + body` mode takes a face image in Front and a body image in Body)
3. Adjust Mode / Size / Fit etc. — the previews regenerate automatically
4. Click **Download skin (PNG)** to save `<original-name>_skin.png`
5. Apply it in Minecraft

Two previews appear on the right:

- **Skin texture** — the exact PNG that gets saved (the unwrapped texture)
- **3D preview** — the actual model: drag to rotate, wheel / pinch to zoom, double-click or *Reset view* to go back to the front, *Auto rotate* to spin it slowly

### Modes

| Mode | Behaviour |
| --- | --- |
| Whole body photo | Wraps the whole-body photo around the entire skin |
| Face only | Prints the face on the head; the body is painted with a strongly blurred copy |
| Face + body | Face image on the head, body image on torso, arms and legs (falls back to the blurred face if no body image is given). This is the default |

### Settings

| Setting | Description |
| --- | --- |
| Front image | The front (or face) image. Required. The select beside it sets this image's Fit |
| Body image | Shown only in `Face + body`; the image used for the body. Its own Fit select |
| Back image | The back image. When empty, a darkened copy of the front is used. Its own Fit select |
| Mode | One of the three modes above |
| Size | 64×64 (Java) / 128×128 / 256×256 |
| Fit (per image) | `contain` (fit whole image, stretch its border pixels into the padding) / `cover` (fill and crop the centre) / `stretch` (ignore aspect ratio). Front, Body and Back are independent |
| Slim arms (Alex) | 3 px arms instead of the 4 px Steve arms |
| Pixel art | Nearest-neighbour downscaling for pixel-art sources |
| Transparent margins | Keep transparent areas of the source transparent; turn off to fill them with the colour picker value |
| Face on sides / Face on back | Shown in `Face only` / `Face + body`. When on (default) the face image itself is put on the head's sides / back; when off that face is built from the body image's head instead (or the blurred face) |

In `Face only` mode, the Body / Back images and the background colour are ignored (they are disabled automatically). The Front Fit controls how the face is cropped for the head.

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

1. The input is fitted into a **16×32** canvas (1 block = 1 px at 64×64) according to `Fit`. With `contain` any padding is filled by stretching the border pixels, since transparent padding would punch a hole through a body part
2. Parts are cropped from that canvas
   - head `(4,0)-(12,8)`, torso `(4,8)-(12,20)`, right arm `(0,8)-(4,20)`, left arm `(12,8)-(16,20)`, right leg `(4,20)-(8,32)`, left leg `(8,20)-(12,32)`
3. Each part is written as the unwrapped faces of a box. The front is copied verbatim; the other faces are built as follows
   - back … your back image (arms and legs are read from the opposite side of it), or the front mirrored horizontally and darkened to 0.85
   - sides … one border column of the front, stretched, at 0.85
   - top … one border row of the front, stretched, at 1.1
   - bottom … one border row of the front, stretched, at 0.6
4. The overlay (second) layer stays transparent

In `Face only` / `Face + body` without a body image, the head front uses the original image (cut with the Front Fit) and the head's sides and back are built from the same face image (the back is mirrored, the sides darkened to 0.85). Turning off `Face on sides` / `Face on back` builds that face from the body image's head instead (the top and bottom follow the side setting). The body comes from a strongly blurred copy (radius = longest side / 12), which spreads the dominant fur/hair colour over the whole skin.

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
| Arms and legs are swapped on the back | A front photo in `Back image` comes out mirrored; use a photo taken from behind |
| Weird colours or banding | Change `Fit` from `stretch` to `contain` or `cover` |
| Saved PNG has an unexpected size | Check the `Size` selection (Java needs 64×64) |
| Drag & drop does not work on mobile | Tap the box and use the file picker |

[↑ Back to top](#top)

---

## Changelog

Also available from the **Changelog** button in the top right of the tool.

### 2026-11-21

- 3D preview: drag to spin, wheel / pinch to zoom, auto rotate, double-click to reset
- `Fit` is set per image (Front / Body / Back)
- The face wrapping is split into `Face on sides` and `Face on back`
- Default mode is now `Face + body`
- Fix: `contain` left transparent holes in the legs and the top of the head
- Fix: the Alex (slim) arm's bottom face was one pixel off
- Fix: `Face on back` did nothing when a `Back image` was set
- Fix: the head's sides showed a stretched border strip instead of the face

### 2026-11-18

- Fix back-face mirroring, blank side faces and the blurry head
- Add the `Face + body` mode with separate face and body images
- Add the bilingual README (Japanese / English)
- Browser-only skin generator (64 / 128 / 256)

---

## License

No license has been specified for this repository yet.
