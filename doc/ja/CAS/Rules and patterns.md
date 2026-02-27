# 規則とパターン

Maximaには規則やパターンを定義するシステムがある。例えば、デスクトップ版のMaximaで以下のようにすると

```
matchdeclare([a],true)；
let(sin(a)^2, 1-cos(a)^2)；
letimp(sin(x)^4)；
```

$\mathrm{sin(x)^4 \rightarrow cos(x)^4 - 2cos(x)^2 + 1}$が得られる。

`let`のサポートはv4.8.0（2024年11月）で追加されたが、現在は部分的なサポートしか利用できない。

特に、Maximaの`let`関数は、Maxima-PHP接続ではサポートされていない特別な演算子`->`を使用する。これに対応するためには、`let`コマンドを最後の要素を返すブロックの中に記述する必要がある。

例えば、問題変数に次のように記述すれば動作する（ただし、上記の例では動作しない）。

```
matchdeclare([a],true)；
p1:(let(sin(a)^2, 1-cos(a)^2), letsimp(sin(x)^4))；
```
そして、（問題文などの）castext内で`{@p1@}`とすると、$\mathrm{cos(x)^4 - 2cos(x)^2 + 1}$が得られる。通常、Maximaはこの簡約を行わない。

### 行列の例

`I`を単位行列とする。

```
orderless(I)；
matchdeclare([a],true)；
/* 戻り値(ここでは"true")がPHPにパースされて戻ってくることを確認するためにブロックを使用していることに注意すること。*/
(let(I*a, a),let(I^2, I),true)；
p:letsimp(expand((A+I)^3))；
```
castext内で`{@p@}`とすると、$\mathrm{A^3 + 3\cdot A^2 + 3 \cdot A + I}$が得られる。