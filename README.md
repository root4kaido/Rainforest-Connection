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
