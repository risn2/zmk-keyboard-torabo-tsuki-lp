# Torabo Tsuki LP ファームウェア説明書

## 概要

Torabo Tsuki LP は、**ZMK ファームウェア**ベースの**左右分割型ワイヤレスキーボード**です。BLE（Bluetooth Low Energy）で左右が接続され、トラックボールまたはトラックパッドによるポインティングデバイスを内蔵しています。

### ハードウェア仕様

| 項目 | 内容 |
|------|------|
| MCU ボード | BMP Boost（nRF52840 ベース） |
| 接続方式 | Bluetooth LE + USB |
| キーマトリクス | 7列 × 5行（片側） |
| レイアウトサイズ | S / M / L の3種類を選択可能 |
| トラックボール | PAW3222（SPI 接続） |
| トラックパッド | IQS7211E（I2C 接続） |
| バッテリー | 非LiPo電池対応 |
| ステータスLED | あり |

---

## 実装されている機能

### 1. 左右分割キーボード

左右それぞれが独立した nRF52840 MCU を搭載し、BLE で通信します。片方が Central（メイン）、もう片方が Peripheral（サブ）として動作します。

### 2. 複数レイアウトサイズ対応

物理レイアウトとして3サイズが定義されています：

- **S（Small）**: 4行 × 片側5〜6列（親指キー含む）
- **M（Medium）**: 4行 × 片側6〜7列（両端に1列追加）
- **L（Large）**: 5行 × 片側7列（最上段の数字行を含む全キー）

デフォルトは **L レイアウト** が選択されています（`torabo_tsuki_lp.dtsi` の `zmk,physical-layout = &physical_layout_l;`）。

### 3. キーマップ（4レイヤー構成）

#### Layer 0 — ベースレイヤー（QWERTY）

標準的な QWERTY 配列です。L サイズの場合、最上段に数字行があります。

- 左親指: `Backspace` → `Space`（Layer 2 ホールド） → `英数`（Shift ホールド）
- 右親指: `かな`（Shift ホールド） → `Enter`（Layer 3 ホールド） → `Backspace`
- 左右のキーボード接合部: `マウス左クリック` / `マウス右クリック`

#### Layer 1 — マウスレイヤー

ほぼ透過（`&trans`）で、中指周辺に左クリック・右クリックが配置されているポインティング操作専用のレイヤーです。

#### Layer 2 — 記号・数字レイヤー

Layer 0 の左親指の `Space` を長押しで有効化します。

- 上段: Shift+数字キー（`!`, `@`, `#`, `$`, `%`, `^`, `&`, `*`, `(`, `)`）
- 中段左: 数字 `1`〜`5` / 中段右: `{`, `-`, `=`, `}`, `:`
- 下段左: 数字 `6`〜`0` / 下段右: `_`, `+`, `[`, `]`, `\`

**コンボ**: このレイヤーでキーポジション 28 + 29 を同時押しすると **Bluetooth ペアリングクリア**（`BT_CLR`）が発動します。

#### Layer 3 — ファンクション・ナビゲーションレイヤー

Layer 0 の右親指の `Enter` を長押しで有効化します。

- 上段: `F1`〜`F5` / `Home`, `PgUp`, `PgDn`, `End`, `Esc`
- 中段: `F6`〜`F10` / 矢印キー（`←`, `↓`, `↑`, `→`）, `Tab`
- 下段: `F11`, `F12`, `USB/BLE 切替`, `BT_NXT` / `'`, `"`, `` ` ``, `~`, `|`

### 4. トラックボール（PAW3222）

- SPI 接続のオプティカルトラックボールセンサー
- ファームウェアビルド時にスニペット `input-trackball` を指定して有効化
- 左側に搭載する場合、X軸・Y軸を反転補正（`INPUT_TRANSFORM_X_INVERT | INPUT_TRANSFORM_Y_INVERT`）

### 5. トラックパッド（IQS7211E）

- I2C 接続の静電容量式トラックパッド
- 217バイトの初期化レジスタデータによるキャリブレーション済み
- タップ、ホールド、スワイプなどのジェスチャー対応
- ファームウェアビルド時にスニペット `input-trackpad` または `input-trackpad-mini` を指定して有効化

### 6. ダブルトラックボール構成

両側にトラックボールを搭載する構成にも対応しています。スニペット `input-split` / `input-split-listener` を組み合わせて、片側のポインティングデータをもう片側へ転送します。

### 7. 省電力管理（4段階パワーモード）

Central 側で動作するカスタム省電力機能が実装されています（`src/board.c`）。

| モード | アイドル時間 | BLE接続間隔 |
|--------|-------------|------------|
| ACTIVE | 0〜5秒 | 標準 |
| SLEEP1 | 5秒〜 | 標準 × 2 |
| SLEEP2 | 15秒〜 | 標準 × 4 |
| SLEEP3 | 30秒〜 | 標準 × 8 |

- キー入力やマウス操作を検出すると即座に ACTIVE に復帰
- USB 電源接続中は常に ACTIVE を維持

### 8. ZMK Studio 対応

`CONFIG_ZMK_STUDIO=y` が有効化されており、Web ベースの ZMK Studio からリアルタイムでキーマップを変更できます。

### 9. USB ブートローダートリガー

`CONFIG_ZMK_CDC_ACM_BOOTLOADER_TRIGGER=y` により、USB CDC ACM 経由でブートローダーモードに入ることができます。

### 10. ステータス LED

BMP ボード上の GPIO に接続されたステータス LED が動作状態を表示します。

### 11. 非LiPoバッテリー管理

`zmk-feature-non-lipo-battery-management` モジュールにより、LiPo 以外の電池（乾電池等）に対応した電圧管理を実装しています。

- 最低電圧: 1000mV
- 低電圧警告: 900mV

---

## ビルド構成

`build.yaml` で以下のビルドバリアントが定義されています：

| アーティファクト名 | 説明 |
|-------------------|------|
| `torabo_tsuki_lp_left_central` | 左手 Central（トラックボール付き、メイン側） |
| `torabo_tsuki_lp_right_peripheral` | 右手 Peripheral（通常のサブ側） |
| `torabo_tsuki_lp_left_peripheral` | 左手 Peripheral（通常のサブ側） |
| `torabo_tsuki_lp_right_central` | 右手 Central（トラックボール付き、メイン側） |
| `torabo_tsuki_lp_double_ball_left_peripheral` | 左手 Peripheral（ダブルトラックボール構成） |
| `torabo_tsuki_lp_double_ball_right_central` | 右手 Central（ダブルトラックボール構成） |
| `settings_reset` | 設定リセット用ファームウェア |

トラックボールを**左側**に搭載する場合は `left_central` + `right_peripheral` を、**右側**に搭載する場合は `right_central` + `left_peripheral` を使用します。

---

## キーマップの変更方法

### 方法 1: キーマップファイルを直接編集する（推奨）

**編集対象ファイル: `config/keymap.keymap`**

このファイルがデフォルトキーマップの定義ファイルです。`boards/shields/torabo_tsuki_lp/torabo_tsuki_lp.keymap` は `config/keymap.keymap` を `#include` しているだけなので、変更は `config/keymap.keymap` に対して行います。

#### ファイル構造

```dts
/ {
    combos {
        // コンボキーの定義（同時押し）
    };

    keymap {
        compatible = "zmk,keymap";

        layer_0 {
            bindings = < ... >;  // ベースレイヤー
        };

        layer_1 {
            bindings = < ... >;  // レイヤー1
        };

        layer_2 {
            bindings = < ... >;  // レイヤー2
        };

        layer_3 {
            bindings = < ... >;  // レイヤー3
        };
    };
};
```

#### キーバインディングの書式

各レイヤーの `bindings` 内に、キーマトリクスの順番でキーの動作を記述します。

| 書式 | 説明 | 例 |
|------|------|-----|
| `&kp KEY` | 通常のキー入力 | `&kp A`, `&kp LSHIFT`, `&kp N1` |
| `&lt LAYER KEY` | レイヤータップ（ホールドでレイヤー切替、タップでキー入力） | `&lt 2 SPACE` |
| `&mt MOD KEY` | モッドタップ（ホールドで修飾キー、タップでキー入力） | `&mt LSHIFT LANGUAGE_1` |
| `&mkp BUTTON` | マウスボタン | `&mkp LCLK`, `&mkp RCLK` |
| `&trans` | 透過（下レイヤーの動作を引き継ぐ） | `&trans` |
| `&bt ACTION` | Bluetooth 操作 | `&bt BT_CLR`, `&bt BT_NXT` |
| `&out OUTPUT` | 出力先切替 | `&out OUT_USB`, `&out OUT_BLE` |

#### 変更手順

1. `config/keymap.keymap` を開く
2. 変更したいレイヤーの `bindings` を編集する
3. ファイルを保存し、ファームウェアをリビルドする

#### 例: ベースレイヤーの ESC を Grave（`` ` ``）に変更する

```dts
        layer_0 {
            bindings = <
&kp GRAVE  &kp N1     &kp N2    &kp N3    &kp N4    &kp N5    ...
```

#### 例: 新しいレイヤーを追加する

```dts
        layer_4 {
            bindings = <
&trans  &trans  &trans  &trans  &trans  &trans              &trans  &trans  &trans  &trans  &trans  &trans
...
            >;
        };
```

### 方法 2: ZMK Studio を使用する

ZMK Studio 対応が有効になっているため、Web ブラウザから GUI でキーマップを変更できます。

1. キーボードを USB でPCに接続する
2. [ZMK Studio](https://zmk.studio/) にアクセスする
3. デバイスを選択し、キーマップを GUI で編集する
4. 変更はリアルタイムでキーボードに反映される（リビルド不要）

### 方法 3: コンボの変更

同時押しキーの定義は、`config/keymap.keymap` の `combos` セクションで変更します。

```dts
    combos {
        compatible = "zmk,combos";

        bt_clear {
            bindings = <&bt BT_CLR>;    // 実行する動作
            key-positions = <28 29>;     // 同時押しするキーの位置番号
            layers = <2>;               // 有効なレイヤー（省略で全レイヤー）
        };
    };
```

---

## ビルド方法

### GitHub Actions によるビルド（推奨）

このリポジトリを GitHub にプッシュすると、`build.yaml` の定義に基づいて GitHub Actions が自動でファームウェアをビルドします。

1. このリポジトリを GitHub にフォークまたはプッシュする
2. GitHub Actions が自動で実行され、全バリアントがビルドされる
3. Actions の Artifacts からビルド済みの `.uf2` ファイルをダウンロードする

### ローカルビルド

ローカル環境でビルドする場合は、ZMK の開発環境（Zephyr SDK + West）のセットアップが必要です。

#### 前提条件

- [Zephyr SDK](https://docs.zephyrproject.org/latest/develop/getting_started/index.html) がインストール済み
- `west` コマンドが使用可能

#### セットアップ

```bash
# リポジトリのクローン
git clone <リポジトリURL>
cd zmk-keyboard-torabo-tsuki-lp

# West の初期化と依存モジュールの取得
west init -l config
west update
```

#### ビルドコマンド

各バリアントを個別にビルドします。`-b` でボード、`-S` でスニペットを指定します。

**左手 Central + 右手 Peripheral（トラックボール左側搭載）:**

```bash
# 左手 Central（トラックボール付き）
west build -s zmk/app -b bmp_boost -- \
  -DSHIELD=torabo_tsuki_lp_left \
  -DSNIPPET="studio-rpc-usb-uart;split-central;input-trackball;input-listener"

# 右手 Peripheral
west build -s zmk/app -b bmp_boost -- \
  -DSHIELD=torabo_tsuki_lp_right \
  -DSNIPPET="studio-rpc-usb-uart"
```

**右手 Central + 左手 Peripheral（トラックボール右側搭載）:**

```bash
# 右手 Central（トラックボール付き）
west build -s zmk/app -b bmp_boost -- \
  -DSHIELD=torabo_tsuki_lp_right \
  -DSNIPPET="studio-rpc-usb-uart;split-central;input-trackball;input-listener"

# 左手 Peripheral
west build -s zmk/app -b bmp_boost -- \
  -DSHIELD=torabo_tsuki_lp_left \
  -DSNIPPET="studio-rpc-usb-uart"
```

**ダブルトラックボール構成:**

```bash
# 右手 Central
west build -s zmk/app -b bmp_boost -- \
  -DSHIELD=torabo_tsuki_lp_right \
  -DSNIPPET="studio-rpc-usb-uart;split-central;input-trackball;input-listener;input-split-listener"

# 左手 Peripheral
west build -s zmk/app -b bmp_boost -- \
  -DSHIELD=torabo_tsuki_lp_left \
  -DSNIPPET="studio-rpc-usb-uart;input-trackball;input-split"
```

**設定リセット:**

```bash
west build -s zmk/app -b bmp_boost -- -DSHIELD=settings_reset
```

ビルド成果物は `build/zephyr/zmk.uf2` に生成されます。バリアントごとにビルドディレクトリを分ける場合は `-d build/left` のようにオプションを追加してください。

### ファームウェアの書き込み

1. キーボードをブートローダーモードにする（USB 接続中に CDC ACM トリガー、またはハードウェアリセットボタン）
2. マスストレージデバイスとして認識される
3. `.uf2` ファイルをドライブにコピーする
4. 左右それぞれに対応するファームウェアを書き込む

---

## ファイル構成

```
config/
  keymap.keymap          ← ★ キーマップ定義（変更はここ）
  west.yml               ← 依存モジュールの定義
  info.json              ← キーボード情報

boards/shields/torabo_tsuki_lp/
  torabo_tsuki_lp.dtsi         ← ハードウェア共通定義（マトリクス、ピン配置等）
  torabo_tsuki_lp.keymap       ← config/keymap.keymap を include
  torabo_tsuki_lp_layouts.dtsi ← 物理レイアウト定義（S/M/L）
  torabo_tsuki_lp_left.overlay ← 左手固有設定（ポインティング反転等）
  torabo_tsuki_lp_right.overlay← 右手固有設定（列オフセット等）
  torabo_tsuki_lp_left.conf    ← 左手設定オプション
  torabo_tsuki_lp_right.conf   ← 右手設定オプション
  Kconfig.defconfig            ← Kconfig デフォルト値
  Kconfig.shield               ← Kconfig シールド定義
  torabo_tsuki_lp.zmk.yml      ← ZMK メタデータ

snippets/
  input-trackball/       ← トラックボール（PAW3222）有効化
  input-trackpad/        ← トラックパッド（IQS7211E）有効化
  input-trackpad-mini/   ← ミニトラックパッド有効化
  input-listener/        ← ポインティングリスナー有効化
  input-split/           ← 分割ポインティングデータ転送
  input-split-listener/  ← 分割リスナー有効化
  split-central/         ← Central ロール指定

src/
  board.c                      ← 省電力管理（4段階パワーモード）
  mini_trackpad_init_reg.c     ← トラックパッド初期化データ（217バイト）
  mini_trackpad_iqs7211e_init.h← トラックパッドレジスタ定義
```

---

## 依存モジュール

| モジュール | 説明 |
|-----------|------|
| `zmk` (v0.3) | ZMK ファームウェアコア |
| `zmk-component-bmp-boost` (v0.2) | BMP Boost ボードサポート |
| `zmk-feature-status-led` | ステータス LED 機能 |
| `zmk-driver-paw3222` (torabo-tsuki ブランチ) | PAW3222 トラックボールドライバー |
| `zmk-feature-cdc-acm-bootloader-trigger` (v0.2) | USB ブートローダートリガー |
| `zmk-feature-non-lipo-battery-management` | 非LiPo バッテリー管理 |

---

## 設定オプション一覧

`boards/shields/torabo_tsuki_lp/torabo_tsuki_lp_left.conf`（右側も同一内容）で設定されているオプション：

| 設定項目 | 値 | 説明 |
|----------|-----|------|
| `CONFIG_ZMK_STUDIO` | `y` | ZMK Studio 対応を有効化 |
| `CONFIG_ZMK_STUDIO_LOCKING` | `n` | Studio のロック機能を無効化 |
| `CONFIG_BT_CTLR_TX_PWR_PLUS_8` | `y` | BLE 送信出力を最大（+8dBm）に設定 |
| `CONFIG_ZMK_SPLIT_BLE_CENTRAL_BATTERY_LEVEL_PROXY` | `y` | Peripheral のバッテリーレベルを Central 経由で報告 |
| `CONFIG_ZMK_SPLIT_BLE_CENTRAL_BATTERY_LEVEL_FETCHING` | `y` | Central が Peripheral のバッテリーレベルを取得 |
| `CONFIG_ZMK_CDC_ACM_BOOTLOADER_TRIGGER` | `y` | USB CDC ACM ブートローダートリガーを有効化 |
| `CONFIG_ZMK_STATUS_LED` | `y` | ステータス LED を有効化 |
| `CONFIG_ZMK_IDLE_SLEEP_TIMEOUT` | `9000000` | アイドルスリープまでのタイムアウト（ミリ秒） |
| `CONFIG_ZMK_NON_LIPO_MIN_MV` | `1000` | 非LiPo バッテリーの最低電圧（mV） |
| `CONFIG_ZMK_NON_LIPO_LOW_MV` | `900` | 非LiPo バッテリーの低電圧警告閾値（mV） |
