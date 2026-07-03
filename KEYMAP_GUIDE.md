# Torabo Tsuki LP キーマップ設定ガイド

## 編集対象ファイル

キーマップの変更は **`config/keymap.keymap`** を編集します。

`boards/shields/torabo_tsuki_lp/torabo_tsuki_lp.keymap` はこのファイルを `#include` しているだけなので、直接編集する必要はありません。

---

## 現在のレイアウト

現在は **M サイズ（4行 × 片側6〜7列、計52キー）** で設定されています。

設定箇所: `boards/shields/torabo_tsuki_lp/torabo_tsuki_lp.dtsi`
```dts
zmk,physical-layout = &physical_layout_m;
```

レイアウトサイズを変更する場合は `&physical_layout_s`（S）/ `&physical_layout_m`（M）/ `&physical_layout_l`（L）に書き換え、キーマップの行数もそれに合わせて調整してください。

| サイズ | 行数 | キー数 | 特徴 |
|--------|------|--------|------|
| S | 4行 | 44キー | 両端列なし |
| M | 4行 | 52キー | 両端列あり |
| L | 5行 | 66キー | 最上段に数字行追加 |

---

## キーマップの構造

```dts
/ {
    combos { ... };    // コンボ（同時押し）定義
    keymap {
        layer_0 { ... };  // レイヤー0（ベース）
        layer_1 { ... };  // レイヤー1（ナビ・マウス・数字）
        layer_2 { ... };  // レイヤー2（記号・BT制御）
        layer_3 { ... };  // レイヤー3（Fn・マウス・ナビ）
        layer_4 { ... };  // レイヤー4（オートマウス）
        layer_5 { ... };  // レイヤー5（テンキー）
        layer_6 { ... };  // レイヤー6（予備）
    };
};
```

---

## 現在のキーマップ（Mサイズ）

### Layer 0 — ベースレイヤー（QWERTY）

```
┌──────┬──────┬──────┬──────┬──────┬──────┐                  ┌──────┬──────┬──────┬──────┬──────┬──────┐
│ TAB  │  Q   │  W   │  E   │  R   │  T   │                  │  Y   │  U   │  I   │  O   │  P   │  @   │
├──────┼──────┼──────┼──────┼──────┼──────┤                  ├──────┼──────┼──────┼──────┼──────┼──────┤
│ TAB  │  A   │  S   │  D   │  F   │  G   │                  │  H   │  J   │  K   │  L   │  ;   │  :   │
├──────┼──────┼──────┼──────┼──────┼──────┼──────┐    ┌──────┼──────┼──────┼──────┼──────┼──────┼──────┤
│LSHFT │  Z   │  X   │  C   │  V   │  B   │ L5+[ │    │ L6+] │  N   │  M   │  ,   │  .   │  /   │  ろ   │
├──────┼──────┼──────┼──────┼──────┼──────┼──────┤    ├──────┼──────┼──────┼──────┼──────┼──────┼──────┤
│LCTRL │RCTRL │ GUI  │ ALT  │L2+かな│L1+SPC│L3+英数│   │ BSPC │L1+ENT│ none │ none │  -   │  ^   │  ¥   │
└──────┴──────┴──────┴──────┴──────┴──────┴──────┘    └──────┴──────┴──────┴──────┴──────┴──────┴──────┘
```

- `L1+SPC`: ホールドで Layer 1（ナビ・マウス）、タップで Space
- `L1+ENT`: ホールドで Layer 1（ナビ・マウス）、タップで Enter
- `L2+かな`: ホールドで Layer 2（記号・BT制御）、タップでかな（LANGUAGE_2）
- `L3+英数`: ホールドで Layer 3（Fn・マウス・ナビ）、タップで英数（LANGUAGE_1）
- `L5+[`: ホールドで Layer 5（テンキー）、タップで `[`（US キーコード `RBKT`）
- `L6+]`: ホールドで Layer 6（マウス）、タップで `]`（US キーコード `BSLH`）
- `@`: JIS 配列での出力（US キーコード `LBKT`）
- `ろ`: JIS 固有キー `INT_RO`（`\` / `_`）
- `¥`: JIS 固有キー `INT_YEN`
- `-`, `^`: JIS 配列での出力（US キーコード `MINUS` / `EQUAL`）
- `none`: `&none`（何も割り当てなし、下位レイヤーも無効）

### Layer 1 — ナビゲーション・マウス・数字レイヤー

Layer 0 の左親指 `Space` または右親指 `Enter` の長押しで有効化。

```
┌──────┬──────┬──────┬──────┬──────┬──────┐                  ┌──────┬──────┬──────┬──────┬──────┬──────┐
│ ESC  │  1   │  2   │  3   │  4   │  5   │                  │  6   │  7   │  8   │  9   │  0   │ none │
├──────┼──────┼──────┼──────┼──────┼──────┤                  ├──────┼──────┼──────┼──────┼──────┼──────┤
│      │ none │ none │ none │ none │ none │                  │ PgUp │LCLK  │ none │RCLK  │MCLK  │ none │
├──────┼──────┼──────┼──────┼──────┼──────┼──────┐    ┌──────┼──────┼──────┼──────┼──────┼──────┼──────┤
│      │ none │ none │ none │ none │ none │ HOME │    │ END  │ PgDn │      │      │      │  ↑   │ none │
├──────┼──────┼──────┼──────┼──────┼──────┼──────┤    ├──────┼──────┼──────┼──────┼──────┼──────┼──────┤
│      │      │      │      │無変換 │ ■■■  │ 変換 │    │ DEL  │ ■■■  │      │      │  ←   │  ↓   │  →   │
└──────┴──────┴──────┴──────┴──────┴──────┴──────┘    └──────┴──────┴──────┴──────┴──────┴──────┴──────┘
```

- `■■■` = `&trans`（Layer 0 の `L1+Space` / `L1+Enter` がそのまま透過）
- 上段: ESC（左端）、数字 `1`〜`0`
- 中段右: PgUp、マウスクリック（LCLK、RCLK、MCLK）
- 下段右: PgDn、↑、HOME/END
- 最下段左: `無変換`（INT_MUHENKAN）、`変換`（INT_HENKAN）
- 最下段右: DEL、矢印キー（←↓→）
- `none` = `&none`（何も割り当てなし）

### Layer 2 — 記号・BT制御レイヤー（左親指 かな 長押し）

```
┌──────┬──────┬──────┬──────┬──────┬──────┐                  ┌──────┬──────┬──────┬──────┬──────┬──────┐
│      │ S+1  │ S+2  │ S+3  │ S+4  │ S+5  │                  │ S+6  │ S+7  │ S+8  │ S+9  │ S+0  │      │
├──────┼──────┼──────┼──────┼──────┼──────┤                  ├──────┼──────┼──────┼──────┼──────┼──────┤
│      │ BT0  │ BT1  │ BT2  │ BT3  │ BT4  │                  │      │      │      │      │      │      │
├──────┼──────┼──────┼──────┼──────┼──────┼──────┐    ┌──────┼──────┼──────┼──────┼──────┼──────┼──────┤
│      │      │      │ USB  │ BLE  │BT_NXT│      │    │      │      │      │      │      │      │      │
├──────┼──────┼──────┼──────┼──────┼──────┼──────┤    ├──────┼──────┼──────┼──────┼──────┼──────┼──────┤
│      │      │      │      │ ▓▓▓▓ │      │      │    │      │      │      │      │      │      │      │
└──────┴──────┴──────┴──────┴──────┴──────┴──────┘    └──────┴──────┴──────┴──────┴──────┴──────┴──────┘
```

`▓▓▓▓` = このキーの長押しでレイヤー有効化（レイヤーキー自体）。空欄はすべて `&trans`（Layer 0 透過）。

- 中段左: Bluetooth プロファイル選択（`BT_SEL 0`〜`BT_SEL 4`）
- 下段左: 出力先切替（`OUT_USB` / `OUT_BLE`）、`BT_NXT`

**コンボ**: このレイヤーでキーポジション 16 + 17（BT3 + BT4）を同時押し → **BT_CLR**（Bluetooth クリア）

`S+` は Shift の略。JIS 配列での実際の出力:

| 表記 | ZMK キーコード | JIS 出力 |
|------|---------------|----------|
| S+1 | `LS(N1)` | `!` |
| S+2 | `LS(N2)` | `"` |
| S+3 | `LS(N3)` | `#` |
| S+4 | `LS(N4)` | `$` |
| S+5 | `LS(N5)` | `%` |
| S+6 | `LS(N6)` | `&` |
| S+7 | `LS(N7)` | `'` |
| S+8 | `LS(N8)` | `(` |
| S+9 | `LS(N9)` | `)` |
| S+0 | `LS(N0)` | *(なし)* |

### Layer 3 — ファンクション・マウス・ナビレイヤー（左親指 英数 長押し）

```
┌──────┬──────┬──────┬──────┬──────┬──────┐                  ┌──────┬──────┬──────┬──────┬──────┬──────┐
│ ESC  │  F1  │  F2  │  F3  │  F4  │  F5  │                  │  F6  │  F7  │  F8  │  F9  │ F10  │ F11  │
├──────┼──────┼──────┼──────┼──────┼──────┤                  ├──────┼──────┼──────┼──────┼──────┼──────┤
│      │ none │ none │ none │ none │ none │                  │ PgUp │LCLK  │  ↑   │RCLK  │MCLK  │ F12  │
├──────┼──────┼──────┼──────┼──────┼──────┼──────┐    ┌──────┼──────┼──────┼──────┼──────┼──────┼──────┤
│      │ none │ none │ none │ none │ none │ HOME │    │ END  │ PgDn │  ←   │  ↓   │  →   │  ↑   │ none │
├──────┼──────┼──────┼──────┼──────┼──────┼──────┤    ├──────┼──────┼──────┼──────┼──────┼──────┼──────┤
│      │      │      │      │      │      │ ▓▓▓▓ │    │ MB4  │ MB5  │      │      │  ←   │  ↓   │  →   │
└──────┴──────┴──────┴──────┴──────┴──────┴──────┘    └──────┴──────┴──────┴──────┴──────┴──────┴──────┘
```

- `▓▓▓▓` = このキーの長押しでレイヤー有効化（レイヤーキー自体）
- 上段: ESC（左端）、ファンクションキー `F1`～`F5`（左）/ `F6`～`F11`（右）
- 中段右: PgUp、マウスクリック（LCLK、RCLK、MCLK）、↑、`F12`
- 下段: `HOME` / `END`、PgDn、←↓→↑
- 最下段右: `MB4`（戻る）、`MB5`（進む）、矢印キー（←↓→）
- `none` = `&none`、空欄 = `&trans`（Layer 0 透過）

### Layer 4 — オートマウスレイヤー

トラックボール操作時に `zip_temp_layer` で自動的に有効化されるレイヤーです（設定方法は後述の「オートマウスレイヤー」セクションを参照）。

```
┌──────┬──────┬──────┬──────┬──────┬──────┐                  ┌──────┬──────┬──────┬──────┬──────┬──────┐
│      │ none │ none │ none │ none │ none │                  │ none │ none │ none │ none │ none │ none │
├──────┼──────┼──────┼──────┼──────┼──────┤                  ├──────┼──────┼──────┼──────┼──────┼──────┤
│      │ none │ none │ none │ none │ none │                  │ none │LCLK  │ none │RCLK  │MCLK  │ none │
├──────┼──────┼──────┼──────┼──────┼──────┼──────┐    ┌──────┼──────┼──────┼──────┼──────┼──────┼──────┤
│      │ none │ none │ none │ none │ none │      │    │      │ none │ none │ none │ none │ none │ none │
├──────┼──────┼──────┼──────┼──────┼──────┼──────┤    ├──────┼──────┼──────┼──────┼──────┼──────┼──────┤
│      │      │      │      │      │      │      │    │      │      │      │      │ none │ none │ none │
└──────┴──────┴──────┴──────┴──────┴──────┴──────┘    └──────┴──────┴──────┴──────┴──────┴──────┴──────┘
```

- 中段右: マウスクリック（LCLK、RCLK、MCLK）
- 最下段: 左側の修飾キー・レイヤーキーは `&trans`（Layer 0 透過）で通常のキー入力を維持
- それ以外は `&none`（マウス操作中に不要なキーをブロック）

### Layer 5 — テンキーレイヤー

Layer 0 の左手 `[` キー（`L5+[`）の長押しで有効化。右手側にテンキー配列を提供。

```
┌──────┬──────┬──────┬──────┬──────┬──────┐                  ┌──────┬──────┬──────┬──────┬──────┬──────┐
│      │      │      │      │      │      │                  │      │  7   │  8   │  9   │ KP-  │      │
├──────┼──────┼──────┼──────┼──────┼──────┤                  ├──────┼──────┼──────┼──────┼──────┼──────┤
│      │      │      │      │      │      │                  │      │  4   │  5   │  6   │ KP+  │      │
├──────┼──────┼──────┼──────┼──────┼──────┼──────┐    ┌──────┼──────┼──────┼──────┼──────┼──────┼──────┤
│      │      │      │      │      │      │ ▓▓▓▓ │    │      │      │  1   │  2   │  3   │ KP/  │      │
├──────┼──────┼──────┼──────┼──────┼──────┼──────┤    ├──────┼──────┼──────┼──────┼──────┼──────┼──────┤
│      │      │      │      │      │      │      │    │      │  0   │      │      │  .   │ KP*  │      │
└──────┴──────┴──────┴──────┴──────┴──────┴──────┘    └──────┴──────┴──────┴──────┴──────┴──────┴──────┘
```

- `▓▓▓▓` = このキーの長押しでレイヤー有効化（レイヤーキー自体）
- 右側: テンキー配列（`7` `8` `9` / `4` `5` `6` / `1` `2` `3` / `0` `.`）
- 右側演算子: `KP-`（マイナス）、`KP+`（プラス）、`KP/`（除算）、`KP*`（乗算）
- 空欄はすべて `&trans`（Layer 0 透過）

### Layer 6 — マウスレイヤー

Layer 0 の右手 `]` キー（`L6+]`）の長押しで有効化。右手側にマウス操作を提供
右手だけでマウス操作を完結したい場合に使用する

```
┌──────┬──────┬──────┬──────┬──────┬──────┐                  ┌──────┬──────┬──────┬──────┬──────┬──────┐
│      │ none │ none │ none │ none │ none │                  │ none │ none │ none │ none │ none │ none │
├──────┼──────┼──────┼──────┼──────┼──────┤                  ├──────┼──────┼──────┼──────┼──────┼──────┤
│      │ none │ none │ none │ none │ none │                  │ none │LCLK  │ none │RCLK  │MCLK  │ none │
├──────┼──────┼──────┼──────┼──────┼──────┼──────┐    ┌──────┼──────┼──────┼──────┼──────┼──────┼──────┤
│      │ none │ none │ none │ none │ none │      │    │ ▓▓▓▓ │ none │ none │ none │ none │ none │ none │
├──────┼──────┼──────┼──────┼──────┼──────┼──────┤    ├──────┼──────┼──────┼──────┼──────┼──────┼──────┤
│      │      │      │      │      │      │      │    │ MB4  │ MB5  │      │      │ none │ none │ none │
└──────┴──────┴──────┴──────┴──────┴──────┴──────┘    └──────┴──────┴──────┴──────┴──────┴──────┴──────┘
```

- `▓▓▓▓` = このキーの長押しでレイヤー有効化（レイヤーキー自体）
- 中段右: マウスクリック（LCLK、RCLK、MCLK）
- 最下段左側: `&trans`（Layer 0 透過）で修飾キー・レイヤーキーを維持
- 最下段右: `MB4`（戻る）、`MB5`（進む）
- それ以外は `&none`（何も割り当てなし）

---

## キーポジション番号（Mサイズ）

キーマップやコンボの設定で使用するキーポジション番号です。左上から右方向に 0 から順番に振られます。

```
 0   1   2   3   4   5               6   7   8   9  10  11
12  13  14  15  16  17              18  19  20  21  22  23
24  25  26  27  28  29  30      31  32  33  34  35  36  37
38  39  40  41  42  43  44      45  46  47  48  49  50  51
```

---

## キーバインディングの書式

### 基本キー入力

```dts
&kp KEY_NAME
```

| 例 | 説明 |
|---|---|
| `&kp A` | A キー |
| `&kp N1` | 数字 1 |
| `&kp LSHFT` | 左 Shift |
| `&kp SEMI` | セミコロン `;` |
| `&kp SQT` | シングルクォート `'` |
| `&kp FSLH` | スラッシュ `/` |
| `&kp BSPC` | Backspace |
| `&kp ESC` | Escape |
| `&kp SPACE` | スペース |
| `&kp RET` | Enter |

修飾キー付き（Shift+キー）:
```dts
&kp LS(KEY)    // Left Shift + KEY
&kp LC(KEY)    // Left Ctrl + KEY
&kp LA(KEY)    // Left Alt + KEY
&kp LG(KEY)    // Left GUI (Win/Cmd) + KEY
```

### レイヤータップ（`&lt`）

ホールドでレイヤー切替、タップでキー入力。

```dts
&lt LAYER_NUM KEY_NAME
```

例:
```dts
&lt 2 SPACE    // 長押しで Layer 2、タップで Space
&lt 3 ENTER    // 長押しで Layer 3、タップで Enter
```

### モッドタップ（`&mt`）

ホールドで修飾キー、タップでキー入力。

```dts
&mt MODIFIER KEY_NAME
```

例:
```dts
&mt LSHIFT LANGUAGE_1  // 長押しで左Shift、タップで英数
&mt RSHIFT LANGUAGE_2  // 長押しで右Shift、タップでかな
&mt RIGHT_SHIFT FSLH   // 長押しで右Shift、タップで /
```

### マウスボタン（`&mkp`）

```dts
&mkp LCLK    // 左クリック（MB1）
&mkp RCLK    // 右クリック（MB2）
&mkp MCLK    // 中クリック（MB3）
&mkp MB4     // マウスボタン4（戻る）
&mkp MB5     // マウスボタン5（進む）
```

### 透過（`&trans`）

現在のレイヤーでは何もせず、下位レイヤーの動作をそのまま使用。

```dts
&trans
```

### なし（`&none`）

何も割り当てない（下位レイヤーも無効）。

```dts
&none
```

### Bluetooth 操作（`&bt`）

```dts
&bt BT_CLR     // ペアリング情報をクリア
&bt BT_NXT     // 次のプロファイルに切替
&bt BT_PRV     // 前のプロファイルに切替
&bt BT_SEL 0   // プロファイル 0 を選択（0〜4）
```

### 出力先切替（`&out`）

```dts
&out OUT_USB   // USB 出力に切替
&out OUT_BLE   // Bluetooth 出力に切替
&out OUT_TOG   // USB/BLE をトグル
```

---

## コンボの設定

複数キーの同時押しで特定の動作を発動させます。

```dts
combos {
    compatible = "zmk,combos";

    combo_name {
        bindings = <&動作>;           // 発動する動作
        key-positions = <位置1 位置2>; // 同時押しするキーの位置番号
        layers = <レイヤー番号>;       // 有効なレイヤー（省略で全レイヤー）
        timeout-ms = <50>;            // 同時押し判定時間（省略時デフォルト）
    };
};
```

例: Layer 2 でポジション 16+17 を同時押しすると Bluetooth クリア
```dts
bt_clear {
    bindings = <&bt BT_CLR>;
    key-positions = <16 17>;
    layers = <2>;
};
```

---

## レイヤーの追加方法

`keymap` ブロック内に新しいレイヤーを追加します。M サイズでは各レイヤーに 52 個のキーバインディングが必要です。

```dts
        layer_4 {
            bindings = <
&trans  &trans  &trans  &trans  &trans  &trans                  &trans  &trans  &trans  &trans  &trans  &trans
&trans  &trans  &trans  &trans  &trans  &trans                  &trans  &trans  &trans  &trans  &trans  &trans
&trans  &trans  &trans  &trans  &trans  &trans  &trans  &trans  &trans  &trans  &trans  &trans  &trans  &trans
&trans  &trans  &trans  &trans  &trans  &trans  &trans  &trans  &trans  &trans  &trans  &trans  &trans  &trans
            >;
        };
```

追加後、既存レイヤーのキーに `&lt 4 KEY` や `&mo 4` などを設定して切替手段を用意してください。

---

## キーマップ変更の手順

### ファイル編集による変更

1. `config/keymap.keymap` をテキストエディタで開く
2. 変更したいレイヤーの `bindings` を編集する
3. ファイルを保存する
4. ファームウェアをリビルドし、キーボードに書き込む

### ZMK Studio による変更（リビルド不要）

1. キーボードを USB で PC に接続
2. [ZMK Studio](https://zmk.studio/) にブラウザでアクセス
3. デバイスを選択してキーマップを GUI で編集
4. 変更は即座にキーボードに反映される

---

## よく使うキー名一覧

### アルファベット・数字
`A`〜`Z`, `N0`〜`N9`

### 修飾キー
| キー名 | 説明 |
|--------|------|
| `LSHFT` / `RSHFT` | 左/右 Shift |
| `LCTRL` / `RCTRL` | 左/右 Ctrl |
| `LALT` / `RALT` | 左/右 Alt |
| `LGUI` / `RGUI` | 左/右 GUI (Win/Cmd) |

### 特殊キー
| キー名 | 説明 |
|--------|------|
| `ESC` | Escape |
| `RET` / `ENTER` | Enter |
| `SPACE` | スペース |
| `TAB` | Tab |
| `BSPC` | Backspace |
| `DEL` | Delete |
| `CAPS` | Caps Lock |
| `PSCRN` | Print Screen |
| `SLCK` | Scroll Lock |
| `PAUSE_BREAK` | Pause/Break |
| `INS` | Insert |

### ナビゲーション
| キー名 | 説明 |
|--------|------|
| `UP` / `DOWN` / `LEFT` / `RIGHT` | 矢印キー |
| `HOME` / `END` | Home / End |
| `PG_UP` / `PG_DN` | Page Up / Page Down |

### ファンクションキー
`F1`〜`F24`

### 記号
| キー名 | 記号 |
|--------|------|
| `MINUS` | `-` |
| `EQUAL` | `=` |
| `LBKT` / `LEFT_BRACKET` | `[` |
| `RBKT` / `RIGHT_BRACKET` | `]` |
| `BSLH` / `BACKSLASH` | `\` |
| `SEMI` | `;` |
| `SQT` / `SINGLE_QUOTE` | `'` |
| `GRAVE` | `` ` `` |
| `COMMA` | `,` |
| `DOT` | `.` |
| `FSLH` | `/` |
| `TILDE` | `~`（`LS(GRAVE)` でも可） |

### 日本語入力
| キー名 | 説明 |
|--------|------|
| `LANGUAGE_1` | 英数 / IME OFF |
| `LANGUAGE_2` | かな / IME ON |
| `INT_MUHENKAN` | 無変換 |
| `INT_HENKAN` | 変換 |
| `INT_KATAKANAHIRAGANA` | カタカナ/ひらがな |

完全なキー名の一覧は [ZMK 公式ドキュメント](https://zmk.dev/docs/keymaps/list-of-keycodes) を参照してください。

---

## 日本語配列（JIS）での記号入力

### 重要な前提

ZMK は **HID キーコード（US 配列の物理位置に基づく）** を送信します。OS 側で日本語 (JIS) 配列が設定されている場合、**同じキーコードでも US 配列とは異なる文字が出力されます**。

つまり、ZMK のキー名は US 配列の名前ですが、実際に画面に表示される文字は OS の配列設定に従います。

### 数字キー（JIS 配列での出力）

数字自体は US/JIS で同じですが、**Shift との組み合わせが異なります**。

| ZMK キーコード | 単体 | Shift (JIS) | Shift (US) |
|---------------|------|------------|------------|
| `N1` | `1` | `!` | `!` |
| `N2` | `2` | `"` | `@` |
| `N3` | `3` | `#` | `#` |
| `N4` | `4` | `$` | `$` |
| `N5` | `5` | `%` | `%` |
| `N6` | `6` | `&` | `^` |
| `N7` | `7` | `'` | `&` |
| `N8` | `8` | `(` | `*` |
| `N9` | `9` | `)` | `)` |
| `N0` | `0` | *(なし)* | `)` |

> **注意**: `LS(N2)` は US配列なら `@` ですが、JIS配列では `"` になります。

### 記号キー対応表（JIS 配列での出力）

ZMK のキー名は US 配列に基づいていますが、OS が JIS の場合は右列の文字が出力されます。

| ZMK キーコード | US 出力 | JIS 出力（単体） | JIS 出力（Shift） |
|---------------|---------|-----------------|------------------|
| `GRAVE` | `` ` `` | 半角/全角 | 半角/全角 |
| `MINUS` | `-` | `-` | `=` |
| `EQUAL` | `=` | `^` | `~` |
| `LBKT` | `[` | `@` | `` ` `` |
| `RBKT` | `]` | `[` | `{` |
| `BSLH` | `\` | `]` | `}` |
| `SEMI` | `;` | `;` | `+` |
| `SQT` | `'` | `:` | `*` |
| `COMMA` | `,` | `,` | `<` |
| `DOT` | `.` | `.` | `>` |
| `FSLH` | `/` | `/` | `?` |

### JIS で特定の記号を入力するには

JIS 配列で目的の記号を出すために、どの ZMK キーコードを使えばよいかの逆引き表です。

| 入力したい記号 | ZMK での指定方法 | 備考 |
|---------------|-----------------|------|
| `@` | `&kp LBKT` | US の `[` 位置 |
| `[` | `&kp RBKT` | US の `]` 位置 |
| `]` | `&kp BSLH` | US の `\` 位置 |
| `{` | `&kp LS(RBKT)` | US の `]` + Shift |
| `}` | `&kp LS(BSLH)` | US の `\` + Shift |
| `^` | `&kp EQUAL` | US の `=` 位置 |
| `~` | `&kp LS(EQUAL)` | US の `=` + Shift |
| `:` | `&kp SQT` | US の `'` 位置 |
| `;` | `&kp SEMI` | US/JIS 同じ |
| `+` | `&kp LS(SEMI)` | US の `;` + Shift |
| `*` | `&kp LS(SQT)` | US の `'` + Shift |
| `=` | `&kp LS(MINUS)` | US の `-` + Shift |
| `"` | `&kp LS(N2)` | Shift+2 |
| `&` | `&kp LS(N6)` | Shift+6 |
| `'` | `&kp LS(N7)` | Shift+7 |
| `(` | `&kp LS(N8)` | Shift+8 |
| `)` | `&kp LS(N9)` | Shift+9 |
| `!` | `&kp LS(N1)` | Shift+1 |
| `#` | `&kp LS(N3)` | Shift+3 |
| `$` | `&kp LS(N4)` | Shift+4 |
| `%` | `&kp LS(N5)` | Shift+5 |
| `¥` | `&kp INT_YEN` | JIS 固有キー |
| `\` | `&kp INT_RO` | JIS 固有キー（ろ） |
| `_` | `&kp LS(INT_RO)` | ろ + Shift |
| `\|` | `&kp LS(INT_YEN)` | ¥ + Shift |
| `` ` `` | `&kp LS(LBKT)` | US の `[` + Shift |
| `-` | `&kp MINUS` | US/JIS 同じ |
| `/` | `&kp FSLH` | US/JIS 同じ |
| `?` | `&kp LS(FSLH)` | US/JIS 同じ |
| `,` | `&kp COMMA` | US/JIS 同じ |
| `.` | `&kp DOT` | US/JIS 同じ |
| `<` | `&kp LS(COMMA)` | US/JIS 同じ |
| `>` | `&kp LS(DOT)` | US/JIS 同じ |

### JIS 固有キー

JIS 配列にのみ存在する物理キーに対応するキーコードです。

| ZMK キーコード | 別名 | 説明 |
|---------------|------|------|
| `INT_RO` | `INT1` | `ろ` キー（`\` / `_`） |
| `INT_KATAKANAHIRAGANA` | `INT2` | カタカナ/ひらがなキー |
| `INT_YEN` | `INT3` | `¥` キー（`¥` / `\|`） |
| `INT_HENKAN` | `INT4` | 変換キー |
| `INT_MUHENKAN` | `INT5` | 無変換キー |
| `LANGUAGE_1` | `LANG1` | 英数（IME OFF） |
| `LANGUAGE_2` | `LANG2` | かな（IME ON） |

### 現在のキーマップと JIS での実際の出力

現在の Layer 2 の記号が JIS 配列でどう出力されるかの対応です。

| キーマップの記述 | US での出力 | JIS での実際の出力 |
|-----------------|------------|-------------------|
| `LS(N1)` | `!` | `!` |
| `LS(N2)` | `@` | `"` |
| `LS(N3)` | `#` | `#` |
| `LS(N4)` | `$` | `$` |
| `LS(N5)` | `%` | `%` |
| `LS(N6)` | `^` | `&` |
| `LS(N7)` | `&` | `'` |
| `LS(N8)` | `*` | `(` |
| `LS(N9)` | `(` | `)` |
| `LS(N0)` | `)` | *(なし)* |

> **ヒント**: JIS配列で使用する場合は、上の「JIS で特定の記号を入力するには」の逆引き表を参考にキーマップの記号を修正してください。OS を US 配列に設定して使う場合はそのままで問題ありません。

---

## トラックボールのスクロールモード設定

### 現在の構成

トラックボール（PAW3222）は現在 **カーソル移動モード** のみで動作しています。

```
PAW3222 (SPI) → pointing_listener → zip_xy_transform（軸反転）→ カーソル移動
```

**関連ファイル:**

| ファイル | 役割 |
|---------|------|
| `snippets/input-trackball/input-trackball.overlay` | PAW3222 ドライバ有効化 |
| `snippets/input-listener/input-listener.overlay` | リスナー有効化 + デバイス接続 |
| `boards/shields/.../torabo_tsuki_lp_left.overlay` | 左側: 軸反転設定 |
| `boards/shields/.../torabo_tsuki_lp_right.overlay` | 右側: 軸反転設定 |

### レイヤーベースのスクロールモード設定

ZMK の `zmk,input-listener` は **レイヤーごとに input-processors を切り替え** できます。特定のレイヤーが有効な間だけ、トラックボールをスクロールモードに変更できます。

#### 設定方法

左側の overlay ファイル `boards/shields/torabo_tsuki_lp/torabo_tsuki_lp_left.overlay` を編集します:

**変更前:**
```dts
#include "torabo_tsuki_lp.dtsi"
#include <input/processors.dtsi>
#include <dt-bindings/zmk/input_transform.h>

&pointing_listener {
    input-processors = <&zip_xy_transform (INPUT_TRANSFORM_X_INVERT | INPUT_TRANSFORM_Y_INVERT)>;
};
```

**変更後（例: Layer 1 でスクロール）:**
```dts
#include "torabo_tsuki_lp.dtsi"
#include <input/processors.dtsi>
#include <dt-bindings/zmk/input_transform.h>

&pointing_listener {
    // デフォルト: カーソル移動（軸反転）
    input-processors = <&zip_xy_transform (INPUT_TRANSFORM_X_INVERT | INPUT_TRANSFORM_Y_INVERT)>;

    // Layer 1 有効時: スクロールモード
    scroll_mode {
        layers = <1>;
        input-processors = <&zip_xy_transform (INPUT_TRANSFORM_X_INVERT | INPUT_TRANSFORM_Y_INVERT)
                            &zip_xy_to_scroll_mapper>;
    };
};
```

右側の overlay `torabo_tsuki_lp_right.overlay` にも同様の `scroll_mode` 子ノードを追加してください（右側にトラックボールがある場合）。

#### ダブルトラックボール構成の場合

`snippets/input-split-listener/input-split-listener.overlay` にも `scroll_mode` を追加します:

```dts
&pointing_device_split_listener {
    // 既存の input-processors の後に追加
    scroll_mode {
        layers = <1>;
        input-processors = <&zip_xy_transform (INPUT_TRANSFORM_X_INVERT | INPUT_TRANSFORM_Y_INVERT)
                            &zip_xy_to_scroll_mapper>;
    };
};
```

#### 動作イメージ

現在のキーマップでは、Layer 1 は `Space`（左親指）または `Enter`（右親指）の長押しで有効化されます。

- **通常時**: トラックボール = カーソル移動
- **Space / Enter 長押し中**: トラックボール = スクロール

#### スクロール速度の調整

スクロールが速すぎる / 遅すぎる場合は `zip_xy_scaler` で倍率を調整できます:

```dts
scroll_mode {
    layers = <1>;
    input-processors = <&zip_xy_transform (INPUT_TRANSFORM_X_INVERT | INPUT_TRANSFORM_Y_INVERT)
                        &zip_xy_scaler 1 4     // 移動量を 1/4 に縮小
                        &zip_xy_to_scroll_mapper>;
};
```

`&zip_xy_scaler N D` は移動量を `N/D` 倍にします（例: `1 8` で 1/8 倍、`1 2` で 1/2 倍）。

#### 複数レイヤーでスクロール

複数のレイヤーでスクロールを有効にする場合:

```dts
scroll_mode {
    layers = <1 3>;   // Layer 1 と Layer 3 の両方でスクロール
    input-processors = <&zip_xy_transform (INPUT_TRANSFORM_X_INVERT | INPUT_TRANSFORM_Y_INVERT)
                        &zip_xy_to_scroll_mapper>;
};
```

#### 主な input-processors

| プロセッサ | 機能 |
|-----------|------|
| `zip_xy_transform` | XY 軸の反転・入れ替え |
| `zip_xy_to_scroll_mapper` | XY 移動をスクロールイベントに変換 |
| `zip_xy_scaler` | 移動量の倍率変更（`N D` で N/D 倍） |
| `zip_temp_layer` | ポインター使用中にレイヤーを一時有効化 |

---

## オートマウスレイヤー（Auto Mouse Layer）

### 概要

トラックボールを動かすと **自動的にマウスボタンレイヤーが有効化** され、一定時間操作が止まると自動で解除される機能です。ZMK の `zip_temp_layer` input-processor で実現します。

これにより、マウスボタン（LCLK / RCLK など）を使うために手動でレイヤーキーを押す必要がなくなります。

### 動作の流れ

1. トラックボールを動かす → 指定レイヤーが**自動的にアクティブ化**
2. マウスボタン（LCLK 等）が使えるようになる
3. ポインターが一定時間（タイムアウト）停止 → レイヤーが**自動で解除**
4. マウスボタンを押している間はタイマーがリセットされ、レイヤーは維持される

### 設定方法

`boards/shields/torabo_tsuki_lp/torabo_tsuki_lp_left.overlay` の `input-processors` に `zip_temp_layer` を追加します。

#### 構文

```dts
&zip_temp_layer LAYER_NUM TIMEOUT_MS
```

| パラメータ | 説明 |
|-----------|------|
| `LAYER_NUM` | 一時的に有効化するレイヤー番号 |
| `TIMEOUT_MS` | ポインター停止後にレイヤーを解除するまでの時間（ミリ秒） |

#### 設定例: Layer 1 をオートマウスレイヤーにする

現在の Layer 1 には既に LCLK / RCLK / MCLK / MB4 / MB5 が配置されているため、そのまま利用できます。

**変更前:**
```dts
&pointing_listener {
    input-processors = <&zip_xy_transform (INPUT_TRANSFORM_X_INVERT | INPUT_TRANSFORM_Y_INVERT)>;

    scroll_mode {
        layers = <3>;
        input-processors = <&zip_xy_transform (INPUT_TRANSFORM_X_INVERT | INPUT_TRANSFORM_Y_INVERT)
                            &zip_xy_to_scroll_mapper>;
    };
};
```

**変更後:**
```dts
&pointing_listener {
    input-processors = <&zip_xy_transform (INPUT_TRANSFORM_X_INVERT | INPUT_TRANSFORM_Y_INVERT)
                        &zip_temp_layer 1 500>;   // トラックボール操作で Layer 1 を自動有効化（500ms でタイムアウト）

    scroll_mode {
        layers = <3>;
        input-processors = <&zip_xy_transform (INPUT_TRANSFORM_X_INVERT | INPUT_TRANSFORM_Y_INVERT)
                            &zip_xy_to_scroll_mapper>;
    };
};
```

右側の overlay `torabo_tsuki_lp_right.overlay` にも同様の変更が必要です。ダブルトラックボール構成では `input-split-listener.overlay` にも追加してください。

#### プロセッサの順序

`input-processors` のチェーンは**記述順に実行**されます。`zip_temp_layer` は `zip_xy_transform`（軸反転）の**後**に配置してください:

```dts
input-processors = <&zip_xy_transform (...)   // 1. まず軸反転
                    &zip_temp_layer 1 500>;    // 2. その後レイヤー切替
```

### タイムアウト値の目安

| タイムアウト | 体感 |
|-------------|------|
| `250` | 素早く解除（誤爆しにくい） |
| `500` | バランス型（推奨） |
| `1000` | ゆっくり解除（クリック操作に余裕あり） |
| `2000` | かなり長め（連続クリック作業向き） |

### 専用マウスレイヤーを新設する場合

現在の Layer 4 がオートマウス専用レイヤーとして既に定義されています。マウスボタン（LCLK / RCLK / MCLK / MB4 / MB5）のみを配置し、それ以外は `&none` または `&trans` で不要なキーをブロックしています。

overlay の `zip_temp_layer` を Layer 4 に設定することで有効化できます:

```dts
input-processors = <&zip_xy_transform (INPUT_TRANSFORM_X_INVERT | INPUT_TRANSFORM_Y_INVERT)
                    &zip_temp_layer 4 500>;
```

### 注意事項

- `zip_temp_layer` は `<input/processors.dtsi>` で定義されており、左右の overlay では既にインクルード済みです
- `&lt 1 SPACE` 等のレイヤータップでの手動切替は、オートマウスレイヤーと併用できます
- オートマウスレイヤーが有効な間に手動でレイヤーを切り替えると、手動側が優先されます
-  設定変更にはファームウェアの**再ビルドが必要**です

---

## トラックボールの CPI（感度）設定

### デフォルト CPI

PAW3222 センサーの `res-cpi` プロパティが未設定の場合、**ハードウェアデフォルトの 800 CPI** で動作します。

### CPI の設定方法

`snippets/input-trackball/input-trackball.overlay` の `pointing_device` ノードに `res-cpi` プロパティを追加します:

```dts
pointing_device: pointing_device@0 {
    compatible = "pixart,paw3222";
    reg = <0>;
    spi-max-frequency = <2000000>;
    irq-gpios = <&gpio0 19 GPIO_ACTIVE_LOW>;
    power-gpios = <&gpio0 8 (GPIO_ACTIVE_HIGH | NRF_GPIO_DRIVE_H0H1)>;
    res-cpi = <608>;   // CPI を指定（38 の倍数、608〜4826）
};
```

### 設定可能範囲

| 項目 | 値 |
|------|-----|
| デフォルト | 800 CPI（`res-cpi` 未設定時） |
| 最小値 | 608 CPI（レジスタ値 16 × 38） |
| 最大値 | 4826 CPI（レジスタ値 127 × 38） |
| 刻み幅 | **38 CPI 単位**（38 の倍数のみ有効） |

> **注意**: 400 CPI など 608 未満の値はドライバの下限制限により設定できません。

### よく使う CPI 設定値

| res-cpi | 実際の CPI | 備考 |
|---------|-----------|------|
| （未設定） | 800 | ハードウェアデフォルト |
| `<608>` | 608 | 設定可能な最低値 |
| `<798>` | 798 | デフォルト付近 |
| `<1216>` | 1216 | やや高速 |
| `<1520>` | 1520 | 高速 |
| `<4826>` | 4826 | 最大値 |

### 低 CPI（400 DPI 相当）を実現するには

ドライバの下限（608 CPI）より低い値は直接設定できないため、`zip_xy_scaler` と組み合わせて実効 CPI を下げます。

**方法 A: デフォルト 800 CPI + スケーラー**（overlay のみ変更）

```dts
&pointing_listener {
    input-processors = <&zip_xy_transform (INPUT_TRANSFORM_X_INVERT | INPUT_TRANSFORM_Y_INVERT)
                        &zip_xy_scaler 1 2>;   // 800 × 1/2 = 実効 400 CPI
};
```

**方法 B: 最低 608 CPI + スケーラー**（より精密）

```dts
// input-trackball.overlay で res-cpi = <608>; を設定した上で:
&pointing_listener {
    input-processors = <&zip_xy_transform (INPUT_TRANSFORM_X_INVERT | INPUT_TRANSFORM_Y_INVERT)
                        &zip_xy_scaler 2 3>;   // 608 × 2/3 ≈ 実効 405 CPI
};
```

### CPI と zip_xy_scaler の組み合わせ早見表

| res-cpi | scaler | 実効 CPI |
|---------|--------|---------|
| 800（既定） | なし | 800 |
| 800 | `1 2` | 400 |
| 800 | `1 4` | 200 |
| 608 | なし | 608 |
| 608 | `2 3` | ~405 |
| 1216 | `1 2` | 608 |
| 1520 | `1 2` | 760 |

> **注意**: CPI の変更および `zip_xy_scaler` の値はすべて **ファームウェアの再ビルドが必要** です。キー操作による動的な CPI 変更はできません。レイヤーごとに異なるスケーラーを設定することで、擬似的な速度切替は可能です（前述の「レイヤーベースのスクロールモード設定」を参照）。
