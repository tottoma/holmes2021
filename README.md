# holmes2021
ナレッジグラフ推論チャレンジ2021における  
チーム:カカオ65%のリポジトリです.  
# rule ~小説ルール~
hoge
# external_data ~外部知識(常識知識)~
hogehoge
# data 
hogehogehoge
# Environment 実行環境
OS : macOS Monterey ver12.0  
CPU : Apple M1 Pro  
Stardog server 7.8.2  
Java 1.8.0


# Preparation 前準備
 1. 以下のURL先からOS対応のStardogのインストールを行う.  
    [Stardog Installation](https://docs.stardog.com/get-started/install-stardog/)
 2. 以下のURL先を参照して,ブラウザ上でStardogを起動する.  
    [Stardog Access Studio](https://docs.stardog.com/get-started/access-studio)  
 3. Javaのインストールを行う.(versionは1.8.0,インストールされていれば不要) 
 3. 左メニューバーにおける"Databases"より,新規リポジトリを作成する
 4. 作成したリポジトリにおいて,"Load Data"を押しttlファイルの読み込みを行う.  
    1. PREFIXに関しては,ルールファイル内で定義しているので"NameSpace"で指定する必要はないです.   
   (指定しても大丈夫です)
    1. 読み込むttlファイルは,本文のttlファイルおよびルールに関するttlファイルです.
 5. 左メニューバーにおける"Workspace"において,作成したリポジトリを選択し,Reasoningにチェックを入れ,以下のSPARQL検索を行う.

# How to Search 検索方法   

## Sparked Band まだらのひも  
    
1. 危険人物である
     ```
      SELECT DISTINCT *
      WHERE{
          ?s kgc:hasProperty kd:dangerous.
          }
     ```  
    ?sにロイロットが出てくる.
  
2. 密室である
     ```
      SELECT DISTINCT *
      WHERE{
          ?s kgc:cannotEnter ?o.
      }
     ```  
    ?sに人間クラスに属する要素全て, ?oにジュリアの部屋が出てくる.

3. 密室である理由
     ```
      SELECT DISTINCT *
      WHERE{
          ?s kgc:cannotEnter ?o;
             kgc:from ?x.
      }
     ```  
    ?sに人間クラスに属する要素全て, ?oにジュリアの部屋, ?xに入れない場所(鎧戸・床・煙突・廊下・壁・ドア)が出てくる.

4. 動機があるか
     ```
      SELECT DISTINCT *
      WHERE{
          ?s kgc:haveMotivation ?o.
          }
     ```  
    ?sにロイロットとロマ, ?oに動機(金・協力)が出てくる.

5. ダイイングメッセージに関係しているか
     ```
     SELECT DISTINCT *
     WHERE{
         ?s kgc:isRelatedTo kd:dying_message.
         }
     ```  
    ?sにロイロットとロマが出てくる.

6. 殺害現場にいたか
     ```
     SELECT DISTINCT *
     WHERE{
         ?s kgc:hasPredicate kd:wasAtTheScene.
         }
     ```  
    ?sにロイロットとロマが出てくる.

7. ジュリアを殺したのは誰か



8. どうやって殺したのか



