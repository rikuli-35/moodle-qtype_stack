# 幾何学関連のMaxima関数

STACKで[Geogebra入力](../Specialist_tools/GeoGebra/index.md)を使用する場合では、教師が数学的性質を判定しやすくするために、Maximaにいくつか幾何学関連の関数を追加している。

これらの関数は `stack/maxima/geometry.mac` で定義されている。

なお、Maxima側で定義されていない関数については、関数名をGeogebraの関数名と一致させる必要があります。


### Length関数

`Length(v)`は、ベクトル（リストで表現される）について、原点からその点までのユークリッド距離を返す。

### Distance関数

`Distance(A, B)` はリストで表現された2点間のユークリッド距離を返す。これは任意の次元で動作する。

### Angle関数

`Angle(A,B,C)`は、3点$A,B,C$が作る角度を返す。戻り値はラジアンである。
角度の範囲は、$^\pi$から$\pi$であることに注意すること（$0$から$2\pi$ではない）。
