# 回帰について
## 回帰とは
あるデータ$x$,$y$において、$y = f(x)$という連続的なモデルを当てはめること。<br>
単回帰や多項式回帰、重回帰などの種類がある。<br>
上記の三つの違いは以下の通り。<br>

| 名前 | 説明 | 回帰の式 |
| ---- | ---- | ---- |
| 単回帰 | 変数が一種類で一次式 | $f(x) = ax + b$ |
| 多項式回帰 | 変数が一種類で多項式 | $f(x) = ax^3 + bx^2 + cx + d$ |
| 重回帰 | 変数が一杯 | $f(x) = ax + bu + cw + d$ |

## 単回帰(最小二乗法)
最小二乗法は単回帰を行うための**手法**の一つ<br>
データごとに二乗誤差を計算、その和(いわゆる誤差の二乗和)を誤差関数と定義して、
誤差関数が最小になるようにする。<br>
具体的には誤差関数を偏微分して、求める。<br>

n個のデータ$(x_1,y_1),(x_2,y_2),...,(x_n,y_n)$に対し、<br>
回帰式$y(x) = ax + b$に回帰する。<br>
i番目のデータの回帰式との誤差は<br>
$\varepsilon_i = y_i - (ax_i + b)$<br>
その二乗和(誤差関数)は<br>
$\displaystyle S=\sum^{n}_{i=1} {\varepsilon_i}^2=\sum^{n}_{i=1} \{y_i - (ax_i + b)\}^2$<br>
でなんやかんやすると、直接パラメータ$a$と$b$が出せる。<br>
$\left.
    \begin{matrix}
        a = \frac{
        \displaystyle \sum^{n}_{i=1} x_i \sum^{n}_{i=1} y_i - n\sum^{n}_{i=1} x_iy_i
        }
        {
            (\displaystyle \sum^{n}_{i=1} x_i)^2 - n\sum^{n}_{i=1} {x_i}^2
        } \cr
        b = \frac{
        \displaystyle \sum^{n}_{i=1} x_i\sum^{n}_{i=1} x_iy_i - \sum^{n}_{i=1} {x_i}^2 \sum^{n}_{i=1} y_i
        }
        {
            (\displaystyle \sum^{n}_{i=1} x_i)^2 - n\sum^{n}_{i=1} {x_i}^2
        }
    \end{matrix}
\right\}$<br>
これが最小二乗法による単回帰のやり方である。<br>

### 最小二乗法の導出
改めて二乗和(誤差関数)は<br>
$\displaystyle S=\sum^{n}_{i=1} {\varepsilon_i}^2=\sum^{n}_{i=1} \{y_i - (ax_i + b)\}^2$<br>
こいつをパラメータ$a,b$でそれぞれ偏微分すると<br>
$\displaystyle \frac{\partial S}{\partial a} = \sum^{n}_{i=1} 2\{y_i - (ax_i + b)\}(-x_i)=-2\sum^{n}_{i=1} x_i\{y_i - (ax_i + b)\}$<br>
$\displaystyle \frac{\partial S}{\partial b} = \sum^{n}_{i=1} 2\{y_i - (ax_i + b)\}(-1)=-2\sum^{n}_{i=1} \{y_i - (ax_i + b)\}$<br>
偏微分が0のとき、最小やろ！の精神(最適解に近づくとSは減少傾向)で連立すると<br>
$\left.
    \begin{matrix}
          \displaystyle -2\sum^{n}_{i=1} x_i\{y_i - (ax_i + b)\}=0 \cr
          \displaystyle -2\sum^{n}_{i=1} \{y_i - (ax_i + b)\}=0
    \end{matrix}
\right\}$<br>
上の式を整理していくｩ<br>
$\begin{aligned}
    \displaystyle -2\sum^{n}_{i=1} x_i\{y_i - (ax_i + b)\} &= 0 \cr
    \sum^{n}_{i=1} x_i\{y_i-(ax_i + b)\} &= 0 \cr
    \sum^{n}_{i=1} x_i(y_i - ax_i - b) &= 0 \cr
    \sum^{n}_{i=1} (x_iy_i - a{x_i}^2 - bx_i) &= 0 \cr
    \sum^{n}_{i=1} x_iy_i &= \sum^{n}_{i=1} (a{x_i}^2 + bx_i) \cr
    &= a \sum^{n}_{i=1} {x_i}^2 + b\sum^{n}_{i=1} {x_i}
\end{aligned}$<br>
下の式を整理していくｩ<br>
$\begin{aligned}
    \displaystyle -2 \sum^{n}_{i=1} \{y_i - (ax_i + b)\} &= 0 \cr
    \sum^{n}_{i=1} \{y_i - (ax_i + b)\} &= 0 \cr
    \sum^{n}_{i=1} (y_i - ax_i - b) &= 0 \cr
    \sum^{n}_{i=1} y_i &= \sum^{n}_{i=1} (ax_i + b) \cr
    &=  a\sum^{n}_{i=1} {x_i} + nb
\end{aligned}$<br>
ということで整理された連立方程式はこちら<br>
$\left.
    \begin{matrix}
        \displaystyle \sum^{n}_{i=1} x_iy_i = a\sum^{n}_{i=1} {x_i}^2 + b\sum^{n}_{i=1} {x_i} \cr
        \displaystyle \sum^{n}_{i=1} y_i = a\sum^{n}_{i=1} {x_i} + nb
    \end{matrix}
\right\}$<br>
下の式から$b$をもとめて<br>
$\displaystyle b = \frac{\displaystyle \sum^{n}_{i=1} y_i - a\sum^{n}_{i=1} {x_i}}{n}$<br>
上の式にぶち込む<br>
$\begin{aligned}
    \displaystyle \sum^{n}_{i=1} x_iy_i &= a\sum^{n}_{i=1} {x_i}^2 + \frac{
        \displaystyle \sum^{n}_{i=1} y_i - a\sum^{n}_{i=1} {x_i}
    }
    {
        n
    }
    \sum^{n}_{i=1} {x_i} \cr
    \sum^{n}_{i=1} x_iy_i &= a\sum^{n}_{i=1} {x_i}^2 + \frac{1}{n} \sum^{n}_{i=1} x_i \sum^{n}_{i=1} y_i - \frac{a}{n} (\sum^{n}_{i=1} x_i)^2 \cr
    n\sum^{n}_{i=1} x_iy_i &= an\sum^{n}_{i=1} {x_i}^2 + \sum^{n}_{i=1} x_i \sum^{n}_{i=1} y_i - a(\sum^{n}_{i=1} x_i)^2 \cr
    a(\sum^{n}_{i=1} x_i)^2 - an\sum^{n}_{i=1} {x_i}^2 &= \sum^{n}_{i=1} x_i \sum^{n}_{i=1} y_i - n\sum^{n}_{i=1} x_iy_i \cr
    a \{(\sum^{n}_{i=1} x_i)^2 - n\sum^{n}_{i=1} {x_i}^2\} &= \sum^{n}_{i=1} x_i \sum^{n}_{i=1} y_i - n\sum^{n}_{i=1} x_iy_i \cr
    a &= \frac{
        \displaystyle \sum^{n}_{i=1} x_i \sum^{n}_{i=1} y_i - n\sum^{n}_{i=1} x_iy_i
    }
    {
        (\displaystyle \sum^{n}_{i=1} x_i)^2 - n\sum^{n}_{i=1} {x_i}^2
    }
\end{aligned}$<br>
$b$の式にぶち込んで<br>
$\begin{aligned}
     b &= \frac{
        \displaystyle \sum^{n}_{i=1} y_i - \frac{
            \displaystyle \sum^{n}_{i=1} x_i \sum^{n}_{i=1} y_i - n\sum^{n}_{i=1} x_iy_i
        }
        {
            (\displaystyle \sum^{n}_{i=1} x_i)^2 - n\sum^{n}_{i=1} {x_i}^2
        }
         \sum^{n}_{i=1} {x_i}
    }
    {
        n
    } \cr
    b &= \frac{
        \displaystyle \sum^{n}_{i=1} y_i}
        {
            n}
     - \frac{
        \displaystyle \sum^{n}_{i=1} x_i \sum^{n}_{i=1} y_i - n\sum^{n}_{i=1} x_iy_i}
    {
        n\{(\displaystyle \sum^{n}_{i=1} x_i)^2 - n\sum^{n}_{i=1} {x_i}^2\}} 
    \sum^{n}_{i=1} {x_i} \cr
    b &= \frac{
    \displaystyle \sum^{n}_{i=1} y_i \{(\sum^{n}_{i=1} x_i)^2 - n\sum^{n}_{i=1} {x_i}^2 \} - (\sum^{n}_{i=1} x_i)^2 \sum^{n}_{i=1} y_i + n\sum^{n}_{i=1} x_i\sum^{n}_{i=1} x_iy_i 
    }
    {
        n\{(\displaystyle \sum^{n}_{i=1} x_i)^2 - n\sum^{n}_{i=1} {x_i}^2\}} \cr
    b &= \frac{
    \displaystyle (\sum^{n}_{i=1} x_i)^2\sum^{n}_{i=1} y_i - n\sum^{n}_{i=1} {x_i}^2 \sum^{n}_{i=1} y_i - (\sum^{n}_{i=1} x_i)^2 \sum^{n}_{i=1} y_i + n\sum^{n}_{i=1} x_i\sum^{n}_{i=1} x_iy_i
    }
    {
        n\{(\displaystyle \sum^{n}_{i=1} x_i)^2 - n\sum^{n}_{i=1} {x_i}^2\}
    } \cr
    b &= \frac{
        \displaystyle n\sum^{n}_{i=1} x_i\sum^{n}_{i=1} x_iy_i - n\sum^{n}_{i=1} {x_i}^2 \sum^{n}_{i=1} y_i
    }
    {
        n\{(\displaystyle \sum^{n}_{i=1} x_i)^2 - n\sum^{n}_{i=1} {x_i}^2\}
    } \cr
    b &= \frac{
        \displaystyle \sum^{n}_{i=1} x_i\sum^{n}_{i=1} x_iy_i - \sum^{n}_{i=1} {x_i}^2 \sum^{n}_{i=1} y_i
    }
    {
        (\displaystyle \sum^{n}_{i=1} x_i)^2 - n\sum^{n}_{i=1} {x_i}^2
    }
\end{aligned}$<br>

### 最小二乗法と統計学的指標との関係
分散公式は以下の通り<br>
$\displaystyle \sigma_x^2 = \bar{x^2} - \bar{x}^2 = \frac{1}{n} \sum^n_{i=1} {x_i}^2 - (\frac{1}{n} \sum^n_{i=1} x_i)^2$<br>
ただし$\bar{x}$は$x$の平均で、$\bar{x^2}$は$x$の二乗平均<br>
また共分散公式は以下の通り<br>
$\displaystyle \sigma_{xy} = \bar{xy} - \bar{x}\bar{y} = \frac{1}{n} \sum^n_{i=1} {x_i}{y_i} - (\frac{1}{n} \sum^n_{i=1} x_i)(\frac{1}{n} \sum^n_{i=1} y_i)$<br>
ただし$\bar{x}$は$x$の平均で、$\bar{y}$は$y$の平均、$\bar{xy}$は$xy$の平均<br>

**以上の公式を踏まえて**もっかい最小二乗法の$a$と$b$の式を見てみる。<br>
$\left.
    \begin{matrix}
        a = \frac{
        \displaystyle \sum^{n}_{i=1} x_i \sum^{n}_{i=1} y_i - n\sum^{n}_{i=1} x_iy_i
        }
        {
            (\displaystyle \sum^{n}_{i=1} x_i)^2 - n\sum^{n}_{i=1} {x_i}^2
        } \cr
        b = \frac{
        \displaystyle \sum^{n}_{i=1} x_i\sum^{n}_{i=1} x_iy_i - \sum^{n}_{i=1} {x_i}^2 \sum^{n}_{i=1} y_i
        }
        {
            (\displaystyle \sum^{n}_{i=1} x_i)^2 - n\sum^{n}_{i=1} {x_i}^2
        }
        = \frac{\displaystyle \sum^{n}_{i=1} y_i - a\sum^{n}_{i=1} {x_i}}{n}
    \end{matrix}
\right\}$<br>

**なんか似てね？**<br>
$a$について<br>
$\begin{aligned}
    a &= \frac{
        \displaystyle \sum^{n}_{i=1} x_i \sum^{n}_{i=1} y_i - n\sum^{n}_{i=1} x_iy_i
    }
    {
        (\displaystyle \sum^{n}_{i=1} x_i)^2 - n\sum^{n}_{i=1} {x_i}^2
    } \cr
    a &= \frac{
        \displaystyle \frac{1}{n}\sum^{n}_{i=1} x_i \frac{1}{n}\sum^{n}_{i=1} y_i - \frac{1}{n}\sum^{n}_{i=1} x_iy_i
    }
    {
        (\displaystyle \frac{1}{n}\sum^{n}_{i=1} x_i)^2 - \frac{1}{n}\sum^{n}_{i=1} {x_i}^2
    } \cr
    a &= \frac{
        \sigma_{xy}
    }
    {
        \sigma_x^2 
    } \cr
\end{aligned}$<br>
$b$について<br>
$\begin{aligned}
    b &= \frac{\displaystyle \sum^{n}_{i=1} y_i - a\sum^{n}_{i=1} {x_i}}{n} \cr
    &= \frac{1}{n}\sum^{n}_{i=1} y_i - a\frac{1}{n}\sum^{n}_{i=1} {x_i} \cr
    &= \bar{y} - \frac{\sigma_{xy}}{\sigma_x^2} \bar{x}
\end{aligned}$<br>
といった感じで、すっきりした見た目にすることができる。<br>

## 重回帰(最小二乗法の応用)

重回帰ってなーに？を書く(WIP)<br>

n個のデータ$(x_1,w_1,y_1),(x_2,w_2,y_2),...,(x_n,w_n,y_n)$に対し、<br>
今回は回帰式$y=ax+bw+c$に回帰する。<br>
$\varepsilon_i = y_i - (ax_i + bw_i + c)$<br>
その二乗和(誤差関数)は<br>
$\displaystyle S=\sum^{n}_{i=1} {\varepsilon_i}^2=\sum^{n}_{i=1} \{y_i - (ax_i + bw_i + c)\}^2$<br>
でなんやかんやすると、直接パラメータ$a$、$b$と$c$が出せる。<br>

### 重回帰分析の導出
改めて二乗和(誤差関数)は<br>
$\displaystyle S=\sum^{n}_{i=1} {\varepsilon_i}^2=\sum^{n}_{i=1} \{y_i - (ax_i + bw_i + c)\}^2$<br>
こいつをパラメータ$a,b,c$でそれぞれ偏微分すると<br>
$\displaystyle \frac{\partial S}{\partial a} = \sum^{n}_{i=1} 2\{y_i - (ax_i + bw_i + c)\}(-x_i)=-2\sum^{n}_{i=1} x_i\{y_i - (ax_i + bw_i + c)\}$<br>
$\displaystyle \frac{\partial S}{\partial b} = \sum^{n}_{i=1} 2\{y_i - (ax_i + bw_i + c)\}(-w_i)=-2\sum^{n}_{i=1} w_i\{y_i - (ax_i + bw_i + c)\}$<br>
$\displaystyle \frac{\partial S}{\partial c} = \sum^{n}_{i=1} 2\{y_i - (ax_i + bw_i + c)\}(-1)=-2\sum^{n}_{i=1} \{y_i - (ax_i + bw_i + c)\}$<br>
偏微分が0のとき、最小やろ！の精神(最適解に近づくとSは減少傾向)で連立すると<br>
$\left.
    \begin{matrix}
          \displaystyle -2\sum^{n}_{i=1} x_i\{y_i - (ax_i + bw_i + c)\} = 0 \cr
          \displaystyle -2\sum^{n}_{i=1} w_i\{y_i - (ax_i + bw_i + c)\} = 0 \cr
          \displaystyle -2\sum^{n}_{i=1} \{y_i - (ax_i + bw_i + c)\} = 0
    \end{matrix}
\right\}$<br>
上の式を整理していくｩ<br>
$\begin{aligned}
    \displaystyle -2\sum^{n}_{i=1} x_i\{y_i - (ax_i + bw_i + c)\} &= 0 \cr
    \sum^{n}_{i=1} x_i\{y_i - (ax_i + bw_i + c)\} &= 0 \cr
    \sum^{n}_{i=1} x_i\{y_i - ax_i - bw_i - c\} &= 0 \cr
    \sum^{n}_{i=1} (x_iy_i - a{x_i}^2 - bw_ix_i - cx_i) &= 0 \cr
    \sum^{n}_{i=1} x_iy_i &= \sum^{n}_{i=1} (a{x_i}^2 + bw_ix_i + cx_i)\cr
    &= a\sum^{n}_{i=1} {x_i}^2 + b\sum^{n}_{i=1} w_ix_i + c\sum^{n}_{i=1} x_i
\end{aligned}$<br>
真ん中の式を整理していくｩ<br>
$\begin{aligned}
    \displaystyle -2\sum^{n}_{i=1} w_i\{y_i - (ax_i + bw_i + c)\} &= 0 \cr
    \sum^{n}_{i=1} w_i\{y_i - (ax_i + bw_i + c)\} &= 0 \cr
    \sum^{n}_{i=1} w_i\{y_i - ax_i - bw_i - c\} &= 0 \cr
    \sum^{n}_{i=1} (w_iy_i - aw_ix_i - b{w_i}^2 - cw_i) &= 0 \cr
    \sum^{n}_{i=1} w_iy_i &= \sum^{n}_{i=1} (aw_ix_i + b{w_i}^2 + cw_i) \cr
    &= a\sum^{n}_{i=1} w_ix_i + b\sum^{n}_{i=1} {w_i}^2 + c\sum^{n}_{i=1} w_i
\end{aligned}$<br>
下の式を整理していくｩ<br>
$\begin{aligned}
    \displaystyle -2\sum^{n}_{i=1} \{y_i - (ax_i + bw_i + c)\} &= 0 \cr
    \sum^{n}_{i=1} \{y_i - (ax_i + bw_i + c)\} &= 0 \cr
    \sum^{n}_{i=1} (y_i - ax_i - bw_i - c) &= 0 \cr
    \sum^{n}_{i=1} y_i &= \sum^{n}_{i=1} (ax_i + bw_i + c) \cr
    &= a\sum^{n}_{i=1} x_i + b\sum^{n}_{i=1} w_i + nc
\end{aligned}$<br>
ということで整理された連立方程式はこちら<br>
$\left.
    \begin{matrix}
        \displaystyle \sum^{n}_{i=1} x_iy_i = a\sum^{n}_{i=1} {x_i}^2 + b\sum^{n}_{i=1} w_ix_i + c\sum^{n}_{i=1} x_i \cr
        \displaystyle \sum^{n}_{i=1} w_iy_i = a\sum^{n}_{i=1} w_ix_i + b\sum^{n}_{i=1} {w_i}^2 + c\sum^{n}_{i=1} w_i \cr
        \displaystyle \sum^{n}_{i=1} y_i = a\sum^{n}_{i=1} x_i + b\sum^{n}_{i=1} w_i + nc
    \end{matrix}
\right\}$<br>
下の式から$c$をもとめて<br>
$\displaystyle c = \frac{\displaystyle \sum^{n}_{i=1} y_i - a\sum^{n}_{i=1} {x_i} - b\sum^{n}_{i=1} {w_i}}{n}$<br>
真ん中の式にぶち込んで$b$を求める<br>
$\begin{aligned}
    \displaystyle \sum^{n}_{i=1} w_iy_i &= a\sum^{n}_{i=1} w_ix_i + b\sum^{n}_{i=1} {w_i}^2 + (\frac{\displaystyle \sum^{n}_{i=1} y_i - a\sum^{n}_{i=1} {x_i} - b\sum^{n}_{i=1} {w_i}}{n})\sum^{n}_{i=1} w_i \cr
    \displaystyle \sum^{n}_{i=1} w_iy_i &= a\sum^{n}_{i=1} w_ix_i + b\sum^{n}_{i=1} {w_i}^2 + \frac{1}{n}\sum^{n}_{i=1} w_i\sum^{n}_{i=1} y_i - \frac{a}{n} \sum^{n}_{i=1} w_i\sum^{n}_{i=1} x_i - \frac{b}{n} (\sum^{n}_{i=1} w_i)^2 \cr
    \displaystyle b\{ \frac{1}{n} (\sum^{n}_{i=1} w_i)^2 - 1\sum^{n}_{i=1} {w_i}^2 \}&= (\frac{1}{n}\sum^{n}_{i=1} w_i\sum^{n}_{i=1} y_i - \sum^{n}_{i=1} w_iy_i) + a(\sum^{n}_{i=1} w_ix_i - \frac{1}{n} \sum^{n}_{i=1} w_i\sum^{n}_{i=1} x_i) \cr
    \displaystyle b &= \frac{
        (\displaystyle \frac{1}{n}\sum^{n}_{i=1} w_i\sum^{n}_{i=1} y_i - \sum^{n}_{i=1} w_iy_i) + a(\sum^{n}_{i=1} w_ix_i - \frac{1}{n} \sum^{n}_{i=1} w_i\sum^{n}_{i=1} x_i)
    }
    {
        \displaystyle \frac{1}{n} (\sum^{n}_{i=1} w_i)^2 - 1\sum^{n}_{i=1} {w_i}^2
    }
\end{aligned}$<br>

### 重回帰分析における統計学的指標との関係

WIP<br>
なんやかんやあって<br>

## カーネル回帰分析