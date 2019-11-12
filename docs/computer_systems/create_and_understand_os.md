# 概要

## 本

- [作って理解するOS x86系コンピュータを動かす理論と実装 | 林 高勲, 川合 秀実](https://amzn.to/36I8Ry7)
  - [サポートページ：作って理解するOS x86系コンピュータを動かす理論と実装：｜技術評論社](https://gihyo.jp/book/2019/978-4-297-10847-2/support)

## かかった時間

- x 時間

## 読む前の状態

- OS についての本は何も読んでいなく、1 冊目として手を動かしながら概要を抑えるために読み始めた
- CPU やコンパイラについては、基本は理解している状態
  - [コンピュータシステムの理論と実装](./nand2tetris.md)
  - [CPUの創りかた](./how_to_create_cpu.md)
  - [プロセッサを支える技術](./technologies_for_processors.md)
  - [コンパイラの構成と最適化](./costruction_and_optimization_for_compiler.md)

## 読む前後の変化

- xxx

## コード

- [y-meguro/testOS](https://github.com/y-meguro/testOS)

# 読書メモ

## イントロダクション

- アドレスとポインタ
  - C 言語では、型情報を含んだアドレスのことをポインタと呼び、単なるアドレスとは区別している
    - アドレス: メモリの位置
    - ポインタ: メモリの位置と型情報
  - 間接演算子（*）はポインタが示す変数を表す
  - アドレス演算子（&）は変数のポインタを取得する

## 1章: コンピュータの基礎を理解する

- デューティ比: 1 つの周期内で電圧が高い割合
- イネーブル信号
  - コンピュータの内部には、特定の役割を持った回路がいくつも存在している
  - これらの回路は結果の出力先が同じなので、データを出力する信号線を共有している
  - このため、必要となる回路の出力だけを正しいタイミングで取り出さなくてはならない
  - これを実現するために、多くの回路には、機能や出力信号をアクティブにするためのイネーブル信号が用意されている
  - イネーブル信号は回路からの出力を許可するための信号
- 結線により行うプログラミング方式をワイヤードプログラミング方式という。物理的な変更を必要としない、書き換え可能なメモリにプログラムを記録する方式をストアドプログラミング方式という
- タスク: OS が管理する一連の処理
  - 一般的にジョブは連続処理、タスクは並列処理が行われる時に使用される概念
- タスク切り換え
  - 複数音タスクを切り替えるのは OS の役割
  - タスクが使用する領域は、CPU 内のレジスタとメモリ上のデータ領域に大別される
  - メモリ上のデータ領域は OS がタスクごとに提供するので、他のタスクとの競合を考慮する必要はない。しかし、CPU 内のレジスタはすべてのタスクで共有されるので、タスクごとに個別に管理する必要がある
    - この時、タスクが使用するすべてのレジスタのことをコンテキストという

## 2章: ソフトウェアの基礎

- チューリングマシン
  - アラン・チューリングによって発表された概念的な計算装置のこと
  - チューリングマシンはあらかじめ定義された状態のみを遷移するステートマシンで、その状態は現在の状態と入力された値によってのみ変化する
- OS の主な役割
  - リソース管理
    - OS は接続されたすべてのデバイスをリソースとして管理する。タスクは、OS が管理しないリソースを利用することはできない
    - OS が管理するリソースには、各プロセスによって使用される仮想的なリソースも含まれる。代表的なものが CPU 時間
  - プロセス管理
    - OS が管理するプログラムの実行単位をプロセスという
    - OS はプロセスを管理し、適切な権限を持つプロセスだけが制限されたリソースにアクセスできるようにする
  - インタフェースの管理
    - ソフトウェア / ハードウェアインタフェースは、プロセスが利用可能なリソースにアクセスするための共通インタフェースとなる
    - OS はこれらを管理することで、プロセスに対して、共通のインタフェースを提供する
- プロセスとは何か
  - インスタンス
    - メモリ上に展開された「プログラム」はプログラムの実行状態、またはインスタンスと呼ばれる
    - インスタンスはコード（プログラム）/ データ（実行状態）/ コンテキスト（レジスタ値）を含む
  - プロセスの役割
    - インスタンスごとの状態を管理するのがプロセスの役割
    - プロセスはインスタンスに加え、OS 管理情報（権限）を含む
  - プロセスの状態遷移
    - プロセスはそれぞれ、生成 / 待機 / 実行 / ブロック / 終了のうち、1 つの状態を取る
  - プロセスの実行
    - OS はメモリ上に展開された待機状態のプロセスの中から、実行状態へと移行させるプロセスを 1 つだけ選び出す。どのプロセスを選択するかは、OS の性能や使いやすさに直結する作業で、スケジューリングと呼ばれる
    - 実行状態に移行したプロセスは、OS から割り当てられた時間しか実行状態でいることができない。この時間のことをクォンタムという
    - ほとんどの OS は、クォンタムを使い切ったプロセスを定期的に切り替える方式を採用しているが、このような方式はプリエンプティブ（Preemptive: 先取権のある）なマルチタスクと呼ばれる
    - これに対して、それぞれのプロセスが自発的に他のプロセスに実行権を譲る方式は、ノンプリエンプティブまたは協調型マルチタスクと呼ばれる
- カーネルとは何か
  - OS の中心的な機能を持ったプログラムまたはその集まりのことをカーネルという
  - CPU は特権モードと非特権モードの 2 つの動作モードを持つ
    - 特権モードでは、すべての CPU 命令を使用できる
    - 非特権モードでは、一部の命令を使用できない
  - OS はリソースに対するアクセス方法として、システムコールと呼ばれるインタフェースを用意している
    - タスクは、OS により動作が厳密に定義されたシステムコールを介してのみ、リソースへのアクセスが許可される
  - カーネルの種別
    - カーネル自体を小さく保つ設計をマイクロカーネルと呼ぶ
    - マイクロカーネルに対して、すべての機能を OS 本体に含む設計をモノリシックカーネルと呼ぶ
- 同期処理の必要性とその実現方法
  - 以下のような実現方法がある
    - ソフトウェアで実現する: デッカーのアルゴリズム
    - ハードウェアで実現する: テストアンドセット
    - ハードウェア + OS で実現する: セマフォ
  - ビジーウェイト: 変数の値が変化することを検査するだけのために CPU 時間を使っている期間
  - デッカーのアルゴリズム
    - 交互実行を必要としない同期処理ができる。共有メモリによる通信のみで、2 つのプロセスが 1 つのリソースを競合することなく共有できる
    - 欠点
      - プロセスが多くなった場合、それぞれのプロセスが互いに他のプロセスを認識する必要があり、プロセスの数だけグローバル変数が必要
      - 各プロセスが次に動作するプロセスを指定する必要もある
      - それぞれのプロセスが他のプロセスの変数を参照することになるので、プロセスの独立性も下がる
      - 静的な変数を参照するので、動的に生成または終了したプロセスを考慮していない
  - テストアンドセット
    - あるメモリ位置へアトミックに書き込みを行うコンピュータの命令
    - 共有変数の「値の確認と更新」を 1 つの命令で行う（CPU がメモリアクセス時にロック信号を出力する）
      - 分割されない一連の処理をアトミック処理という
    - ただし、これでもビジーウェイトの問題は残ったまま
      - ビジーウェイト中のプロセスは、実行状態に移行したとしてもリソースの解放を確認するだけなので、コンテキストの切り替えさえも行いたくない
  - セマフォ
    - P 命令と V 命令によってのみ値が変更される整数型変数 S を用いた同期処理
    - グローバル変数 S に初期値として設定された値は、クリティカルセクションを同時に実行できるプロセス数となる
    - リソースの解放を待つだけのプロセスをブロック状態に遷移させるので、ビジーウェイトを取り除くことができる
- デッドロックの発生要因とその回避方法
  - あるプロセスが解放される見込みのないリソースを待ち続けている状態をデッドロックという
  - デッドロックが発生する条件は以下の 4 つと言われる。これらの条件がすべて揃うとデッドロックが発生する可能性があり、条件が 1 つでも欠ければデッドロックを回避できる
    - 排他制御状態
      - リソースの取得に対して、排他的なアクセス制御が行われている
    - 保有待機状態
      - リソースを保有しているプロセスが新しいリソースを要求できる
    - 優先取得権のない状態
      - プロセスが保有しているリソースを強制的に取り上げることができない
    - 循環待機状態
      - 複数のプロセスが、異なる順序での複数のリソースを要求できる
- スケジューリング
  - OS はプロセスが使用する CPU の使用効率を高めながらも適切な応答速度を維持し、より多くのプロセスを並行して実行することが求められる
  - これらを考慮し、次に実行すべきプロセスを決定することをスケジューリングという
  - ターンアラウンド: プロセスの実行が要求されてから、終了するまでの時間
  - リアルタイム: 決められた許容範囲内で処理が終了すること

## 3章: メモリ管理のしくみ

- ROM
  - ROM は特殊な処理で書き込みも可能。ユーザーがプログラムを書き込むことができる ROM のことを PROM（Programmable ROM）という
  - 消去可能なもの
    - EPROM（Erasable PROM）
      - 消去可能な ROM
      - チップの表面に紫外線を十分に当てると、データの消去が行える
    - EEPROM（Electrically Erasable PROM）
      - 電気的にデータを消去可能
    - Flash ROM
      - 基板上で使用する電圧を使い、データの消去および書き込みが可能（通常の PROM は普段の動作時に使用される以上の高い電圧を必要としていた）
  - 消去不可能なもの
    - OTPROM（One Time PROM）
      - ユーザーが一度だけ書き込みができる PROM
    - Mask ROM
      - 工場の製造段階で一度だけ書き込みができる PROM
- RAM
  - データを保持する方法により DRAM（Dynamic RAM）と SRAM（Static RAM）に分類される
  - DRAM はコンデンサを使ってデータを保持するので、リフレッシュを行う必要がある
  - SRAM はコンデンサではなく、複数の回路を使ってデータを保持するもの。リフレッシュが不要となる反面、回路が複雑になるので、アクセス速度や容量では DRAM より劣ることになる。同量の DRAM よりも高価になる傾向がある
- セクション
  - プログラムはその内容により、セクションという単位でまとめられている
  - 大きく分けて、プログラム / 定数 / 初期化 / 未初期化の 4 つのセクションに分類される
- メモリを効率的に利用する手法
  - オーバーレイ
    - メモリに入りきらないプログラムコードを必要なときにだけロードして使用する方法
    - OS にはプログラムの先頭部分だけをロードしてもらい、残りの部分はプロセス自身でメモリにロードする
  - バンク切り替え
    - CPU のアドレス信号を増やすことなく、アクセス可能なメモリ容量を拡張する手法
    - アドレス信号に変わり、多くの場合、ポート出力信号が利用される
    - 切り替えが行われるメモリ領域のことをバンク領域と呼び、ポート出力の値により、異なる実メモリが割り当てられる
  - プロセスのロード
    - 複数のプロセスをメモリに展開する場合、重複しない領域を割り当てるが、効率的にメモリを利用できるように配置する必要がある
    - メモリサイズによるロード
      - メモリ全体をいくつかの領域に分割し、その 1 つ 1 つにプロセスを展開する方法
    - プロセスサイズによるロード
      - プロセスが要求するメモリ領域は可変であるため、空いているメモリ領域に隙間なくプロセスを割り当てていけば未使用領域が生成されず、メモリを効率的に使用できる
      - しかしこれは最初だけで、プロセスの起動と終了が繰り返し行われる過程で十分なメモリ領域があるにも関わらず、メモリ領域が連続していないためプロセスを起動できない状態が発生する
    - プロセスの入れ替え
      - スワップアウト: メモリ上のプロセス全体を外部記憶装置に移動すること
      - スワップイン: 外部記憶装置にあるプロセスイメージをメモリ上に移動すること
      - スワップアウトで作成される、外部記憶装置上のファイルはスワップファイルと呼ばれる
- 論理アドレスと物理アドレス
  - すべてのプロセスは異なるメモリ領域に展開されるので、それぞれ異なるデータ領域にアクセスする必要がある
  - これは、プログラムごとの相対的なアドレス指定にプロセスごとのベースアドレスを追加することで対応する
  - OS とプロセスの双方でメモリのアクセス位置を調整する方法
    - OS はプロセスごとのメモリ領域を管理し、プロセスは OS から割り当てられた領域だけにアクセス範囲を限定する
  - プロセスがアクセスするメモリ領域が重ならないように、OS がアドレスを変換する方法
    - OS はプロセスがアクセスしようとしたアドレス（論理アドレス）と、OS により変換されたアドレス（物理アドレス）の 2 種類のアドレスを管理する
    - すべてのアドレス変換をソフトウェアである OS が行うのはとても負荷がかかるので、現実的ではない
- MMU（Memory Management Unit）
  - アドレス変換を含め、メモリ管理に関する制御を専門に行うハードウェアは MMU と呼ばれる
  - MMU がアドレス変換を行う範囲のことをページという。また、アドレス変換がページ単位で行われることから、ページ変換とも呼ばれる
- ページテーブル
  - ページ変換は MMU が行うが、正しく変換されるように設定するのは OS の仕事
  - OS はプロセスごとにアドレス変換を行うためのページテーブルを作成して管理する
    - ページテーブルには、ページごとの変換情報などが格納される
  - OS は MMU のページ変換機能により、プロセスごとに異なるアドレス空間を提供できる
    - 重要なポイントは 2 つ
      - すべての論理アドレスにページ変換を適用できる
      - プロセスごとにページ変換テーブルを用意できる
- 仮想メモリ
  - 外部記憶装置をページイメージの一時的な保存領域として使用するメモリ管理技法のこと
  - メモリに空き領域がない状態で、ページフォルトが発生すると、OS はメモリ上に空き領域を確保しようとする。使用中のページから使用頻度の低いいくつかのページを選択し、外部記憶装置に保存する。これをページアウトという
  - ページアウトと反対に、外部記憶装置からページデータをメモリにコピーすることをページインと呼ぶ
- セグメンテーション
  - 1 つのプロセスが複数のアドレス空間を利用できる機能。このときにプロセスが利用する論理的なアドレス空間をセグメントという

## 4章: ファイルシステムのしくみ

- ファイルシステム
  - OS が適切なメモリ管理を行ってくれるので、プロセスは与えられたメモリ領域を自由に使うことができる。しかし、これはプロセスが起動している間だけに限られる
  - 長期にわたってデータを蓄積していくプログラムやメモリに収まらないほど大量のデータを扱うプロセスには、永続的にデータを保存する方法が必要になる
  - 一般的な OS では、プロセスが終了した後でもデータを保存しておく方法が提供されており、保存されたデータはファイルと呼ばれる。ファイルを使ってデータを管理するプログラムの集まりをファイルシステムという
- ファイル情報
  - ファイルシステムは 1 つのファイルに対して 2 種類の情報を管理する
    - 保存すべきデータそのもの
    - ファイルの属性
      - ファイルサイズやデータ更新日時など
  - データへのアクセス方法は 2 つあり、ファイルシステムではシーケンシャルアクセスを提供している
    - シーケンシャルアクセス
      - データの先頭から順次アクセスを行わなければ、任意のデータにたどり着くことができないアクセス方法
    - ランダムアクセス
      - アドレスを特定してデータへアクセスする方法
      - メモリなどが利用
  - プロセスはファイルシステムが管理する情報に直接アクセスすることはできず、システムコールを利用する必要がある
    - この時、プロセスがファイルを特定するために必要となる情報がファイル記述子
- ファイル操作
  - 基本的なシステムコール
    - CREATE / DELETE
      - 作成と削除
    - OPEN / CLOSE
      - オープンとクローズ
    - READ / WRITE
      - 読み込みと書き込み
    - SEEK
      - ファイルデータのアクセス位置の変更
    - GET / SET ATTRIBUTES
      - 属性の取得と設定
    - RENAME
      - ファイル名の変更
- 通常ファイルと特殊ファイル
  - ファイルシステムには、データを保存する機能以外に、ファイルと入出力デバイスを関連付けることでデバイスごとに異なるアクセス方法の差異を吸収する役割もある
    - このような目的で作成されたファイルを「特殊ファイル」、データの保存を目的としたファイルを「通常ファイル」という
    - 特殊ファイルの場合、デバイスへのアクセスは関連付けられたファイルへのアクセスで行える
- ファイルシステムの階層構造
  - ファイルシステムは複数の物理的なデバイスを管理するために、機能ごとにわかれたいくつかの階層で構成される
  - 最上位層では、共通のファイル操作法を提供する
  - 最下位層では、各記憶装置専用のデバイスドライバが役割を担う
- ファイルシステムでは、ファイル名によって記録データを識別する
- ファイルシステムでは、複数のファイルを論理的なグループ（ディレクトリ）に分けて管理できる
- ブロック単位でのデータ管理方法
  - 連結リストを使用する方法とインデックスを使用する方法にわけられる

## 5章: 入出力のしくみ

- 入出力ソフトウェアの階層構造
  - 上位層から下位層へ、以下のようになる
    - プロセス
      - 入出力要求を出す
    - デバイス非依存ソフトウェア
      - 入出力要求を管理する
    - デバイスドライバ
      - 上位層からの入出力要求をハードウェアの命令に置き換えたり、ハードウェア固有の設定や管理などを行う
    - 割り込みハンドラ
      - 入出力機器からの要求があった時にどのような処理を行うかを示す
    - ハードウェア
      - 入出力処理の実行
- 入出力ハードウェア
  - 入出力デバイスには、コンピュータに接続するための物理的な接続部分が必要
  - またデータをやり取りするためには、物理的な接続方法が一致しているだけでなく、同時に電気的な仕様も一致している必要がある
    - このような接続に関する仕様はハードウェアインタフェースと呼ばれる

## 6章: コンピュータの基本構成

- コンピュータの ROM には、外部記憶装置からメモリに起動プログラムをロードする機能が備わっている
  - この時に利用されるのが BIOS（Basic Input/Output System）と呼ばれる基本入出力ソフトウェア群
- 電源投入時に行われる処理
  - 電源投入後、最初に起動するプログラムは BIOS。BIOS は POST（Power On Self Test）と呼ばれる最低限のハードウェアチェックを行う
  - 周辺機器のエラーが検出されなければ、起動装置として指定された外部記憶装置の先頭から 512 バイト分を読み取り、ロードする。これがユーザーが作成可能な最初の起動プログラムで、ブートコードと呼ばれる
- 割り込みコントローラの役割
  - 割り込みとは、プログラムの実行アドレスを特定の値に書き換えること
  - 割り込みコントローラは複数の割り込み信号の中から、最も優先度の高い割り込みを CPU に通知し、低優先度の割り込みは高優先度の割り込みが終了するまで保留するなどの制御を行う

## 7章: CPU の基本機能

- 8086 のレジスタ
  - 8086 は 16 ビット CPU で 80386 の前に出されている機種
  - 汎用レジスタ
    - 一般的な算術命令 / カウンタ / ビット演算 / データ転送などで使用可能なレジスタ群
  - フラグレジスタ
    - CPU の内部状態を表す 16 ビット幅のレジスタ
    - 制御フラグは CPU の動作に影響を及ぼすもので、フラグの値が異なれば、同じ CPU 命令でも異なる動作を行う
    - 状態フラグには、桁上りやパリティなどの CPU 命令を実行した結果による付加的な情報が反映される
      - パリティ: ある数字の並び（大体 2 進数）の合計が偶数・奇数かどうかを比較する事により、通信の誤りを検出する技術
  - セグメントレジスタ
    - セグメントとは、分割されたメモリの一部分をさすもので、8086 で導入された概念
    - セグメントの開始位置を指定する専用のレジスタとして、セグメントレジスタが用意されている
    - セグメントレジスタとポインタレジスタをあわせて、すべてのアドレス空間にアクセスすることが可能になる
  - ポインタレジスタ
    - CPU がアドレスを指定する時に使用できるレジスタ
- 80386 のレジスタ
  - 80386 は 8086 の後継機種で一部の 16 ビットレジスタが 32 ビットに拡張されている
  - 80386 に内蔵されているレジスタは 8086 で使用可能なレジスタが全て含まれている
  - セグメントレジスタ
    - ポインタレジスタが 32 ビットに拡張されたので、単一のレジスタですべてのアドレス空間にアクセスすることが可能になった。しかし、セグメントレジスタは 16 ビットのまま存在し続けている
    - 80386 では、保護されたセグメント空間をセグメントと呼ぶようになった
  - アドレッシング
    - 80386 ではポインタレジスタの概念がなくなり、8 本の汎用レジスタすべてをアドレッシングで使用できる
  - 制御レジスタ
    - 4 本の制御レジスタが追加された
    - 管理する情報
      - メモリの保護とコプロセッサ制御に関するフラグ
        - コプロセッサ: CPU の補助を行う目的で作られた処理装置
      - ページフォルト例外が発生した時のアドレス、ページ変換で使用されるページテーブルのアドレス
  - メモリ管理レジスタ
    - メモリ管理情報を設定する複数のレジスタ群

## 8章: CPU 命令の使い方

- アセンブラにはいくつかの種類が存在するが、ここでは [NASM](https://www.nasm.us/) と呼ばれるアセンブラを使用する
- IP（インストラクションポインタ）
  - 次に実行する機械語命令が格納されているアドレスを保持している
- セグメントオーバーライド
  - x86 系 CPU のメモリアクセスでは、必ずセグメントレジスタが参照される
  - デフォルトで参照されるセグメントは変更することが可能。これをセグメントオーバーライドという
- CPU 命令とその使用例
  - 転送と変換命令
    - MOVZX 命令: 拡大された上位ビットに 0 を設定する。ゼロ拡張
    - MOVSX 命令: 拡大された上位ビットに、第 2 オペランドの最上位ビットを設定する。符号拡張
  - 変換命令
    - CBW 命令: AL レジスタの値を符号付き整数として AX レジスタに設定する
    - CWD 命令: AX レジスタの値を符号付き整数として DX と AX レジスタで構成される 32 ビット符号付き整数に変換する
    - CQD 命令: EAX レジスタの値を符号付き整数として EDX と EAX レジスタで構成される 64 ビット符号付き整数に変換する
    - これらの命令はすべて変換前の最上位ビットをコピーする、符号拡張が行われる
  - 加算命令 / 減算命令
    - ADC 命令は第 2 オペランドとキャリーフラグを第 1 オペランドに加算する
    - SBB 命令は第 1 オペランドから第 2 オペランドを減算する
  - 乗算命令 / 除算命令
    - MUL 命令は符号なし乗算を、IMUL 命令は符号付き乗算を行う
    - DIV 命令は符号なし除算を、IDIV 命令は符号付き除算を行う
  - ビット操作命令
    - NOT 命令は 1 の補数を設定する（ビット反転）
    - NEG 命令は 2 の補数を設定する（負の数にする）
  - シフト命令
    - SHL（SHift logical Left）/ SHR（SHift logical Right）命令は論理シフトを行う
    - SAL（Shift Arithmetic Left）/ SAR（Shift Arithmetic Right）命令は算術シフトと呼ばれ、最上位ビットを符号として扱う
    - ROL（ROtate Left）/ ROR（ROtate Right）命令は、シフトにより押し出された最上位ビットまたは最下位ビットを空きビットにコピーすることでビットを循環させる
    - RCL（Rotate thru Carry Left）/ RCR（Rotate thru Carry Right）命令は、キャリーフラグを含めてビットを循環させる
  - ビットテスト命令
    - BT 命令: 第 1 オペランド中の 1 ビットを CF にコピーする。コピーするビットの位置は第 2 オペランドで指定する
    - BTS（Bit Test and Set）命令: BT 命令の実行後にビットの値をセットする
    - BTR（Bit Test and Reset）命令: ビットのクリア
    - BTC（Bit Test and Complement）命令: ビットの反転
    - TEST 命令: 第 1 オペランドと第 2 オペランドの AND 演算を行い、その演算結果をフラグに反映させるが、演算結果は破棄する
  - 条件転送命令（SETcc）
    - 指定された条件によって 1 バイトのオペランドを 0 または 1 に設定する、条件転送命令の総称
  - フラグ操作命令
    - CLD（CLear Direction flag）命令: DF（Direction Flag）を 0 に設定する
    - STD（SeT Direction flag）命令: DF（Direction Flag）を 1 に設定する
    - CLI（CLear Interrupt flag）命令: IF（Interrupt enable Flag）を 0 に設定する
    - STI（SeT Interrupt flag）命令: IF（Interrupt enable Flag）を 1 に設定する
    - CLC（CLear Carry flag）命令: CF（Carry Flag）を 0 に設定する
    - STC（SeT Carry flag）命令: CF（Carry Flag）を 1 に設定する
  - ストリング命令
    - MOVS 命令: 転送元から転送先にデータをコピーする
    - CMPS 命令: 転送元と転送先を比較し、フラグを設定する
    - SCAS 命令: AL または AX レジスタと転送先を比較し、フラグを設定する
    - LODS 命令: AL または AX レジスタにデータをコピーする
    - STOS 命令: AL または AX レジスタの値を転送先にコピーする
  - リピートプレフィックス
    - REP 命令: CX レジスタの値を 1 減算した後、終了条件が成立するまで、オペコードに指定されたストリング命令を繰り返す
  - スタック操作命令
    - PUSH 命令は SP の値を減算した後、オペランドで指定された値を SP で指定されたアドレスに保存する
    - POP 命令は SP で指定されたアドレスの値をオペランドに復帰し、SP の値を加算する
  - 関数呼び出し / 復帰命令
    - RET 命令: スタックに積み込まれた値を IP レジスタに復帰する
  - ソフトウェア割り込み / 復帰命令
    - INT 命令: 事前に設定された割り込みベクタテーブルの中から、オペランドで指定されたベクタ番号のセグメントとオフセットを CS（Code Segment）と IP レジスタに設定する
  - LOCK プレフィックス
    - 次のメモリアクセス命令実行時に CPU の LOCK 信号を有効にする
- リアルモードとプロテクトモードの違い
  - 8086 と 80386 の大きな違いは、メモリ空間保護（プロテクト）機能の有無にある
  - 80386 ではメモリ空間の保護機能が無効な状態をリアルモード、有効な状態をプロテクトモードという
  - 2 つの保護機能
    - 80386 では 2 つの機能を使ってメモリ空間の保護を実現している
      - 特権レベル
        - 特権レベルはセグメントごとに割り当てられるが、プログラム側でも特権レベルを保持する必要がある
        - 80386 では、メモリ空間へのアクセスを要求するプログラムは特権レベルが検査されるので、低い特権レベルのプログラムが高い特権レベルを必要とするメモリ空間にアクセスすることはできない
      - アクセス制限
        - 80386 では、メモリの範囲やアクセス方法などによる制限を行うことも可能
        - アクセス制限は、特権レベルに関係なく、すべてのプログラムが対象となる
  - セグメント記述子
    - セグメントに関する情報の集まり
      - ベースアドレス / リミット / 属性（特権、アクセス制限）
    - セグメント記述子では、メモリ空間のプロテクトと同時にアドレス変換も行う
  - 割り込み
    - ゲート記述子
      - セグメント内のオフセットが設定してある。セグメント記述子とセットで使用される
      - 割り込みゲート記述子
        - ハードウェア割り込みで使用される
      - トラップゲート記述子
        - ソフトウェア割り込みで使用される
      - コールゲート記述子
        - トラップゲート以外の方法で、高い特権レベルのプログラムを実行する方法を提供する
  - タスクスイッチ
    - 80386 では、タスクのコンテキスト情報を TSS（タスクステートセグメント）と呼ばれるセグメント（メモリ空間）に保存する
    - タスクゲート記述子
      - タスクの切替時に参照される記述子
  - ページング
    - CPU のアドレッシングで生成された論理アドレスを物理アドレスに変換する機能

## 9章: アセンブラ（NASM）の使い方

- アセンブルの手順
  - NASM を含めた一般的なアセンブラには、ソースファイルがどのように機械語に変換されたかをテキストファイルに出力するオプションが用意されている。このときに生成されるファイルはリスティングファイルと呼ばれ、NASM の場合、「-l」オプションを指定すると生成できる
- 疑似命令とその使用例
  - アセンブラが解釈する命令には、CPU に対する CPU 命令とアセンブラに対する疑似命令が存在する。ここではいくつかの疑似命令を紹介する
  - データ定義
    - 要素サイズごとに 4 つある
    - DB（Define Byte、バイト数 1）/ DW（Define Word、バイト数 2）/ DD（Define Double word、バイト数 4）/ DQ（Define Quad word、バイト数 8）
  - データ境界（ALIGN）
    - データ境界を調整する命令
  - 定数定義（EQU）
    - 定数を文字列で置き換える
  - 繰り返し（TIMES）
    - 指定された回数分、後続の命令を繰り返す
  - ロードアドレス指定（ORG）
    - アセンブラは、何も指示がなければアドレスの 0 番地からプログラムが開始されるものとして機械語を生成する
    - ブートプログラムは BIOS によってアドレス 0x7C00 番地にロードされるので、それをアセンブラに知らせるのがロードアドレス指定
  - セクション（SECTION）
    - セクションを定義し、指定したセクションに出力を配置する
- プリプロセッサとその使用例
  - プリプロセッサとは、実際のアセンブル作業を開始する前に行われる前処理のこと
  - 置き換え（％define）
    - プリプロセッサのなかでも、文字の置き換えは一般的に行われる作業で、マクロと呼ばれることもある
  - 繰り返し（％rep）
    - 繰り返しは「％rep <回数>」と「％endrep」に挟まれた行を指定回数分繰り返す
  - プリプロセッサ変数（％assign）
    - NASM では、プリプロセッサ処理中にのみ有効な変数を定義して、値を設定および参照できる
  - 条件付きアセンブル（％if）
    - 条件付きアセンブルとは、条件が成立したときにだけアセンブルを行うもの
  - 繰り返し終了（％exitrep）
    - 繰り返し処理は、％exitrep ディレクティブを使用して、途中で終了させることができる
  - 複数行マクロ（％macro）
    - 複数行に渡ってマクロを定義する時に使う
  - 他にもマクロ内ローカルラベル（％％）/ 可変引数 / 引数の回転（％rotate）/ リスト出力の抑止（.nolist）/ ファイルの取り込み（％include ディレクティブ）/ 構造体（struc）を使用できる

## 10章: 周辺機器の制御方法

- メモリマップ
  - メモリマップはハードウェア設計時に決められるので、ソフトウェアでは与えられたメモリマップを使用して、適切なハードウェアの管理を行う
- I/O マップ
  - 周辺機器のポートアドレスに関する情報をまとめたもの
- ビデオ出力
  - VGA（Video Graphics Array）を制御する方法
- キーボードコントローラの機能と使い方
- 割り込みコントローラの機能と使い方
- RTC（Real Time Clock）の機能と使い方
- タイマーの機能と使い方
- 数値演算コプロセッサの機能と使い方
  - x86 系 CPU に接続されている数値演算コプロセッサは、内部で扱うデータが浮動小数点形式であることから、FPU（Floating Point Unit: 浮動小数点演算装置）と呼ばれる
  - FPU 命令のニーモニックには簡単なルールがある
    - すべての FPU 命令は「F」から始まり、整数型データを扱う命令の場合には「FI」から始まる。
    - レジスタのポップをともなう FPU 命令は末尾に「P」がつく
    - 被除数と除数を読み替えて作業を軽減する命令は末尾に「R」がつく
- 大容量記憶装置
  - 異なるファイルシステムを保存することができるように、内部をいくつかに分けて使用できる。このときに分割される 1 つの領域をパーティションという
  - FAT ファイルシステム（File Allocation Table）
    - 大容量記憶装置が提供する、巨大なメモリ領域の管理はファイルシステムが行う
    - ファイルシステムが行う作業には、内部的なデータの管理やファイルアクセスのためのインタフェースを提供することも含まれる。ここでは、マイクロソフト社が開発したファイルシステムである FAT ファイルシステムがファイルを管理する時のフォーマットを確認する

## 11章: BIOS の役割

- BIOS
  - BIOS は関数の集まり。一般的な関数との違いは、ハードウェアの制御が可能であることと、ソフトウェア割り込みで実装されていること
  - BIOS はすべての PC で基本的なハードウェア制御が可能であることから、サービスプログラムとも呼ばれ、BIOS が提供する機能のことをサービスという
  - BIOS 機能を利用することを一般的な関数呼び出しとは区別して、BIOS コールという
  - 関数名ではなく、割り込み名で区別される
- ビデオサービスとその使用例
  - ビデオーモードの設定
  - テレタイプ式 1 文字出力
    - リアルモード時のテキスト出力で使用
  - フォントアドレスの取得
    - プロテクトモードに移行した後は、文字の描画も自力で行わなければならない。フォントデータを自作することもできるが、BIOS が使用しているデータを流用することも可能。この BIOS コールでは、BIOSが使用するフォントアドレスを取得できる
- ディスクサービスとその使用例
  - セクタ読み出し
    - この BIOS コールは、指定したセクタ位置から複数の連続したセクタを読み出すことが可能
  - ドライブパラメータの取得
    - セクタ読み出し時に指定するパラメータは、ハードウェアに強く依存した指定方法。そのため、アクセス対象となるドライブの構造により、セクタの指定方法が変わる
    - この BIOS コールでは、デバイスごとに異なるドライブパラメータを取得できる
- システムサービスとその使用例
  - A20 制御
    - BIOS が A20 Gate 信号の制御をサポートしているか問い合わせる
    - もしサポートしている場合は、A20 Gate 信号の設定が可能
  - システムメモリマップ
    - 0x0010_0000 番地より上位にある、1 つのメモリ領域に関する情報を取得できる
- キーボードサービスとその使用例
  - キーコードの取得
    - この BIOS コールは、キーが押されるまで制御を戻さない。処理が正常に終了すると、キーボードに固有のスキャンコードとアスキーコードが AX レジスタに設定される
- その他のサービス
  - 再起動
    - システムの起動処理を実行する
- ACPI（Advanced Configuration and Power Interface）による電源管理と制御例
  - ソフトウェアで電源を管理する仕様の 1 つが ACPI
  - ACPI では電源の状態を S0 から S5 までの 6 段階で定義している。これをシステム状態という

## 12章: 開発環境を構築する

- アセンブラの使用環境を整える
  - [NASM](https://www.nasm.us/) の 2.14.02 を使用する
- 挙動確認は [QEMU](https://www.qemu.org/) または [Bochs](http://bochs.sourceforge.net/) を利用する

## 13章: アセンブラによる制御構文と関数の記述例

- メモリのコピー関数（memcpy）を作成する
  - void memcopy(dst, src, size)
- メモリの比較関数を作成する
  - memcmp(src0, src1, size)
  - 比較結果を AX レジスタに設定する

## 14章: リアルモードでの基本動作を実装する

- 「何もしない」ブートプログラムを作成する
- BIOS パラメータブロックの領域を確保する
- ブートプログラム内にデータを保存する
- 文字を表示する
- 文字列を表示する
  - すでに文字列の表示関数を作成しているので、数値を文字列に変換する関数 itoa を作成する
- 数値を表示する
- コンピュータを再起動する
- セクタを読み出す
- セクタ読み出し関数を作成する
- ドライブパラメータを取得する
- BIOS のフォントデータを取得する
- メモリの実装状況を確認する
- KBC（キーボードコントローラ）を制御する
- A20 ゲートを有効化する
- キーボード LED を制御する
- カーネルをロードする
- ビデオモードを変更する

## 15章: プロテクトモードへの移行を実現する

- リアルモードとプロテクトモードとの大きな違いは、メモリ空間が保護されているか否か
  - プロテクトモードで動作するすべてのタスクが、許可されたメモリ空間にしかアクセスできない。OS やカーネルでさえ、例外ではない
  - プロテクトモードに移行する前に、カーネルがアクセス可能なメモリ空間を事前に設定していなければならない
- メモリ空間の保護は 2 つのアプローチで行われる
  - アクセスタイプによる保護
    - コード用メモリ領域への書き込みや、データ領域でのプログラムの実行を禁止することが可能
  - 特権レベルによる保護
    - 低い特権レベルに設定されたアプリケーションが、高い特権レベルに設定されたメモリ空間への不用意なアクセスを禁止することが可能
- セグメントディスクリプタを作成する
- 割り込みディスクリプタテーブルを作成する
- プロテクトモードへ移行する
  - プロテクトモードへの移行は、CR0 レジスタにある PE ビットに 1 を設定するだけ
- カーネルを起動する

# 作成メモ

## 12章: 開発環境を構築する

- [Bochs](http://bochs.sourceforge.net/) を利用することにした
  - バージョンは 2.6.9
  - `brew install bochs`
  - Windows ではなく、本に書かれているようなメニュー画面がないので、config file を設定する必要がある
    - 参考: [The configuration file bochsrc](http://bochs.sourceforge.net/doc/docbook/user/bochsrc.html)
  - env 配下に .bochsrc ファイルを作成。12 章で設定されていそうな内容を記載した

```
ata0-master: type=disk, path=boot.img, mode=flat, cylinders=20, heads=2, spt=16, translation=auto
boot: disk
```

- 本に記載がある通り、`ata0-0: specified geometry doesn't fit on disk image` の警告は無視して、`Booting from Hard Disk...` のメッセージが出ることを確認した
- 起動オプションは本の指定通りとした
  - 参考: [Using Bochs](http://bochs.sourceforge.net/doc/docbook/user/using-bochs.html#COMMANDLINE)

```
bochs -q -f ../../env/.bochsrc -rc ../../env/cmd.init
```

- bat ファイルではなく sh ファイルとした
- env.bat と dev.bat は作らず、.zshrc で PATH を指定した。また Bochs を使う（QEMUを使わない）ため、boot.bat も作っていない
- 本と同様に、00_boot_only や 01_bpb などから box.sh 等を呼び出す想定で記載

## 13章: アセンブラによる制御構文と関数の記述例

- memcpy.s と memcmp.s を追加
  - 特に挙動確認はないので、追加しただけ

## 14章: リアルモードでの基本動作を実装する

- 各ステップごとに実装し、挙動確認をした
- 14.9 で macro.s にある .cyln が .syln とタイポされてたので、.cyln にする。また、resw が reww とタイポされているので、resw にする
  - [サポートページ](https://gihyo.jp/book/2019/978-4-297-10847-2/support) に記載あり
- 14.12 で get_mem_info.s にメモリを確保する記述を追加する
  - [サポートページ](https://gihyo.jp/book/2019/978-4-297-10847-2/support) に記載あり
- 14.13 で kbc.s の KBC_Data_Read の loopnz を loopz に修正する
  - [サポートページ](https://gihyo.jp/book/2019/978-4-297-10847-2/support) に記載あり
- 14.16 で lba_chs は lba_chs.s という名前で保存する

## 15章: プロテクトモードへの移行を実現する

- 記載の通り実装しただけ