---
title: "Diver OSINT CTF 2024 に参加しました"
emoji: "🏖️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["CTF", "writeup", "OSINT"]
published: true
---

## はじめに
2024年6月8日~9日に開催された[DIVER OSINT CTF](https://ctfd.diverctf.org/)のwriteupです。
所属する[いちぴろ・エクスプローラ](https://x.com/hcu_tech)で2チームを作り、参加しました。

## スコア
個人：７問/1057点
チーム：15問/1941点/72位
![](/images/Diver-0.png)



## 解けた問題
### imagetrack / easy
画像の撮影された郷土料理屋の店名を答えよ。
Flag形式: Diver24{店名}
![](/images/Diver-1.JPG)

:::details imagetrack 回答プロセス
画像の詳細を確認したところ、撮影した位置情報が含まれているのを確認しました。
![](/images/Diver-1-2.png)

ストリートビューで確認し、回答しました。
![](/images/Diver-1-4.png)
:::

### office / introduction
ファイルから得られるFlagを入力してください。

:::details office 回答プロセス
ODT (OpenDocument Text)形式で、そのまま開いてもフラグは見つかりませんでした。そこでfileコマンドで確認すると、ZIP形式であることを確認しました。
```shell
$ file mudai(office).odt
mudai(office).odt: Zip archive data, at least v2.0 to extract, compression method=deflate
```

拡張子をzipに変更し、解凍すると画像形式でフラグを見つけました。
:::


### 246 / introduction
画像が撮影された場所から最も近い交差点名を答えよ。
Flag形式: Diver24{交差点名}
![](/images/Diver-3-1.jpg)

:::details 246 回答プロセス
Exif情報は特に無いため、写真から以下の要素を推測しました。
- 県道246号線
- 点字ブロックが確認できるため大きな道路沿い
- 大きな橋、送電線があるため、大きな島の沿岸部
- 瀬戸内海近辺？


[日本の県道一覧 - Alt CVA](https://altcva.main.jp/pref/)というサイトで、県道の検索ができます。これらを確認し長府前田線であると推測しました。
![](/images/Diver-3-2.png)

県道246号線付近を確認すると、問題の橋や送電線を確認し、付近を調べると該当の場所を発見しました。
![](/images/Diver-3-3.png)
:::


### youtuber / medium
2023年、あるYouTuberが日本で無賃乗車や無銭飲食を行い、その様子を投稿したことで問題となった。
彼は日本である列車に無賃乗車をしていたところ、九州地方のある駅で一度捕まったが、そのまま逃走して別の列車に再び無賃乗車をした。
彼が捕まった駅と、乗り継いだ列車の列車番号を教えてほしい。なお、列車番号は時刻表に掲載されている形式で回答せよ。
Flag形式: Diver24{駅名_列車番号}
例えば、折尾駅で捕まり、456Mという列車に乗り換えた場合、Diver24{折尾_456M} となる。

:::details youtuber 回答プロセス
以前以下のニュースを見たことがあり、スムーズに解くことができました。
[BBC-日本で無賃乗車、動画公開したユーチューバーに批判殺到　「そんなつもりはなかった」と謝罪](https://www.bbc.com/japanese/67247960)

しかし、該当の動画はすでに削除されているため、[ニュース動画](https://www.youtube.com/watch?v=56X36sim3TM)を確認したところ、乗り継いだ列車がさくら572であり、次の停車駅が博多駅であることがわかります。
![](/images/Diver-4-1.png)

[おでかけネット](https://timetable.jr-odekake.net/train-timetable/104121?date=20240217)を確認し、停車駅が新鳥栖、列車番号が572Aであることがわかりました。
:::


### chiban / medium
画像の中央に写っている道路の地番は何か。
たとえば住所が 仙台市宮城野区二十人町 303-8 の場合、Flagは Diver24{303-8} となる。
また、地番には数字だけでなく漢字が含まれることもある。その場合は漢字表記のまま解答せよ。
![](/images/Diver-5-1.jpg)

:::details chiban 回答プロセス
画像から読み取れる情報は以下のとおりです。
- サンディ 7号店
- 餃子の満州
- 小学校の近く

"サンディ 7号店"で検索しても出てきませんでした。サンディは関西圏に多く出店するチェーン店なので、餃子の満州の店舗情報で関西の店舗を順番に確認していき、阪急茨木東口店であることがわかりました。
地番の確認には[地番検索くん](https://chiban-kensaku.com/)を利用し、地番を取得しました。
![](/images/Diver-5-3.png)
:::

### label / medium
この荷物の宛先として考えられる施設の郵便番号を教えてほしい。
例えば在日アメリカ大使館が答えの場合、Flagは Diver24{107-8420} となる。
![](/images/Diver-6-1.jpg)

:::details label 回答プロセス
最初は、下に小さく書かれているトラッキングナンバーを検索し続けましたが、該当の荷物は出てきませんでした。
そこでバーコードの下にある横長のQRコードのようなものに着目しました。これはrMQRコードというものです。[デンソー](https://www.denso-wave.com/ja/adcd/info/detail__220525.html)
そこでスマートフォンで読み取ると、宛先の住所が出てきました。
![](/images/Diver-6-2.png)
住所から郵便番号を検索し、回答しました。
:::

## 解けなかった問題
後日追記予定

## 感想
最近CTFに参加できておらず、久しぶりに解きましたがとても楽しかったです。来週のSECCON Beginners 2024も沢山解いていきたいです。
次はもう少し高得点を狙いたいですね。
最後になりますが、運営の皆様ありがとうございました！

https://twitter.com/ikepe_main/status/1799656138142790005

## その他
部員のWriteupのリンクです。

https://zenn.dev/cheru/articles/cheru-zenn-20240609

https://zenn.dev/abea0911/articles/74d1221197a135