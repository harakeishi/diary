# ウェブブラウザの仕組み
## pingについて
ICMPプロトコルを使っている

### 大まかにやっていること
- 自分から通信ができるか確認したい相手に `ICMP Echo Request` を送信する
- 返事（ `ICMP Echo Reply` ）が帰ってくるのを待っつ
- 結果を表示

### pingの処理の流れ
1. ソケットの作成
2. sendtoでパケットの送信
3. recvmsgでパケットの受信
4. writeで出力

### sendtoとかってなに？
システムコール
> OSが提供する機能をアプリケーションが利用する仕組みのこと

用意されたシステムコールを使うことでOSが提供する機能を利用できる

***豆知識***
`strace` コマンドを使うとアプリが発行したシステムコールをトレースできる

```bash:例
[例]
strace -o ping.txt ping -c 3 192.168.10.1
```
