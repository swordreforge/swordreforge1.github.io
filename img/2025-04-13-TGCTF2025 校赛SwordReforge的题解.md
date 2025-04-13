# TGCTF2025 校赛SwordReforge的题解

> conclusion:I'am fw,with learning one month.

#### 一.misc 部分

###### 1.AAAAAA真签到

队友gpt梭的

###### 2.**next is the end**

> zip Bomb

binwalk->7-zip->路径粘贴->直接找到路径

也有脚本

```python
import os
import zipfile
import rarfile
import py7zr
import re
from threading import Timer

# 配置
MAX_DEPTH = 1000  # 防止无限递归
CURRENT_DEPTH = 0
FLAG_PATTERN = r'flag\{.*?\}'
PASSWORDS = ['', 'password', '123456', 'flag']

def extract_archive(file_path, extract_dir, password=None):
    """解压支持的压缩格式"""
    try:
        if file_path.endswith('.zip'):
            with zipfile.ZipFile(file_path, 'r') as z:
                if password:
                    z.extractall(extract_dir, pwd=password.encode())
                else:
                    z.extractall(extract_dir)
            return True
        elif file_path.endswith('.rar'):
            with rarfile.RarFile(file_path, 'r') as r:
                if password:
                    r.extractall(extract_dir, pwd=password)
                else:
                    r.extractall(extract_dir)
            return True
        elif file_path.endswith('.7z'):
            with py7zr.SevenZipFile(file_path, 'r', password=password) as a:
                a.extractall(extract_dir)
            return True
        else:
            print(f"Unsupported format: {file_path}")
            return False
    except Exception as e:
        print(f"Extraction failed: {str(e)}")
        return False

def search_flag(directory):
    """在目录中搜索flag"""
    for root, _, files in os.walk(directory):
        for file in files:
            path = os.path.join(root, file)
            try:
                with open(path, 'rb') as f:
                    content = f.read().decode('utf-8', errors='ignore')
                    if re.search(FLAG_PATTERN, content):
                        match = re.search(FLAG_PATTERN, content)
                        print(f"[+] Flag found: {match.group()} in {path}")
                        os._exit(0)  # 立即退出
            except:
                continue
    return False

def recursive_extract(start_dir, depth=0):
    global CURRENT_DEPTH
    if depth >= MAX_DEPTH:
        print("[-] Max depth reached. Aborting.")
        return

    # 搜索当前目录
    if search_flag(start_dir):
        return

    # 查找压缩文件
    archives = []
    for f in os.listdir(start_dir):
        full_path = os.path.join(start_dir, f)
        if os.path.isfile(full_path) and any(f.endswith(ext) for ext in ['.zip', '.rar', '.7z']):
            archives.append(full_path)

    # 按修改时间排序，优先处理最新文件（可能更接近flag）
    archives.sort(key=lambda x: os.path.getmtime(x), reverse=True)

    for archive in archives:
        print(f"[*] Processing {archive} at depth {depth}")
        extract_dir = os.path.join(start_dir, f"extracted_{depth}")
        os.makedirs(extract_dir, exist_ok=True)

        # 尝试空密码解压
        success = extract_archive(archive, extract_dir)
        if not success:
            # 尝试密码破解
            for pwd in PASSWORDS:
                print(f"[*] Trying password: {pwd}")
                success = extract_archive(archive, extract_dir, pwd)
                if success:
                    break

        if success:
            recursive_extract(extract_dir, depth + 1)
        else:
            print(f"[-] Failed to extract {archive}")

if __name__ == "__main__":
    # 设置超时（例如30分钟）
    def timeout_handler():
        print("[-] Timeout reached. Exiting.")
        os._exit(1)
    
    Timer(1800, timeout_handler).start()  # 1800秒=30分钟
    recursive_extract(os.getcwd())

```





###### 3.where it is

> 社工

谷歌识图

#### 二.Crypto部分

###### 4.费克特尔

```
c=670610235999012099846283721569059674725712804950807955010725968103642359765806
n=810544624661213367964996895060815354972889892659483948276203088055391907479553
e=65537
```

可用yafu分解 n,脚本

```python
from Crypto.Util.number import inverse, long_to_bytes

# 分解后的n的素数因子
p = [
    113,
    18251,
    2001511,
    214168842768662180574654641,
    916848439436544911290378588839845528581
]

# 计算n
n = 1
for prime in p:
    n *= prime

# 分解后的c的因子
c_factors = [
    2,
    3,
    39317,
    200443,
    61399747823107048319,
    230983561625122789653422292020602288585417281109
]

# 计算c
c = 1
for factor in c_factors:
    c *= factor

# 确保c < n，否则无法正确解密
assert c < n, "c must be less than n"

# 计算欧拉函数φ(n)
phi = 1
for prime in p:
    phi *= (prime - 1)

# 计算私钥d
e = 65537
d = inverse(e, phi)

# 解密得到明文m
m = pow(c, d, n)

# 转换为字节并输出
flag = long_to_bytes(m)
print("Flag:", flag.decode())

```

![image-20250413212727098](C:\Users\zhuji\AppData\Roaming\Typora\typora-user-images\image-20250413212727098.png)

###### 三.web部分

###### 1.AAA偷渡阴平（赛后解）

bp抓包，可用与复仇题比较法（雾）执行print_r(pos(getallhearders()));改请求头为危险命令，拿到flag

###### 2.**火眼辩魑魅**

robots.txt->访问php文件->shell->蚁剑直接连接->操作所有文件

(从另一个题解了解可使用取反脚本shell=~()对phpinfo()urlencode访问)

拓展阅读：蚁剑的原理AV1150982761

###### 3.直面天命（赛后解）

> 有提示的，没用bp抓包的有福了

开局一个框，以为是注入，fuzz测试均正常，怀疑为干扰项，直接扫路由，得到aazz,有请求头，filename=传文件即可

#### 四.疑问部分

#### 一.web

###### 4.（ez）upload

##### 一.成功部分

1.大写Php,php7,phps,PHP,%00均可绕过

2.访问 index.php.bak,uploads.php.bak,成功将黑盒审计转为白盒

3.可以成功访问上传文件

4.获取到nignx版本号

###### 二.失败部分

1.nignx 无法访问uploads路径，访问失败

2.访问php7,phps,Php，均出现文件下载现象，同时均无法用蚁剑链接

3.回忆nssctf上NISA一题，将filename="/flag"进行上传，失败

##### 5.前端小游戏

> 20分即可hint文件在/tgflagggg上，但不知道如何访问文件

###### 二. misc

> 伪码

![image-20250413224321393](C:\Users\zhuji\AppData\Roaming\Typora\typora-user-images\image-20250413224321393.png)

由于时间有限后续完善