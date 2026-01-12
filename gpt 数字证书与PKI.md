# 数字证书与PKI 



1. 下面按你列的 4 点**“简要但覆盖考点”**回答（适合期末背诵/作答）。

   ---

   ## 1) 数字签名：概念 + 典型算法

   **概念**
   数字签名是一种密码机制，用私钥对消息（通常是消息的哈希）生成签名；任何人用公钥可验证签名，从而提供：

   * **完整性**：消息没被改
   * **身份认证**：签名者持有对应私钥
   * **不可否认性**：签名者事后难以否认（在密钥未泄露前提下）

   **典型算法**

   * **RSA 签名**：对哈希做填充（如 PKCS#1 v1.5 / PSS）再用私钥指数运算；验证用公钥指数运算。
   * **DSA / ECDSA**：基于离散对数/椭圆曲线离散对数，签名含随机数 *k*（*k* 泄露或重用会直接暴露私钥）。
   * **Schnorr / EdDSA(Ed25519)**：Schnorr 系列，结构更简洁；EdDSA 常用确定性 nonce。
   * **BLS**（基于双线性对）：签名短、易聚合（后面第 4 点会提）。

   ---

   ## 2) 数字证书与 PKI：概念 + 证书链 + DV/OV/EV + 浏览器观察要点

   **数字证书（X.509）是什么**
   把**主体身份信息**与**公钥**绑定，并由 **CA** 用其私钥签名：

   > cert = { Subject(身份), Subject Public Key(公钥), Validity(有效期), Issuer(CA), Extensions… , CA签名 }

   **CA / 根证书 / 证书链**

   * **CA**：签发并签名证书的机构/系统。
   * **根证书（Root CA）**：通常自签名，预置在浏览器/操作系统的信任库里（“信任锚”）。
   * **中间 CA（Intermediate CA）**：由根 CA 签发，用来进一步签发站点证书（降低根私钥暴露风险）。
   * **证书链（Certificate Chain）**：站点证书 → 中间 CA → 根 CA。浏览器验证链路签名、有效期、用途等，最终“链到”信任库里的根证书。

   **PKI（公钥基础设施）**
   围绕证书的全套机制：证书格式与标准、CA/RA 流程、签发/更新/吊销（CRL/OCSP）、信任库管理等。

   **证书里常见“域/字段”（浏览器里重点看）**

   * **Subject / SAN（Subject Alternative Name）**：域名一般在 SAN 里（如 `www.example.com`）。
   * **Issuer**：签发者是谁（中间 CA 名称）。
   * **Validity**：Not Before / Not After。
   * **Public Key & Signature Algorithm**：公钥类型与签名算法（RSA/ECDSA 等）。
   * **Key Usage / Extended Key Usage**：能否用于服务器认证、签名等。
   * **Basic Constraints**：是否为 CA、path length 等。
   * **CRL Distribution Points / AIA / OCSP**：吊销与链构建信息。

   **DV / OV / EV 区别（按“验证强度”记）**

   * **DV（Domain Validation）域名验证**：只证明“你控制该域名”（DNS/HTTP 文件/邮箱等）。
   * **OV（Organization Validation）组织验证**：在 DV 基础上，还核验组织真实存在、关联关系等，证书信息更“实名”。
   * **EV（Extended Validation）扩展验证**：最严格的组织核验流程（历史上浏览器曾用更醒目的 UI 展示，近年弱化了 UI 差异）；本质仍是更强的身份审核。

   ---

   ## 3) 基于身份密码（IBC）：定义 + 典型构造/应用 + 与传统 PKI 区别

   **定义（核心一句话）**
   在 IBC 中，**公钥就是身份字符串**（email/手机号/工号等），用户私钥由 **PKG（Private Key Generator）** 用系统主密钥生成并安全下发。

   **典型构造（讲义中的代表）**

   * **IBE（Identity-Based Encryption）**：Boneh–Franklin IBE（基于 pairing）是经典实用方案；Cocks IBE（基于二次剩余）也是早期实用方案。
   * **IBS（Identity-Based Signature）**：早期 Shamir IBS（RSA 风格），以及后续多种 pairing/非 pairing 构造。
   * **身份基密钥协商**：如 SOK 非交互密钥共享。

   **典型应用**

   * 企业/校园内部通信（省去证书分发）
   * 设备/物联网（用序列号做身份）
   * 广播/组播场景（用身份空间做密钥管理）
   * 带“时间戳身份”的短期密钥（`ID || time`）做自然的“过期/轮转”

   **与传统公钥密码/PKI 的区别（抓住 3 个对比）**

   1. **公钥获取方式**

      * PKI：公钥来自证书，需要验证证书链
      * IBC：公钥=身份字符串（无需证书链）
   2. **信任与风险点**

      * PKI：信任分散在多 CA/信任库；私钥在用户手里
      * IBC：**PKG 持主密钥**，可生成任何人私钥 ⇒ **Key Escrow（密钥托管/后门风险）**
   3. **吊销/更新机制**

      * PKI：CRL/OCSP/换证书
      * IBC：常用 `ID || time` 让私钥自然按周期失效，达到“准吊销/轮转”

   ---

   ## 4) 双线性对（Bilinear Pairing）与基于 Pairing 的方案

   **双线性对定义**
   映射：( e: $G_1 \times G_2 \rightarrow G_T$)（群阶通常为素数 $p $）

   **三大性质（必背）**

   1. **双线性**： $ e(aP, bQ)=e(P,Q)^{ab} $
   2. **非退化**：不是对所有输入都映射到 1（存在  $P,Q $ 使  $(e(P,Q)\neq 1) $
   3. **可计算**：存在有效算法计算（Weil/Tate/Ate/R-ate 等）

   **基于 Pairing 的典型方案（记 3 个代表）**

   * **Boneh–Franklin IBE（BF-IBE）**：用 pairing 把“身份→公钥点”，再通过 $e(Q_{ID}, P_{PKG})^r $导出会话密钥来加密消息。
   * **SOK 身份基非交互密钥共享**：双方用各自身份点与 PKG 下发私钥，通过 pairing 得到相同共享密钥。
   * **BLS 签名**：短签名；验证式形如
      $ e(\sigma, G)=e(H(m), P)  $；并支持**签名聚合**。

   （补一句理解：pairing 既能用于构造，也提示了像 MOV 这类把 ECDLP“搬运”到有限域去解的攻击思路。）
