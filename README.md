# Rainforest-Connection


---
- 12/15

データを落として，中身みた．種類がよくわからん…？なんでtrain_fo, train_tpどっちにも同じidがいるんだろ． \
聞き耳した感じ，ラベル付が合っているような気がしないんだけど… \
[ベースライン](https://www.kaggle.com/fffrrt/all-in-one-rfcx-baseline-for-beginners)の1つを見たら，tmin,tmaxをwavの時点で取り出して，fmin,fmaxはmelへの変換の時使っていた．

---
- 12/18

上のベースラインは，csvに一致するwavをとってきて(ここでtmin, tmax, fmin, fmaxのメルにもしちゃう)，学習はそれを使ってるっぽい． \
音の長さとか，サンプリングレートはすべて一緒だった．やさしい． \
train_tpにないwavどうつかえばいいんだろ…？ \
第一陣の方針としては， 
1. pytorch lightningをつかう
2. モデルは適当(ResNet34？)
3. Wavは，csv(train_tpにあるやつ)
4. メルをつかうか，wavを毎回へんかんするかはどうしようかね．
5. 学習に使う区間は，tmin, tmaxがはいるようにランダムにとる．(2つ以上ある場合は，どちらでもよい)
6. fmax, fminは，02.ipynbによると，50~15000くらいをとればはいってきそう．

--- 
- 12/19

上記第一陣の方針を進めている．03.ipynbで，train_tpを整理した． \
01.ipynbで，pytorch_lightningを進めているが，なんだか学習が遅い気がする．後，スケジューラーはまだ対応していない．

--- 
- 12/20

01.ipynbで遅かった理由は，GPU使えていなかったからだった． \
04でとりあえず第一陣提出．あいかわらずスケジューラはまだ． \
05.ipynbでメルだした．ただ，今回学習速いから音でやっても良いかも．06は03のmel版． \
07で，ランダムのとり方をもとに戻したらちょっとのびた．あと，sigmoidじゃなくてsoftmaxのほうがよさそう．

---
- 12/21

08で，learning_rate小さくしたら伸びた．スケジューラはなんかうまく行かなかった．ピリオドは5がちょうどよさそう．(ほかは09でためした．) \
種類が，レコーディングidでこていしちゃったほうがいいのかもしれない…train_tpに合わせて変えたらなぜかおちた． \
メルのパラメータは要検討． 

---
- 12/22

なにすればのびんのかよくわかんなくなってきた． \
とりあえず，10_foldでミックスアップを試したが，LBは伸びなかった．そのあと，今回のメトリックをエポック終了時に出せるようにした． \
12は，音でやった．メルの代わりに．どっちがいいんだ．11は残骸．

---
- 12/23

14でaugmentためした．ガウスノイズきくんかこれ…きかなそう．いれたりいれなかったりを，13でためした． \
なんか，08とおなじしくみを13でやったはずなのに，さがってしまった．12と13(augなし)のさは多分殆ど無い．ピッチシフト試したいね．
15で，少しランダムの範囲を狭めたら，伸びた．

---
- 12/24

08_anotherで，08をどうやったらスコア悪くなるのか試したら，おためしのdataloader通すかどうかでかわった．意味不明． \
16.ipynbは，その結果を用いて音で読み込む方のスコア改善するか試したけど，微妙． \
17.ipynbは，メルでスケジューラーとかもう一回試したけどやっぱ微妙．もうわけがわからん…

---
- 12/25, 26

18, 19でmelの長さをいろいろ試したが，結局デフォルトに落ち着いた．21,22で，pklの中身をちょっと変えた．まえはrecording_idベースだったが，train_tpの行ベースにした． \
20で試したら，まあちょっといいかなってかんじ．23で切り方かえてみたけど下がった． \
24でそれを音にした．melじゃなくて．25はその準備．

---
- 12/27

26でガウシアンノイズ追加したら伸びた．27でピッチシフト，28でスペックオーグメントしたけどだめ．28はcvはのびたけど．29_はそれのおためし． \
29はtime shiftこれはきいてるか微妙．30はタイムシフト．31はミックスアップ．これらはだめ．32で，resnet34にしてみてる途中．

---
- 12/28

32で，結局モデルをいろいろかえてもだめだったので，OK_RANGEを半分から60パーにしたらちょっと伸びた． \
予測した結果をtrainに使おうと思って，33で予測した．34でそれをcsvにしている．

---
- 12/29, 30

35で前日のやつをtrainに追加したが，ダメでした．これをやるにはpannsのほうがよさそう． \
36で音の大きさ確認した．38_で，ノイズにするしくみつくって，37でためした，ノイズの大きさを調節すれば聞くかも． \
lossが毎回変わる事件が起きたが，37中の32でなんとか解決した．でも，0722くらいになってしまった． \
38で，ガウシアンノイズの方も大きさあげた．きくかもしれん． \
なんで0.722くらいになったのかとおもって，39で，5foldを試しているが，なぜか2番目以降すぐに学習が終わる現象になってる．

---
- 1/1, 2

上記問題はよくわからなかったので一個一個回して提出．まあ，0.77くらい． \
[これ](https://www.kaggle.com/khoongweihao/resnet34-more-augmentations-mixup-tta-inference)を参考に40.ipynbでfminとか変えてみたが意味なし． \
41でスケジューラいじったがやはり聞かない． \
42で，やはり切り方のいぞんはおおきそうなので調べてみたが，あまりいいのはなかった． \
43で，上記URLの切り方をパクったら，0.75くらいになった． \
44は，そのおためし． \
45で，自分のと組み合わせたら0.78くらいになった． \
46で，バリッドをちょっと変えた．同じファイル名を含んではリークしてしまいそうだったでので． \
上記URLは，フォークしたので，もうちょっと色々試す．あと，コンフォーマーも試す．

---
- 1/3, 4

47で，バリッドにリークがないように直した． \
48はimage augmentationたくさん試したけど意味なし． \
49で，SEDタスクをためしたがvalidはいいかんじなのにLBがさがらん． \
50で秒数をかえてみたけど意味なし．てかなんかめっちゃさがた． \
51でコンフォーマー試した．lossさがらん．
52はわすれた． \
53はCRNNためした．lossさがらん．　\
54はわすれた．


---
- 1/5~1/

あまりに結果が伸びないのでやる気をなくしていた． \
やはりSEDに希望がありそう．しんむらさんのディスカッションをよく読んでいる． \
RESNETベースのSEDでいろいろためした．55で．framewaiseのアウトプットよりclipwiseのほうが良さそうだった．自分的には． \
56はearlystopを外したら伸びた． \
57はlossをもどしたけど下がった．58はaudiomentationをためしたけど，なんかうまくいかなかった．augmentationはもうちょっと考える必要あり．\
59はネットワークためしている．movilenetはびみょうなきがする学習おっそい．efficientnet試し中．

---
- 1/9~11

60はモバイルネット．61はefficientnet自分で実装しないバージョン．やっぱ学習あんまうまく行かないなあ． \
62で5fold．やっとそれなりのスコア． \
63，trainにframeoutのlossくわえてないことにきづいた！！！というわけで，frameoutのほうを提出．微妙．あした，clipoutも出さなきゃ． \
64で，メルフィルタバンクをかすたむしたけど，inferenceがうまくいかなかった． \
ここで，なんとinferenceを10びょうでやっていたことにきづいた！！！泣ける． \
とおもってなおしてみたら，なんと10秒のほうが良いときたもんだ． \
とりあえず，以下の3つは5foldでやってみたい．\
- 10秒
- lossをちゃんとかえる
- melバンクをかえる \
上から順にやってく．

---
- 1/12~13

65は全部period10でまわしたけどだめ．66はぜんぶframeの方のlossでやってみたけどだめ． \
ここから，birdの一位のソリューションを真似して見る方向でやってみる． \
67はとりあえずclampをtanhにした． \
cvはよくなったけど，lbわるくなった

---
- 1/14

昨日に引き続き，[鳥の一位](https://www.kaggle.com/c/birdsong-recognition/discussion/183208)を参考にしてみている． \
68でなんとかdensenetでそれなりの値を出すとこまで来た． 30秒よりは10秒のほうが良さそう．また，完全にランダムよりはtpが入ってたほうが良さそう．\
69は，前のやつ(67)で，optimizer等を一位のものに変えたがダメだった． \
70は，68をefficientnetにしたけどダメだった． \
これに関してはもっと試したい． \
- augmentまだためせてない
- 68をresnestにする

---
- 1/15~17

- 71は10秒でResNestを5foldやったけどだめ． 
- 72は一位の，augmentをpinknoise以外やった 
- 73は，pinknoiseいれた．ききそう！ 
- 74は，なにかためした． 
- 75は，tanhうまく行かないので試行錯誤したけどやぱだめそう．
- 76は，resnestで良いスコアが出ているものに一位のノイズを加えてみたけど何故かメチャ下がった．
- 77は，一位のやつをいじった．resnetやったりしたけどだめ．
- 78は，67をepochごとにもでるほぞんして，どれか良いのないか試そうと思った．
- 79は，74のmelを24000にした．ちょっときいた．
- 80は，[SEDノートブック](https://www.kaggle.com/gopidurgaprasad/rfcx-sed-model-stater)を研究するために，pytorch lightningに移植したが，なぜか少しスコア落ちた．
- 81は，切り方を上記ノートブックに合わせたが，イマイチ．
- 82は，79をeffi2や0にしてみたが，イマイチ．

---
- 1/18~19

- 83は5fold．84はresnestの小さいの試したけど，だめ．85は5秒．10秒とほぼ変わらず． \
86はtorchlibrosaにかきかえ．87はそれをつかってmixup． ラムダかえたらcvは伸びたが，lbはだめ．．\
88は5foldmixup．89でエポック色々変えたけど，だめ．90でもっとラムダ変えたけど，だめ．悲しい．

---
- 1/20 

92は不均衡なおした．ちょっとさがった． \
93はtime_loss実装したけどもうだめだ．94はパラメータをガッツリ変えて，efficientにしてみた．ちょっとさがった． \
95はさいごだけmixupしない作戦．ちょっと上がった． \
92, 94, 95は5_foldやってもいいかも．96は，とりあえず86を5foldまわしてる．

---
- 1/21

97は，94を5foldやってる．こっちのがよさそうだった． \
98は，94にmixupやってみたけどなんか微妙． \
99は，b0かb4か，b4はだめそうだけど，b0はためしたほうがよさそう． \
100は，lossをかえた．がくしゅうはめっちゃはやくなったけど，さがった．epoch少なくても良いかも．

---
- 1/22~23

101_2で，centerをfalseにしたけどlbさがった．102はframeのbce．lwlrapはあがるけど，valid_lossに進捗見られず． \
103はなぞ．104はeffi_b0を５foldでまわしたけど微妙． \
valid_lossがけっこうlbとそうかんしてるっぽい．
105はわすれた．106は，pinknoiseけした．85のった！107はmixup． \
108は．focal lossリベンジ．

---
- 1/24

106_60で60エポック回したけど意味なし．113で，108の5foldやったらあがった．108でストライド半分にしたら，ちょっとあがるかもしれん． \
111で，どういうデータがミスってるか見た．音量差が激しいデータミスってそう．112でそのへんどうにかできないかやったけど意味なし． \
114はマルチラベルにした．よさそうだけど，115で5fold回したらさがっちゃった．工夫が必要そう． \

---
- 1/25~27

116で，mixupをaugment的に使ったけどダメだった．117はわすれた． \
118は，denoiseした．よさげだったので，119で5fold．0.88こえた！ \
120でピッチシフト． \
121はtimeひきのばし． \
122はピッチシフト5fold． \
123は最終層でmix．だめだった． \
124はdenoiseとオリジナル全部使ったけどダメ． \
そして，validでミスってたことに気づく．121_1と，119はとりあえずvalid直して回してる．

---
- 1/28~30

このへんからまじでコーネルなんとかコンペを見始めた． \
125で，denoiseのバリデーションを直した．16で，originalのバリデーションを直した． \
127で，songtypeを別にしたけど，ダメでした． \
128で，バッチノーマライゼーションを消したけど下がった． ので，mixup unionをためしたけど，だめ．\
129で，画像累乗にしたけど，cvよくなってlbさがった．130はそれをoriginalでやったけどおなじ． \
132はmixupunion200epochたぶん．133は累乗したときのバッチノーマライゼーションいじった． \
134はランダムに累乗．だめだこりゃ．135はタイムマスクとフレックマスク追加．きいたかも？ \
136はそのた，オーグメントとかこーねるではいってたやつもいろいろためした．最終的にはモデル変えてみた． \
gammatone，bcラーニングはちゃんとできたらやってみたいけどできるかなあ．

133はmixupunion5foldたぶん．

---
- 2/1~3

139はクロマベクトル． \
140は外部DATA．1141, 142でセカンダリーラベルでノイズver． \
143でmixupランダム． \
144はインバランス5fold.ちょっとさがっちゃった． \
145はパラメータとか全部denseにもどしたdenoiseバージョン． \
146はガンマトンフィルタ．さがった． \
148は，originalでいままでのどうか．1_time, freq 2_defolt, 3_delta, 4_background

---
- 2/4~5

137で，inferenceを色々実験した．hopを2秒にしたのはまあかわらずだったが，nocallみたいに予測確率がどれも0.3を超えない時全部0にしたらちょびっとよくなった． \
150は長い時，60%かぶってたらにしてみた．だめそう． \
151は，jikkenでいろいろみてみたら，mixup機能してる？？というのと，ガウスノイズ，意味ある？？というのが気になった． \
151でmixupするときに音量合わせてみたら，ちょっとよくなった． \
152はガウスノイズいじってみたけど意味なし．
153は151の5fold．


---
- 2/6~8

156でmeanwhereをがんばってやったのにだめだった〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜 \
158は，ネガティブラーニング的なことをぷりトレインにしてみたけど，だめだった．

--- 
- 2/9~11

159は，SENetをためしたけど，だめ．160はrainをバックグラウンドに追加してみたけどダメだった． ←オリジナルに追加はあり？\
162はじょーりゅーについて色々実験．163は5fold．164はじょーりゅーでラベルスムース．だめ．165はおりじなる． \
そろそろアンサンブルどうするか．168は，でノイズで秒数変えた．4秒を取り入れようかな． \
169はオリジナル． \
170は，モデル変えた．resnet34はよさそう．

---
- 2/12~14

いろいろやったが，スコアを向上させるものはなし． \
アンサンブルは，5-foldと4-foldのresnet34，effib-0の4通りかなあと言った感じ． \
秒数かえるのはよくなさそう．また，オリジナルのデータも微妙． \
175はresnet34．176は4秒5fold．177はoriginalとノイズ除去後のデータでノイズ除去後をバリデーションにしてみたが，cv伸びてlb下がった． 
