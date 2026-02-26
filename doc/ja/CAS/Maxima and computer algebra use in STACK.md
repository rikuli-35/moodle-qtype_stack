# STACKにおけるMaximaとコンピュータ代数の使用

STACKはコンピュータ代数システム(CAS)である [Maxima](../../en/CAS/Maxima_background.md) を使用している。このドキュメントのセクションでは、Maxima固有の関数について扱う。これには、Maximaの標準機能に含まれる関数と、STACKによって定義された関数の両方が含まれる。

## STACKにおけるMaximaの機能

* 式を判定するのに役立つ[述語関数](../../en/CAS/Predicate_functions.md)
* 浮動小数点数や複素数を含む[数値計算](../../en/CAS/Numbers.md)
* Maximaでは[簡約](../../en/CAS/Simplification.md)のオン/オフを切り替えることができる
* [不等式](Inequalities.md)
* [行列とベクトル](../../en/CAS/Matrix.md)
* [統計](../../en/CAS/Statistics.md)
* [ランダムに生成されるオブジェクト](../../en/CAS/Random.md)
* [Maxima plot2d](../../en/CAS/Maxima_plot.md)
* 常に正しいとは限らない規則[Buggy rules](../../en/CAS/Buggy_rules.md)

## オフラインでの利用

オフラインで作業する場合は、デスクトップ用のグラフィカルインターフェースであるWxMaximaをダウンロードして利用することが推奨されている。

* デスクトップ上でコードをテストするための[STACK-Maximaサンドボックス](../../en/CAS/STACK-Maxima_sandbox.md)の構築
