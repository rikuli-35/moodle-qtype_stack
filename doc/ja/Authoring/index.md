# STACK問題の作成

このセクションでは、STACKの問題作成機能についてのリファレンスを提供する。

## STACK問題の構造

「問題」はシステムの基本的なオブジェクトである。次の表は、問題を構成する各フィールドと、それぞれのドキュメントへのリンクを示している。

| 名前 | 詳細 |
| --- | --- |
| **問題名** (Question name) | 問題の名前 |
| [**問題変数**](Variables.md#Question_variables) (Question variables) | 問題を生成するために使用できる、値がランダムに定まり得る変数 |
| [**問題テキスト**](CASText.md#question_text) (Question text) | 学生が実際に目にする問題文 |
| [**個別のフィードバック**](CASText.md#Specific_feedback) (Specific feedback) | 小テストの設定に応じて、小テスト受験中に利用可能になるフィードバック |
| [**全般的なフィードバック**](CASText.md#General_feedback) (General feedback) | 問題が終了した後にのみ利用可能になるフィードバック |
| [**問題記録**](Question_note.md) (Question note) | ランダム生成された問題の変形を区別するための情報を保存するフィールド（※この値が異なる場合にのみ別問題として扱われる） |
| [**解答欄**](../../en/Authoring/Inputs/index.md) (Inputs) | 学生が実際に操作する要素 |
| [**ポテンシャル・レスポンス・ツリー**](Potential_response_trees.md) (Potential response trees) | 学生の解答の数学的特性を確立し、フィードバックを生成するアルゴリズム |
| [**オプション**](Question_options.md) (Options) | 多くの挙動を変更できる設定項目 |

問題作成ドキュメントでは、以下のトピックも扱っている。

* [解答形式](../../en/Authoring/Inputs/index.md)
* [評価関数](../../en/Authoring/Answer_Tests/index.md)
* [問題ブロック](../../en/Authoring/Question_blocks/index.md)
* [STACKにおけるフィードバックの種類](Feedback.md)に関する情報

### その他

* [多言語問題](Languages.md)の作成
* [よくある質問（FAQ）](Author_FAQ.md)
