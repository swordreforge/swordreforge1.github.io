[toc] Content



# 我的第一个markdown /#

## Test//二级标题## 

> JUST A NEW START //引用文字>

### 一.编码分析

> * jsfuck 编码（常用特征[]!!!!）
> * brainfuck编码() // *无序列表
> * url,base,rot,栅栏，cansar,猪圈 and so on...

### 二.编码嵌套

1.base纯嵌套

2.rot-base

### 三.掌握情况

[ ] this is task

[x] 完成

### 四.代码分析

``` python
from Crypto.Util.number import *
from gmpy2 import *

# 已知值
m = bytes_to_long(flag)
p, q, r = [getPrime(512) for _ in range(3)]
e = 65537

n = p * q * r

c = pow(m, e, n)

print(f"hint1 = {1144*p+5143*q+1281*r}")
print(f"hint2 = {1869*p+1356*q+1495*r}")
print(f"hint3 = {1620*p+1762*q+1957*r}")
print(f"c = {c}")

# 提示中的值
hint1 = 79333650588725980145842690308459793002212384733760792497903824255475158426421388758884515854200584020175891983698755801887895178728215285671100862522546388920
hint2 = 42091939085030707750026943885448586020057668249489766328512130903699537123923304865199162545942232848524822773449884502246598542894968112652618125488987069022
hint3 = 47921639502651352998409354170011465949752789835571950468703988521419628212309010862554662374631739734695758683737532319227640559546280393992908749025031664459

# 从提示中恢复 p, q, r
p = (hint1 - 1620 * r) // 1144
q = (hint2 - 1869 * r) // 1356
r = hint3 // 1620

# 验证恢复的值是否正确
assert p * q * r == n

# 计算 phi(n)
phi_n = (p - 1) * (q - 1) * (r - 1)

# 计算私钥 d
d = invert(e, phi_n)

# 解密 c
m_decrypted = pow(c, d, n)

# 将解密后的整数转换回字节串
flag_decrypted = long_to_bytes(m_decrypted)
print("Decrypted flag:", flag_decrypted.decode())
```

> usage: use ''' + python or other to create a programming language 

### 五.Markdown 怎么不可以有LaTex数学公式呢

$$
\math bf{V}_1 \times \mathbf{V}_2 =  \begin{vmatrix}  \mathbf{i} & \mathbf{j} & \mathbf{k} \\ \frac{\partial X}{\partial u} &  \frac{\partial Y}{\partial u} & 0 \\ \frac{\partial X}{\partial v} &  \frac{\partial Y}{\partial v} & 0 \\ \end{vmatrix}
$$

$$
\math bf{V}_1 \times \mathbf{V}_2 =  \begin{vmatrix}  \mathbf{i} & \mathbf{j} & \mathbf{k} \\ \frac{\partial X}{\partial u} &  \frac{\partial Y}{\partial u} & 0 \\ \frac{\partial X}{\partial v} &  \frac{\partial Y}{\partial v} & 0 \\ \end{vmatrix}
$$

> 使用‘$$ $$’包围构造数学公式哟

### 六.表格?表格!

| 常用绕过    | 鲜明特征                                     |
| ----------- | -------------------------------------------- |
| %00空格绕过 | if(prematch('/flag,cat,more,less,tac,/'))    |
| 双写绕过    | foreach('flag' in $cmd); replace('flag',''); |
| ${IFS}绕过  | 114514                                       |
| WAF 绕过    | 19190                                        |

> usage: '| |' to create excel.

### 七.脚注啊

[^114514]: I'm looking for a emphasize of SSTL.

### 八.Span long,long span

This is [大学生求生指南](http://chaoxing.com/ "Title") inline link.

[ThinkPhp](http://bugku.net/) has no title attribute.

### 九.下划线

***

114514 please use '***' to create a new line.

***

### 十.强调

> 1.四十五度致敬

*php 是最好的语言* （** or _ _）输出\*

_塞尔达是最早的真开放世界_

> 2.粗标点重

**硅谷之火**（use '\*\*\*\*'）

> 3.不该出现的东东

~~《人间失格》~~ ~~厚黑学~~

> 4.真是表表又情情啊

> :satisfied::sweat_smile:

> 5.请解释<\\u><\\u>的句子的含义

### 十一.兄弟，你有标吗

> 1.下标
>
> `H~2~O`, `X~long\ text~`/
>
> 2.上标
>
>  `X^2^`

```Markdown
<iframe height='265' scrolling='no' title='Fancy Animated SVG Menu' src='http://codepen.io/jeangontijo/embed/OxVywj/?height=265&theme-id=0&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'></iframe>
```

### 十二.好康的

> 1.video

```Markdown
<video src="1.mp4" />
```



