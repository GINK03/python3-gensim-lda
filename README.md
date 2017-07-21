# LDA example

## LDAについて

1. Poisson分布で語数を生成
2. Dirichlet分布でK個の出力をするモデルのパラメータを生成
3. 語wを話題zで分布から選択
1-3をM回繰り返す

（ベイズのエンコーダデコーダモデルに見える）

## gensimのLDAで使うコーパスのフォーマットについて  

DoW(Document Of Word)で表現する必要があり  
まず、あるドキュメントに関して、このようにする必要がある  

```console
[('アドバンテッジパートナーズ', 1), ('ホームプロダクツ', 1), ('居住まい', 1), ('ユニゾン・キャピタル', 1), ('のたうち回っ', 1), ('たとえれ', 1), ('元の木阿弥', 1)]
[('おぉ', 1), ('ぁぁぁ', 1), ('ボロッと', 1), ('摘み食い', 1), ('ドンピシャ', 1), ('こう着', 1), ('曲る', 1), ('ねじ込ん', 1), ('カッポン', 1), ('神々し', 1), ('かせげ', 1)]
[('辰典', 1), ('すごかっ', 1), ('石田秀範', 1), ('武田玲奈', 1), ('ヤダ', 1), ('真鈴', 1), ('BuzzFeed', 1), ('寝れ', 1), ('マリエッティ', 1), ('今年5月', 1), ('引き締まる', 1), ('楽しいっ', 1)]
[('帝都自動車交通', 1), ('コネクテッド', 2)]
```

かつ、単語にユニークな数値のIDを割り当てる必要があり、ListのListの形式でDocumentを生成する　　
```console
[(36399, 1), (2532, 1), (25, 1), (36400, 1)]
[(1581, 1), (1583, 1), (1587, 1), (1582, 1), (1586, 1), (1585, 1), (1584, 1)]
[(5750, 1), (5754, 1), (5753, 1), (5746, 1), (5747, 1), (5752, 1), (5751, 1), (3748, 1), (5749, 1), (5755, 1), (5748, 1)]
[(1030, 1), (1263, 1), (3366, 1), (3362, 1), (3364, 1), (3363, 1), (15, 1), (3368, 1), (3365, 1), (999, 1), (712, 1), (3367, 1)]
[(15248, 1), (5861, 2)]
```

## gensimによる学習

```python
model = LdaMulticore(gensim_corpus, workers={8}, num_topics={TOPICN})
```

TOPICNはトピック数、workersはCPUの数

## トピックの分布を表示する

```python
  model = pickle.loads( open('tmp/pickles/model.pkl', 'rb').read() )
  for topic in range(TOPICN):
    topn =  model.print_topic(topic, topn=100) 
    pairs = {}
    for pair in topn.split(' + '):
      weight, term = pair.split('*')
      term = term.replace('"', '')
      pairs[ index_term[int(term)] ] = float( weight )
    print( topic, sorted(pairs.items(), key=lambda x:x[1]*-1) )
```

実際に選定された重みがついたキーワードはこのようになっている(トップN件表示)  
```console
0 [('森友', 0.005), ('BuzzFeed', 0.004), ('籠池', 0.004), ('ネイマール', 0.003), ('社会保険料', 0.003), ('働き方改革', 0.003), ('＂', 0.003), ('Spotify', 0.002), ('安倍総理', 0.002), ('漏えい', 0.002)]
1 [('タカタ', 0.006), ('キャサリン妃', 0.005), ('マクロン', 0.004), ('ミケルソン', 0.003), ('昨季', 0.003), ('メーガン', 0.002), ('文在寅', 0.002), ('平昌五輪', 0.002), ('GPIF', 0.002), ('ふっかちゃん', 0.002)]
2 [('トランプ氏', 0.01), ('コミー', 0.007), ('トランプ大統領', 0.005), ('モスル', 0.004), ('昨季', 0.003), ('パリ協定', 0.003), ('トナミ運輸', 0.002), ('藤井聡太', 0.002), ('NAND', 0.002), ('クーリ ング・オフ', 0.002)]
3 [('藤井聡太', 0.003), ('企業コード', 0.003), ('働き方改革', 0.003), ('前年同期', 0.003), ('スノーデン', 0.003), ('グランピング', 0.002), ('ＥＵ', 0.002), ('オジサマ', 0.002), ('路線価', 0.002), ('贈与税', 0.002)]
4 [('海老蔵', 0.007), ('BuzzFeed', 0.006), ('都民ファースト', 0.005), ('小林麻央', 0.004), ('都議選', 0.003), ('メルカリ', 0.003), ('ランサムウェア', 0.003), ('Uber', 0.003), ('米林', 0.003), ('都議会議員選挙', 0.002)]
5 [('ふるさと納税', 0.006), ('インフルエンサー', 0.006), ('スポルチ', 0.003), ('ランサムウェア', 0.003), ('勝ち点6', 0.002), ('みね子', 0.002), ('クラフトビール', 0.002), ('今夏', 0.002), ('TWICE', 0.002), ('CGW', 0.002)]
6 [('ユベントス', 0.004), ('自動運転車', 0.003), ('ディープラーニング', 0.002), ('ＤｅＮＡ', 0.002), ('嗣永', 0.002), ('ポリアモリー', 0.002), ('SENSORS', 0.002), ('メッシ', 0.002), ('定期保険', 0.002), ('筒香', 0.002)]
7 [('利上げ', 0.007), ('文在寅', 0.005), ('FOMC', 0.005), ('ミニオン', 0.003), ('コネクテッド', 0.003), ('ダルビッシュ', 0.003), ('ECB', 0.003), ('日経平均', 0.003), ('円安', 0.003), ('ドラギ', 0.002)]
8 [('トランプ大統領', 0.017), ('AMOLED', 0.004), ('民泊', 0.003), ('apos', 0.003), ('(～', 0.003), ('FinTech', 0.002), ('コミー', 0.002), ('BuzzFeed', 0.002), ('NISA', 0.002), ('鈴鹿8耐', 0.002)]
9 [('都議選', 0.01), ('ヒアリ', 0.01), ('都民ファースト', 0.008), ('加計学園', 0.007), ('韓米', 0.006), ('お子さま', 0.004), ('トランプ大統領', 0.003), ('小池都知事', 0.003), ('BuzzFeed', 0.002), ('防衛相', 0.002)]
```

## 推して参る（予想)

```python
def infer():
  model = pickle.loads( open('tmp/pickles/model.pkl', 'rb').read() )
  words = pickle.loads( open('tmp/words.pkl', 'rb').read() )
  term_index = pickle.loads( open('tmp/pickles/term_index.pkl', 'rb').read() )
  documents = []
  for name in glob.glob('tmp/wakati/*'):
    terms = dict( Counter( [term_index[term] for term in open(name).read().split() if term in words] ) )
    document = [ (t, f) for t,f in terms.items() ]
    documents.append( document )
  for document in documents:
    print( model[document] )
```

出力としては、こんなものが期待できる
```console
[(0, 0.95263049489804907)]
[(4, 0.78961007444174169), (9, 0.13765851135887749)]
[(5, 0.93076412318561164)]
[(2, 0.96249726375718547)]
[(0, 0.033336967996157006), (1, 0.03333629941120636), (2, 0.34663365744992908), (3, 0.033334750531323239), (4, 0.38669021264707037), (5, 0.033333682735445042), (6, 0.03333390834993169), (7, 0.033333387870785824), (8, 0.033333799669263149), (9, 0.033333333338888264)]
[(0, 0.01250112109912109), (1, 0.012500475180188301), (2, 0.64653990449252441), (3, 0.012501880573022232), (4, 0.012501350558104852), (5, 0.012500646292880018), (6, 0.012501254006931684), (7, 0.25344885710930709), (8, 0.012502845208298127), (9, 0.012501665479622079)]
[(0, 0.012500020801422297), (1, 0.012500007156413834), (2, 0.012500407084273966), (3, 0.012500903241164764), (4, 0.012500042895959818), (5, 0.012500207579960541), (6, 0.012500278476085111), (7, 0.88749649418622301), (8, 0.012500339740885231), (9, 0.012501298837611313)]
[(0, 0.014285714289994497), (1, 0.014285796310506044), (2, 0.014285874899101774), (3, 0.014291296120021665), (4, 0.014287630403904556), (5, 0.014286538657006997), (6, 0.014285714289982283), (7, 0.8714183259854954), (8, 0.014285714290054803), (9, 0.014287394753931959)]
[(8, 0.91816775338220391)]
[(0, 0.01428861794693234), (1, 0.014285783849360235), (2, 0.014285990961874577), (3, 0.87141924170044605), (4, 0.014288089997698719), (5, 0.014285714299295069), (6, 0.014286519069011946), (7, 0.014286342772098779), (8, 0.014287184869409221), (9, 0.014286514533873002)]
```

## その他

LDAはストップワードは頻出する単語を、コーパスから取り除くことで、うまく表現することである 

今回は、Wikipediaから構築したtf-idf辞書から、重要度が高い単語のみの残す  
