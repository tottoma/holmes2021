# holmes2021
ナレッジグラフ推論チャレンジ2021における  
**チーム:カカオ65%** のリポジトリです. 

# rule 小説ルール
各小説それぞれの小説で描写されていたことを踏まえたルールです.  
内容については, プログラム内コメントや発表資料を参照して下さい.

# external_data 常識オントロジー
小説の内容に関わらず, 成立すると考えられたルールです.  
内容については, プログラム内コメントや発表資料を参照して下さい.

# data 
このGithub内,dataには本文のデータ, external_dataに常識オントロジー, rule内各フォルダに小説ルールが入っています.  


# Environment 実行環境
OS : macOS Monterey ver12.0  
CPU : Apple M1 Pro  
Stardog server 7.8.2  
Java 1.8.0  
Windows環境はオススメしません.  

# Preparation 前準備
 1. 以下のURL先からOS対応のStardogのインストールを行う.  
    [Stardog Installation](https://docs.stardog.com/get-started/install-stardog/)
 2. 以下のURL先を参照して,ブラウザ上でStardogを起動する.  
    [Stardog Access Studio](https://docs.stardog.com/get-started/access-studio)  
 3. Javaのインストールを行う.(versionは1.8.0, インストールされていれば不要) 
 3. 左メニューバーにおける"Databases"より,新規リポジトリを作成する
 4. 作成したリポジトリにおいて"Load Data"を押し,ttlファイルの読み込みを行う.  
    1. PREFIXに関しては,ルールファイル内で定義しているので"NameSpace"で指定する必要はないです.   
    2. 読み込むttlファイルは,本文のttlファイルおよび小説ルール・常識オントロジーのttlファイルです.
 5. 左メニューバーにおける"Workspace"において,作成したリポジトリを選択し,Reasoningにチェックを入れ,以下のSPARQL検索を行う.


# How to Search 検索方法   
各検索に際して,検索文の前に以下の接頭辞を指定した上で検索を行って下さい.  
  
**まだらのひもの場合**
```
PREFIX kd: <http://kgc.knowledge-graph.jp/data/SpeckledBand/> 
PREFIX kgc: <http://kgc.knowledge-graph.jp/ontology/kgc.owl#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl/#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
```
  
**悪魔の足の場合**
```
PREFIX kd: <http://kgc.knowledge-graph.jp/data/DevilsFoot/> 
PREFIX kgc: <http://kgc.knowledge-graph.jp/ontology/kgc.owl#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl/#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
```  
  
**検索例 : まだらの紐にて危険人物を検索する場合**
```
PREFIX kd: <http://kgc.knowledge-graph.jp/data/SpeckledBand/> 
PREFIX kgc: <http://kgc.knowledge-graph.jp/ontology/kgc.owl#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl/#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT DISTINCT *
WHERE{
    ?s kgc:hasProperty kd:dangerous.
    }
````



## Speckled Band まだらのひも
### テーマ検索クエリ  
1. 犯人探し
   ```
   SELECT ?s (count(?s) as ?c) WHERE{
       {
           ?s ?p ?o.
           filter(?p = kgc:canKill && ?o = kd:Julia)
       }union{
           ?s ?p ?o.
           filter(?p = kgc:canMurder && ?o = kd:Julia)   
       }union{
           ?s ?p ?o.
           filter(?p = kgc:haveMotivation) 
       }union{
           ?s ?p ?o.
           filter(?p = kgc:hasProperty && ?o = kd:dangerous) 
       }union{
           ?s ?p ?o.
           filter(?p = kgc:hasPredicate && ?o = kd:wasAtTheScene) 
       }union{
           ?s ?p ?o.
           filter(?p = kgc:isRelatedTo && ?o = kd:dying_message) 
       }
   }
   ```
   ?sに犯人候補が列挙され, ?cに上記条件にどれだけ当てはまるかの回数を表示する.

2. 動機探し
   ```
   SELECT DISTICT * 
   WHERE{
       kd:Roylott kgc:haveMotivation ?o.
       }
   ```
   ?sに犯人候補が列挙され, ?cに上記条件にどれだけ当てはまるかの回数を表示する.
   
### 各個別クエリ
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

7. どうやって殺したのか
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:canKill ?o.
        }
    ```  
    ?sにヘビ,?oにジュリアが出てくる.


8. 誰が殺したのか
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:canMurder ?o.
        }
    ``` 
    ?sにロイロット,?oにジュリアが出てくる.  
  
## Sparked Band 悪魔の足
1. 




# 関連リンク
以下に当チャレンジにて用いたツールのリンクを紹介します.  


 - <span style = 'font-size:150%'>[**Miro**](
https://miro.com/app/board/uXjVOY2VrOc=/?invite_link_id=111838331320)</span>
