# tallyexp

[![ooparts](https://raw.githubusercontent.com/elzup/ooparts-spec/main/badge.svg)](https://github.com/elzup/ooparts-spec)

数値を数字ではなく、記号の数や並びで表すための表現規格 (draft)。

```
★★★☆☆      { value: 3, max: 5, kind: "rating" }
正正下      { value: 13, base: 5, mode: "group" }
●○●● ○○●○  { value: 0xB2, base: 16, mode: "positional" }
```

名前はタリー (数え印、一進法) と、regexp に倣った `-exp` (記法の規格) から。

## 表現レベル

param が正本で、ascii / unicode は param を描いた結果として扱う。

| レベル | 中身 | 例 (5 段階評価の 3) |
| --- | --- | --- |
| param | 記号を決めずパラメータだけを持つ。記号は描画側が選ぶ | `{ value: 3, max: 5, kind: "rating" }` |
| ascii | ASCII 文字だけで描く | `***--` |
| unicode | Unicode の記号で描く | `★★★☆☆` |

## 基数ごとの表現

基数ごとの表現 (桁ごと、4bit ごと) はコアに含める。

- **group (束ねる)**: 正の字のように base 個ずつ束ねる
- **positional (位取り)**: 桁の位置で重みが変わる。各桁を記号の数 (そろばん・算木) か、記号のオン / オフ (ビット) で表す
- base 無し: 束ねない単純な一進法 (`|||`)

| base | mode | 値 | ascii | unicode |
| --- | --- | --- | --- | --- |
| 5 | group | 13 | `\|\|\|\|/ \|\|\|\|/ \|\|\|` | 正正下 |
| 10 | positional | 237 | `\|\| \|\|\| \|\|\|\|\|\|\|` | (算木・そろばん風) |
| 16 | positional | 0xB2 | `#.## ..#.` | ●○●● ○○●○ |
| 2 | positional | 5 | `#.#` | ●○● |

## param (案)

| パラメータ | 必須 | 意味 |
| --- | --- | --- |
| `value` | 必須 | 表す量 |
| `base` | 任意 | 基数。省略すると単純な一進法 |
| `mode` | 任意 | `group` か `positional` |
| `max` | 任意 | 最大数。満たされた記号と空の記号の並びに使う |
| `kind` | 任意 | `count` / `rating` / `ratio` |

## 未確定

- group / positional を `base` パラメータで扱う形で確定するか
- kind を count / rating / ratio のどこまで対象にするか
- ascii / unicode レベルの既定の記号セット
