## 公钥密码简练复习

### 1) 单向函数 & 单向陷门函数：定义+例子

* **单向函数 (one-way function)**：正向 $y=f(x) $  易算；给 $y$求 $x=f^{-1}(y)$ 在计算上困难。
  **例**：模幂 $f(x)=g^x \bmod p$（正向快，逆向是离散对数难）。
* **单向陷门函数 (trapdoor one-way)**：没有陷门难求逆；有陷门信息可高效求逆。
  **例**：RSA：$c=m^e \bmod N$易算；无$(p,q)$ 难求 $d$ 解密；知道 $\varphi(N)$（由 $p,q$得）可算$d$。

---

### 2) 群 / 环 / 域：定义+例子

* **群$(G,\star)$**：封闭、结合律、单位元、逆元。
  **例**：$(\mathbb Z_n,+)$，$(\mathbb F_p^*,\times)$。
* **环 $(R,+,\times)$**：$(R,+)$ 是阿贝尔群；乘法封闭且结合；分配律成立。
  **例**：$(\mathbb Z,+,\times)$，$(\mathbb Z_n,+,\times)$。
* **域 $(F,+,\times)$**：交换环 + (1) + 无零因子；且每个非零元素都有乘法逆元。
  **例**：$\mathbb F_p=\mathbb Z/p\mathbb Z$（$p$素数），$\mathbb R$。

---

### 3) 公钥加密 (Public Key Encryption) 定义

* 给定密钥空间K、明文空间M和密文空间C

* 密钥空间中的元素k是一对密钥，即私钥sk和公钥pk
  $$
  𝑘 = (𝑠𝑘, 𝑝𝑘)
  $$

* 加密函数$𝐸_{𝑝𝑘}: M → C$

* 解密函数$D_{s𝑘}: C → M$
  $$
  𝐷_{𝑠𝑘}(𝐸_{𝑝𝑘}(𝑚)) = 𝑚 \quad 𝑚 ∈ M
  $$

---

### 4) 欧几里得算法 & 扩展欧几里得算法

* **欧几里得算法**：$\gcd(a,b)=\gcd(b,a\bmod b)$，迭代直到余数为 0。
* **扩展欧几里得**：除求 $d=\gcd(a,b)$，还求 $x,y$使
  $$
  ax+by=d
  $$
* 若 $\gcd(a,n)=1$，则$x\equiv a^{-1}\pmod n$（用来求模逆）。

---

### 5) Diffie–Hellman & ElGamal 加密

* **DH 密钥交换**（群里都成立）：公开 $p,g$。
  Alice: $A=g^a$，Bob: $B=g^b$。共享密钥：
  $$
  K=B^a=A^b=g^{ab}\pmod p
  $$
* **ElGamal 加密**（基于 DLP）：公钥  $y=g^d $，私钥 $d $。

  加密：选随机 $r $，
  $$
  c_1=g^r,\quad c_2=m\cdot y^r \pmod p
  $$

* 

​	解密：
$$
m=c_2\cdot (c_1^d)^{-1}\pmod p
$$

### 6) 椭圆曲线定义 & 椭圆曲线算术

* **定义**：在域上
  $$
  E: y^2=x^3+Ax+B,\quad 4A^3+27B^2\ne 0
  $$
  加上无穷远点 (O) 形成阿贝尔群。(垂线P与—P关于x轴对称，与曲线无第三交点，则称为O)
  
* **点加 $P+Q$**：过 $P,Q$作直线，与曲线第三交点取关于$x$轴对称即为和；若$Q=-P$则 $P+Q=O$。

![image-20251223153619504](C:\Users\ZGYXW\AppData\Roaming\Typora\typora-user-images\image-20251223153619504.png)

* **倍点 $2P$**：取$P$处切线同理。
* **标量乘 $[k]P$**：重复加法（用 double-and-add，类似快速幂）。

![image-20251223153322912](C:\Users\ZGYXW\AppData\Roaming\Typora\typora-user-images\image-20251223153322912.png)

---

### 7) 公钥密码中的安全“硬问题”

* **离散对数问题 (DLP/ECDLP)**：给 $g,h$，求 $x$使$g^x=h$（或椭圆曲线上的 $[x]G=Q)$。→ DH/ElGamal/ECDH/ECDSA 的基础。
* **整数分解问题 (IFP)**：给 $N=pq$分解出 $p,q$很难。→ RSA 的基础。
  -（了解）其他：二次剩余（GM）、格/编码等后量子方向。