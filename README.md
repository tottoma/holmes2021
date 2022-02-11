# holmes2021
ナレッジグラフ推論チャレンジ2021における  
**チーム:カカオ65%** のリポジトリです. 

# Rule 小説ルール
各小説それぞれの小説で描写されていたことを踏まえたルールです.  
内容については,プログラム内コメントや発表資料を参照して下さい.

# ex_Rule 常識オントロジー
小説の内容に関わらず,成立すると考えられたルールです.  
各小説ファイルに入っているex_...ファイルが常識オントロジーファイルです.  
内容については,プログラム内コメントや発表資料を参照して下さい.

# Data 
 このGithub内,dataには本文のデータ(完全,不完全(10%),不完全(25%)),  
 SpeckledBandにSpackledBandの推論で使用するファイル,  
 DevilsFootにDevilsFootの推論で使用するファイルです.  
 推論の際には,推論対象の小説名に対応する各フォルダ内のファイルを全てloadして下さい. 

# Environment 実行環境
OS : macOS Monterey ver12.0  
CPU : Apple M1 Pro  
Stardog server 7.8.2  
Java 1.8.0

**Windows環境はオススメしません.**  

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
 1. 左メニューバーにおける"Workspace"において,作成したリポジトリを選択し,Reasoningにチェックを入れ,以下のSPARQL検索を行う.


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
   }GROUP BY ?s
   ```
   ?sに犯人候補が列挙され, ?cに上記条件にどれだけ当てはまるかの回数を表示する.

2. 動機探し
   ```
   SELECT DISTICT * 
   WHERE{
       kd:Roylott kgc:haveMotivation ?o.
       }
   ```
   ?oにお金が出てきて,動機がお金であることが分かる.
   
### 各個別クエリ
1. 危険人物である
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:hasProperty kd:dangerous.
        }
    ```  
    ?sにロイロットが出てくる.  
    すなわち,ロイロットは危険人物であることが分かる.
  
2. 密室である
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:cannotEnter ?o.
        }
    ```  
    ?sに人間クラスに属する要素全て, ?oにジュリアの部屋が出てくる.  
    すなわち,人間はジュリアの部屋に入ることが出来ない,ジュリアの部屋は密室であることが分かる.

3. 密室である理由
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:cannotEnter ?o;
            kgc:from ?x.
        }
    ```  
    ?sに人間クラスに属する要素全て, ?oにジュリアの部屋, ?xに入れない場所(鎧戸・床・煙突・廊下・壁・ドア)が出てくる.  
    すなわち,ジュリアの部屋の各場所から入ることは出来ないことが分かる.

4. 動機があるか
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:haveMotivation ?o.
        }
    ```  
    ?sにロイロットとロマ, ?oに動機(金・協力)が出てくる.  
    すなわち,ロイロットはお金,ロマはロイロットへの協力が動機であることが分かる.

5. ダイイングメッセージに関係しているか
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:isRelatedTo kd:dying_message.
        }
    ```  
    ?sにロイロットとロマが出てくる.  
    すなわち,ロイロットとロマはロダイイングメッセージと関係があることが分かる.

6. 殺害現場にいたか
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:hasPredicate kd:wasAtTheScene.
        }
    ```  
    ?sにロイロットとロマが出てくる.  
    すなわち,ロイロットとロマは殺害現場にいたことが分かる.

7. どうやって殺したのか(殺害手段)
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:canKill ?o.
        }
    ```  
    ?sにヘビ,?oにジュリアが出てくる.  
    すなわち,ヘビがジュリアを殺したことが分かる.

8. 誰が殺したのか
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:canMurder ?o.
        }
    ``` 
    ?sにロイロット,?oにジュリアが出てくる.  
    すなわち,ロイロットがジュリアを殺したことが分かる.  
  
## Devils Foot 悪魔の足
 ## ブレンダ殺害事件
  ### テーマ検索クエリ
1. 犯人探し
   ```
   SELECT ?s (count(?s) as ?c) WHERE{
       {
           ?s ?p ?o.
           filter(?p = kgc:canKill)
       }union{
           ?s ?p ?o.
           filter(?p = kgc:canMurder && ?o = kd:Brenda)   
       }union{
           ?s ?p ?o.
           filter(?p = kgc:haveMotivationForBrenda) 
       }union{
           ?s ?p ?o.
           filter(?p = kgc:hasProperty && ?o = kd:dangerous) 
       }union{
           ?s ?p ?o.
           filter(?p = kgc:hasProperty && ?o = kd:wasAtTheScene1) 
       }union{
           ?s ?p ?o.
           filter(?p = kgc:isRelatedTo && ?o = kd:field_message_1) 
       }
   }GROUP BY ?s
   ```
   ?sに犯人候補が列挙され, ?cに上記条件にどれだけ当てはまるかの回数を表示する.
   
2. 動機探し
   ```
   SELECT DISTICT * 
   WHERE{
       kd:Mortimer kgc:haveMotivationForBrenda ?o.
       }
   ```
   ?oにお金が出てきて,お金が動機であることが分かる.  
   (kd:Mortimer以外でも検索自体は可能だが,結果が出てこない.  
    すなわち動機がないという結果になる.)

### 各個別クエリ

1. 危険人物である
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:hasProperty kd:dangerous.
        }
    ```  
    モーティマーとスタンデールは危険人物であることが分かる.

2. 現場の物証に関係しているか
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:isRelatedTo kd:field_message_1.
        }
    ```
    ブレンダ殺害事件には,現場の物証と関係しているものは存在しない.

3. 殺害現場にいたか
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:hasProperty kd:wasAtTheScene1.
        }
    ```
    殺害現場であるトリジェニス家にいた人物が, モーティマー,ブレンダ,ジョージ,オーウェン,ポーターの5人であること分かる.

4. どうやって殺したのか(殺害手段)
    ```
    SELECT ?o 
    WHERE{ 
        kd:Mortimer kgc:have ?o;
                    kgc:canMurder kd:Brenda.
        ?o kgc:canKill ?o2.
        } 
    ```
    殺した手段が魔足根であることが分かる.

5. 誰が殺したのか
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:canMurder kd:Brenda.
        }
    ``` 
    モーティマーとスタンデールがブレンダを殺せることが分かる.


 ## モーティマー殺害事件
  ### テーマ検索クエリ
1. 犯人探し
   ```
   SELECT ?s (count(?s) as ?c) WHERE{
       {
           ?s ?p ?o.
           filter(?p = kgc:canKill)
       }union{
           ?s ?p ?o.
           filter(?p = kgc:canMurder && ?o = kd:Mortimer)   
       }union{
           ?s ?p ?o.
           filter(?p = kgc:haveMotivationForMortimer) 
       }union{
           ?s ?p ?o.
           filter(?p = kgc:hasProperty && ?o = kd:dangerous) 
       }union{
           ?s ?p ?o.
           filter(?p = kgc:hasProperty && ?o = kd:wasAtTheScene2) 
       }union{
           ?s ?p ?o.
           filter(?p = kgc:isRelatedTo && ?o = kd:field_message_2) 
       }
   }GROUP BY ?s
   ```
   ?sに犯人候補が列挙され, ?cに上記条件にどれだけ当てはまるかの回数を表示する.
   
2. 動機探し
   ```
   SELECT DISTICT * 
   WHERE{
       [Murder Name] kgc:haveMotivationForMortimer ?o.
       }
   ```
   [Murder Name]には,1にて出力された犯人候補の中から任意の名前を入力する.  
   kd:Mortimerである場合は,?oに後悔が出てきて,後悔からの自殺が動機であることが分かる.  
   kd:Standaleである場合には,?oに復讐が出てきて,恋人の殺害への復讐が動機であることが分かる.

 ### 各個別クエリ
1. 危険人物である
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:hasProperty kd:dangerous.
        }
    ```  
    モーティマーとスタンデールは危険人物であることが分かる.

2. 現場の物証に関係しているか
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:isRelatedTo kd:field_message_2.
        }
    ```
    現場の物証と関係しているものは,小石とスタンデールである.

3. 殺害現場にいたか
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:hasProperty kd:wasAtTheScene2.
        }
    ```
    殺害現場である牧師館にいた人物が, モーティマーとスタンデールの2人であること分かる.

4. どうやって殺したのか(殺害手段)
    ```
    SELECT ?o 
    WHERE{ 
        kd:Standale kgc:have ?o;
                    kgc:canMurder kd:Mortimer.
        ?o kgc:canKill ?o2.
        } 
    ```
    ```
    SELECT ?o 
    WHERE{ 
        kd:Mortimer kgc:have ?o;
                    kgc:canMurder kd:Mortimer.
        ?o kgc:canKill ?o2.
        } 
    ```
    殺した手段が魔足根であることが分かる.

5. 誰が殺したのか
    ```
    SELECT DISTINCT *
    WHERE{
        ?s kgc:canMurder kd:Mortimer.
        }
    ``` 
    モーティマーとスタンデールがモーティマーを殺せることが分かる.



# 関連リンク
以下に当チャレンジにて用いたツールおよび関連リンクを紹介します.  

<span style = 'font-size:150%'>[**Github**](https://github.com/tottoma/holmes2021)</span>

<span style = 'font-size:150%'>[**Miro**](
https://miro.com/app/board/uXjVOY2VrOc=/?invite_link_id=111838331320)</span>

<span style = 'font-size:150%'>[**ナレッジグラフ推論チャレンジ2021**](https://challenge.knowledge-graph.jp/2021/)</span>


