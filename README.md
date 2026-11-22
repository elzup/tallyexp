# tallyexp

[![ooparts](https://raw.githubusercontent.com/elzup/ooparts-spec/main/badge.svg)](https://github.com/elzup/ooparts-spec)

[Interactive demo](https://elzup.github.io/tallyexp/)

数値を数字ではなく、記号の数や並びで表すための表現規格 (draft)。

```
★★★☆☆      { value: 3, max: 5, kind: "rating" }
正正下      { value: 13, base: 5, mode: "group" }
●○●● ○○●○  { value: 0xB2, base: 16, mode: "positional" }
```

名前はタリー (数え印、一進法) と、regexp に倣った `-exp` (記法の規格) から。

## 表現レベル

param が正本で、ascii / unicode / 図は param を描いた結果として扱う。

```
value ──[分解]──> 中間表現 (param) ──[描画]──> ascii / unicode / 図
```

コアは分解 (基数変換、満ちた数と空きの数、割合の丸め、束ね方) と中間表現。描画は差し替えられる候補として提供する。

| レベル | 中身 | 例 (5 段階評価の 3) |
| --- | --- | --- |
| param | 記号を決めずパラメータだけを持つ。記号は描画側が選ぶ | `{ value: 3, max: 5, kind: "rating" }` |
| ascii | ASCII 文字だけで描く | `***--` |
| unicode | Unicode の記号で描く | `★★★☆☆` |
| 図 | SVG などの図で描く | 星 5 個のうち 3 個を塗った SVG |

## 用途と方針

想定する用途は、単純な量 (1〜10000)、時間量、距離量、指数的に広い範囲の量。

分かりやすさを最優先し、横幅は描画側の制約 (`width`) として扱う。mode を省略したときは、幅に収まる範囲で最も分かりやすい mode を選ぶ。

## 分解の方式 (mode)

| mode | 分解 | 向いている用途 |
| --- | --- | --- |
| `group` | base 個ずつ束ねる。束の束も可 | 小さな数を数える (正の字)、ローマ数字 (加算型) |
| `positional` | 基数ごとの桁 | 量 (10 進)、ビット (2 / 16 進) |
| `log` | 桁数 (+ 仮数を割合で) | 指数的に広い範囲、オーダーの比較 |
| (base 無し) | 束ねない一進法 (`\|\|\|`) | ごく小さな数 |

`base` は group / positional とも配列 (混合基数) を受け付ける。時間 `[24, 60, 60]`、距離 `[1000]`、ローマ数字 `[5, 2, 5, 2, 5, 2]` など。group と positional は分解の仕方が共通で、違いは段の中身の描き方だけ (group は束の数、positional はさらに桁に分ける)。

| base | mode | 値 | ascii | unicode |
| --- | --- | --- | --- | --- |
| `5` | group | 13 | `\|\|\|\|/ \|\|\|\|/ \|\|\|` | 正正下 |
| `[5, 5]` | group | 63 | | ▣▣ 正正 下 |
| `10` | positional | 237 | `\|\| \|\|\| \|\|\|\|\|\|\|` | (算木・そろばん風) |
| `16` | positional | 0xB2 | `#.## ..#.` | ●○●● ○○●○ |
| `[24, 60, 60]` | group | 2h35m | | ●● 正正正正正正正 |
| `10` | log | 3725 | | ▮▮▮▮◑ (≒ 3.7×10³) |
| `[5, 2, 5, 2, 5, 2]` | group | 1984 | `MDCCCCLXXXIIII` | |

## 描画の設定

段ごとの記号 (`glyphs`) は param ではなく描画側の設定に持つ。例: ローマ数字は `glyphs: ["I", "V", "X", "L", "C", "D", "M"]`、正の字の束を ▣ で描くなど。ローマ数字の減算型 (IV, IX) は、分解ではなく描画側の書き換え規則として扱う。

## param (案)

| パラメータ | 必須 | 意味 |
| --- | --- | --- |
| `value` | 必須 | 表す量 |
| `base` | 任意 | 基数。数値か配列 (混合基数)。省略すると単純な一進法 |
| `mode` | 任意 | `group` / `positional` / `log`。省略すると幅から自動選択 |
| `units` | 任意 | 段ごとのラベル (`["h", "m", "s"]` など) |
| `max` | 任意 | 最大数。満たされた記号と空の記号の並びに使う |
| `kind` | 任意 | `count` / `rating` / `ratio` |

## 未確定

- kind を count / rating / ratio のどこまで対象にするか
- ascii / unicode レベルの既定の記号セット
