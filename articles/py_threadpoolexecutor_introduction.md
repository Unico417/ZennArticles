---
title: "Pythonでマルチスレッド処理"
emoji: "💠"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["python","マルチスレッド", "初心者向け"]
published: true
---

Pythonでマルチスレッド処理をするには、`ThreadPoolExecutor`というクラスを使います。
このライブラリは、Python3.2以降でビルトインで使えます。

```python
from concurrent.futures import ThreadPoolExecutor
```

`ThreadPoolExecutor`はスレッドプールというだけあって、処理を溜めこむプールを持っているイメージです。
そのプールにある処理を順番に行っていきます。

# 最小構成のプログラム

簡単に、ただ処理をするならこれだけです。

```python
from concurrent.futures import ThreadPoolExecutor

# マルチスレッドさせたい処理
def process():
    print('process done!')
    return

with ThreadPoolExecutor() as executor:

    # スレッドプールに処理を追加
    exec.submit(process)

    # 全ての処理終了まで待つ
    exec.shutdown(wait=True)
```
**処理結果:**

```
process done!
```

これだとマルチスレッド処理の意味がないですね。

# たくさんの処理を登録してみる

100個ぐらい用意してあげましょう。

`ThreadPoolExecutor.submit()` の引数には、一つ目には関数、二つ目以降には引数を与えます。
試しに、何番目の処理が走っているか分かるように、引数を使って表示してみましょう。

```python
from concurrent.futures import ThreadPoolExecutor

# マルチスレッドさせたい処理
def process(n):
    print(f'process {n} done!')
    return

with ThreadPoolExecutor() as executor:

    for n in range(100):
        
        # スレッドプールに処理を追加
        exec.submit(process, n)

    # 全ての処理終了まで待つ
    exec.shutdown(wait=True)
```

**処理結果:**

```
process 0 done!
process 1 done! 
process 2 done! 
process 3 done! 
process 6 done! 
process 5 done! 
process 7 done! 
~~~~~ 省略 ~~~~~
process 82 done!
process 97 done!
process 84 done!
process 99 done!
```

おどろくことに、ただ `print()` しかしない処理でも順不同になるんですね。
処理負荷が全て同じなので、数字は順番に並ぶと思いました。

# ランダムに時間がかかる処理をする

もうちょっと、ランダムに負荷をかけてみましょう。

```python
from concurrent.futures import ThreadPoolExecutor
from random import random
from time import sleep

# マルチスレッドさせたい処理
def process(n):
    ''' 0.1～5.1秒かかる処理 '''
    print(f'START: process {n}')        # 開始メッセージ
    t = random() * 5.0 + 0.1            # t = 処理にかかる時間
    sleep(t)                            # 高負荷を想定して、止めてみる
    print(f'END: process {n} {t:.3f}s') # 完了後、終了メッセージ


with ThreadPoolExecutor() as executor:

    for n in range(100):
        # スレッドプールに処理を追加
        # n = 処理を追加した順番
        exec.submit(process, n)

    # 全ての処理終了まで待つ
    exec.shutdown(wait=True)
```

**処理結果:**

```
START: process 0
START: process 1
START: process 2
START: process 3
END: process 2 0.134s
START: process 4
END: process 0 0.743s
START: process 5
END: process 3 1.606s
START: process 6
~~~~~ 省略 ~~~~~
END: process 94 2.411s
START: process 98
END: process 96 2.700s
START: process 99
END: process 99 0.302s
END: process 95 4.628s
END: process 97 4.557s
END: process 98 4.325s
```

このページで出力結果だけ見ると分からないことですが･･･。

このプログラムを、実際に実行してみると面白いですね。
どこかの処理が終わった瞬間に、次の処理が走り始めているのがわかります。

# 処理の結果を得てみる

これまでの処理は投げっぱなしでした。

今回は、処理をした結果を取得します。
処理の結果は、`ThreadPoolExecutor.submit()`した戻り値に`.result()`メソッドを実行してあげると取得できます。

```python
from concurrent.futures import ThreadPoolExecutor
from random import random
from time import sleep

# マルチスレッドさせたい処理
def process(n):
    ''' 0.1～5.1秒かかる処理 '''
    print(f'START: process {n}')        # 開始メッセージ
    t = random() * 5.0 + 0.1            # t = 処理にかかる時間
    sleep(t)                            # 高負荷を想定して、止めてみる
    print(f'END: process {n} {t:.3f}s') # 完了後、終了メッセージ
    return n, t


with ThreadPoolExecutor() as executor:

    procs = []  # 結果入れ
    for n in range(100):
        # スレッドプールに処理を追加
        # n = 処理を追加した順番
        proc = exec.submit(process, n)
        procs.append(proc)

    # 全ての処理終了まで待つ
    exec.shutdown(wait=True)

    # 処理結果を表示する
    for p in procs:
        n, t = p.result()
        print(f'{n: >2}: {t:.3f}s')

```

**処理結果:**

```
START: process 0
START: process 1
START: process 2
START: process 3
END: process 0 2.793s
START: process 4
END: process 1 4.404s
START: process 5
END: process 3 4.404s
~~~~~ 省略 ~~~~
END: process 95 1.010s
START: process 97
END: process 97 0.765s
START: process 98
END: process 94 3.359s
START: process 99
END: process 98 2.102s
END: process 93 5.061s
END: process 99 1.783s
END: process 96 4.933s
 0: 2.793s
 1: 4.404s
 2: 4.911s
 3: 4.403s
 4: 4.470s
~~~~~ 省略 ~~~~~
95: 1.010s
96: 4.932s
97: 0.765s
98: 2.102s
99: 1.783s
```

処理も受け取れたついでに、投げた順番に結果を得ることもできました。
めでたし、めでたし。
