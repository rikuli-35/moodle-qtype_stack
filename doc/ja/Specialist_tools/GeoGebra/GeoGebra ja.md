# 初めてのGeoGebra問題の作成

GeoGebraアプレットからの情報はSTACKの解答欄に連携でき、通常の方法でポテンシャル・レスポンス・ツリーによって評価できます。このドキュメントの目的は、初めてGeoGebraを入力として使用する問題の作成する際の支援をすることです。

[GeoGebra](../../Specialist_tools/GeoGebra/index.md)アプレットの使用に関するリファレンスドキュメントは別のページにあります。

## はじめに

このドキュメントは、以下の問題作成クイックスタートガイドのセクションを学習済みであることを前提としています。

* [問題作成クイックスタート1](../../AbInitio/Authoring_quick_start_1.md)：基本的な問題
* [問題作成クイックスタート2](../../AbInitio/Authoring_quick_start_2.md)：問題変数
* [問題作成クイックスタート3](../../AbInitio/Authoring_quick_start_3.md)：フィードバックの改善

__GeoGebraを含むSTACK問題を編集する際は、HTML対応エディタを使用しないことを強く推奨します。__ 代わりに、生のHTMLを直接編集してください。各ユーザーは以下の手順でエディタ設定を変更できます。

    ユーザーダッシュボード > プレファレンス > エディタプレファレンス > テキストエディタ

__ライセンスに関する注意：__ [GeoGebraのライセンス](https://www.geogebra.org/license)は[STACKのライセンス](https://github.com/maths/moodle-qtype_stack/blob/master/COPYING.txt)と一致していない点にご注意ください。STACKのユーザーは、STACK問題内に埋め込まれた素材やメディアのライセンスに関して、完全に自己責任で遵守する必要があります。

## 問題の設計

目標は、以下のような問題を作成することです。

「（GeoGebraアプレット上で）点 \\(A\\) と \\(B\\) を移動して、直線 \\(AB\\) がアプレットに表示されている直線 \\(y=mx+c\\) に垂直になるようにしなさい。」

注意事項：

* \\(m\\) と \\(c\\) は問題変数でSTACKによって定義されます。最終的にはランダムに生成することも可能です。この変数の扱いは、アプレット内の値を「set（設定）」する方法の具体例となっています。
* 直線 \\(AB\\) の傾きを入力として「取得(listen)」する必要があります。これはGeoGebra内の値を「監視(watch)」してSTACKの解答欄に連携させる方法を示しています。
* \\(A\\) と \\(B\\) はGeoGebraアプレット内の点です。システムは学生が点を残した位置を「記憶(remember)」する必要があります。
* この問題では、ポテンシャル・レスポンス・ツリーで変数 \\(m\\) に直線 \\(AB\\) の傾きを掛けて、それが \\(-1\\) であるかを確認します。学生の直線の位置そのものは問いません。

## 1. GeoGebraアプレットの作成

最初のステップは、GeoGebraアプレットを作成してオンラインで公開することです。GeoGebraのURLからmaterial_idを取得し、ワークシートをSTACK問題に連携させます。

1. [https://www.geogebra.org/](https://www.geogebra.org/) にログインします。
2. GeoGebraのウェブサイト上で空のアプレットを作成します。
   * 数値 `m=2` と `c=3` を作成します。実際の問題ではSTACKが学生の問題開始時に値を設定しますが、適切な初期値を設定しておく必要があります。
   * 直線 `l=m*x+c` を作成します。
   * 点AとBを作成し、それらを通る直線を作成します。この直線を `f` と名付けます。
   * 新しい変数 `ans1=Slope(f)` を作成します。この変数 `ans1` の値をSTACKの解答欄が「watch」するように設定します。
   * 傾きや、必要に応じてその他のオブジェクト名をGGBアプレット内で非表示にします。
   * GeoGebraの数式ビューを非表示にし、グラフィックスビューのみを表示した状態にします。
3. GeoGebraファイルを保存して公開します。このとき、URLに含まれるIDが必要となります。例えば、GeoGebraファイルのURLが `https://www.geogebra.org/calculator/anr6ujyf` である場合、IDは末尾部分の `anr6ujyf` です。このIDは `material_id` の値としてSTACKとGeoGebraを連携させるために必要です。

## 2. 素材を含む最小限のSTACK問題の作成

STACK上で新しい問題を作成します。問題変数を設定します。初期段階では問題変数は固定値とします。後でこれらの値をランダムに生成するようにします。

    m:2;
    c:-1;
    ta:-1/m;

問題テキストを設定します。入力前にエディタがHTMLモードに切り替わっていることを確認してください。

    [[geogebra set="c,m" watch="ans1"]]
    params["material_id"]="anr6ujyf";
    [[/geogebra]]
    <p>Move the points \(A\) and \(B\) so that the line \(AB\) is perpendicular the line shown in the applet.</p>
    [[input:ans1]][[validation:ans1]]

これは `[[geogebra]]` 問題ブロックを使用しています。次に、以下の通り問題を完成させてください。

1. 問題テキストのフォーマットが「HTML」形式（「Moodle自動フォーマット」やその他ではなく）であることを確認してください。
2. 解答欄 `ans1` の正解を `-1/m` に設定します。
3. 解答欄 `ans1` の「浮動小数点数の禁止」を `いいえ` に設定します（GeoGebraは浮動小数点数を返すため）。
4. PRTのノード1を `ATNumAbsolute(ans1*m, -1, 0.1)` として評価するように設定します。これは直線の傾きの積が \\(-1\\) の \\(0.1\\) 以内にあるかをチェックします。つまり、ほぼ垂直であるかを判定します。（厳密な代数的同値性 `ATAlgEquiv(ans1*m, -1)` を使用することもできます。）

注意事項

* タグ `[[geogebra set="c,m" watch="ans1"]]` には、GeoGebra内のどの変数を「set（設定）」し、どの変数を「watch（監視）」するかの情報が含まれています。
* 変数名はGeoGebraとSTACKの間で一致していなければならないなど、厳密な命名規則に従う必要があります。詳細は[GeoGebra](../../Specialist_tools/GeoGebra/index.md)リファレンスドキュメントを参照してください。

## 3. STACK問題の予備テスト

この時点で最小限の動作するSTACK問題ができているはずです。問題を保存してプレビューしてください。

1. GeoGebraワークシートが問題に表示され、点 \\(A\\) と \\(B\\) が移動可能であることを確認します。
2. 点を移動すると傾きが解答欄 `ans1` に入力されることを確認します（この時点では解答欄が表示されているはずです）。

## 4. 点 \\(A\\) と \\(B\\) の位置の記憶

問題はアプレット内の値を設定し監視しますが、Moodleのクイズは学生が \\(A\\) と \\(B\\) を残した位置を_記憶(remember)_する必要もあります。これにより、ページの再読み込み時にこれらの位置が保持されます。

これには、ブロックヘッダーに両方の点 \\(A\\) と \\(B\\) を指定した `remember` タグを追加します。

    [[geogebra set="c,m" watch="ans1" remember="A,B"]]
    params["material_id"]="anr6ujyf"; 
    [[/geogebra]] 
    <p>Move the points \(A\) and \(B\) so that the line \(AB\) is perpendicular the line shown in the applet.</p>
    [[input:ans1]][[validation:ans1]]
    [[input:remember]][[validation:remember]]
    

2つの変更点があることに注目してください。(1) geogebraブロックタグに `remember` 引数があること、(2) 問題に新しい解答欄があることです。\\(A\\) と \\(B\\) の座標を実際に保存するには、問題テキストの末尾に新しい解答欄 `[[input:remember]][[validation:remember]]` を追加する必要があります。問題テキストを確認し、フォームを更新して、この新しい解答欄を以下の点に注意して設定します。

1. `remember` 解答欄は文字列型でなければなりません。
2. 「正解」には空文字列 `""` を使用します。
3. （クイズ中に利用可能な場合）教師の最終解答の一部として「remember」の正解を表示したくないため、解答欄のSTACK「その他のオプション」に `hideanswer` を設定して学生から[解答欄を非表示](../../Authoring/Inputs/Input_options.md#extra_option_hideanswer)にします。
4. *remember* 内の値はPRTでは利用できず、STACKのフィードバックで値の計算に使用することはできません。

## 5. 問題の仕上げと調整

問題が動作するようになったら、より良いフィードバックの追加、模範解答の追加、ランダムバージョンの作成などを行えます。例えば、以下のように設定できます。

    m:rand_with_step(2,3,1);
    c:rand_with_step(2,3,1);

さらに、`\[ y={@m*x+c@} \]` のような解答注記を追加します。ランダム化に関するアドバイスは[問題作成クイックスタート4](../../AbInitio/Authoring_quick_start_4.md)を参照してください。

学生に直線の方程式を求めさせる追加の代数入力を設けることも可能です。この時点で、幾何学的な図をより大きな問題の中に組み合わせるための多くのオプションがあります。

テスト完了後は、CSSを使用して解答欄を学生から非表示にしてください。例：`<span hidden="">...</span>`

参考として、完成した問題テキスト全体を以下に示します。

    [[geogebra set="c,m" watch="ans1" remember="A,B"]]
    params["material_id"]="anr6ujyf"; 
    [[/geogebra]] 
    <p>Move the points \(A\) and \(B\) so that the line \(AB\) is perpendicular the line shown in the applet.</p>
    <span hidden="">
    [[input:ans1]][[validation:ans1]]
    [[input:remember]][[validation:remember]]
    </span>
