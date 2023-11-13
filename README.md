# マンデルブロ集合を描画するPythonコード

下記のページに掲載されたPythonコードを実行してみた。

[マンデルブロ集合の描画](https://qiita.com/r_09/items/b6c83ee15717da21371e)

Pythonするのが久しぶりだったので、環境を設定する手順を思い出すのに手こずった。メモしておく。

Pythonの環境をする手順を自分なりに整理した資料が下記にある。python3とpyenvとpipenvのインストールについてはこの資料を参照のこと。

https://github.com/kazurayam/MyPythonProjectTemplate


プロジェクトのためのディレクトリを作った。

```
$ cd ~/github
$ mkdir mandelbrot-set-python
```

プロジェクトのディレクトリにcdして
```
$ cd mandelbrot-set-python
```

Python仮想環境を作った。
```
$ pipenv --python 3
...
$ pipenv --venv
/Users/kazuakiurayama/.local/share/virtualenvs/mandelbrot-set-python-FfkQujD9
```

NumpyとMatplotlibをインストールした
```
$ pipenv install numpy
...
$ pipenv install matplotlib
```

[Numba, an open source JIT compiler that translates a subset of Python and NumPy code into fast machine code](https://numba.pydata.org/)をインストールした。

```
$ pipenv install numba
```

これで外部ライブラリのインストールはできた。

コードをコピペした。ただし `autojit` はもう使えないよとエラーがでた。そこで `jit` に書き換えた。

```
import numpy
from numba import jit
import matplotlib.pyplot as plt

@jit(nopython=True)
def mandelbrot(Re, Im, max_iter):
    c = complex(Re, Im)
    z = 0.0j

    for i in range(max_iter):
        z = z*z + c
        if(z.real*z.real + z.imag*z.imag) >= 4:
            return i
    return max_iter

columns = 3000
rows = 3000

result = numpy.zeros([rows, columns])
for row_index, Re in enumerate(numpy.linspace(-2, 1, num=rows)):
    for column_index, Im in enumerate(numpy.linspace(-1, 1, num=columns)):
        result[row_index, column_index] = mandelbrot(Re, Im , 100)

plt.figure(dpi=120)
plt.imshow(result.T, cmap='bone', interpolation='bilinear', extent=[-2, 1, -1, 1])
plt.xticks(color='None')
plt.yticks(color='None')
plt.tick_params(length=0)
plt.show()
```

このコードを実行するには、pipenvを経由してシェルを起動し、pythonコマンドを実行する。そうすることで上記でインストールした外部モジュールをimportできる環境でコードを実行することができる。
```
$ pipenv shell
Launching subshell in virtual environment...
 . /Users/kazuakiurayama/.local/share/virtualenvs/mandelbrot-set-python-FfkQujD9/bin/activate
:~/github/mandelbrot-set-python (master #)
$  . /Users/kazuakiurayama/.local/share/virtualenvs/mandelbrot-set-python-FfkQujD9/bin/activate
(mandelbrot-set-python) :~/github/mandelbrot-set-python (master #)

```

```
$ python mandelbrot.python
```

動いた。
