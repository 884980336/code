## RSA加密

```python
# 生成公钥私钥对象
import rsa
pub_key_obj, priv_key_obj = rsa.newkeys(1024)
'''
这里的1024是二进制位数, 也就是说他加密的内容只有1024/8 = 128个字节, 但是里面又有着11个字节是它必须有的, 所以最长只能加密117个字节
'''
# 获取公钥私钥字符串
pub_key_str = pub_key_obj.save_pkcs1()
priv_key_str = priv_key_obj.save_pkcs1()

# 加密
pk = rsa.PublicKey.load_pkcs1(pub_key_str) # 加载公钥
val = b"haha"
m_val = rsa.encrypt(val,pk) # 这就是加密的结果, 长度就是128(与上面有关)

# 解密
pr = rsa.PrivateKey.load_pkcs1(priv_key_str) # 加载私钥
r_val = rsa.decrypt(m_val, pr)
```

上面说1024只能加密127个字节, 我们来试一下

```python
pk = rsa.PublicKey.load_pkcs1(pub_key_str) # 加载公钥
val = b"haha" * 30 # 120长度
m_val = rsa.encrypt(val,pk)
'''
OverflowError: 120 bytes needed for message, but there is only space for 117
'''
```

那如何来加密长字符串呢, 通过循环, 我们可以每次加密117个字节, 最后把得到的内容拼接起来, 解密时相同, 每次解密128字节

```python
# 加密
pk = rsa.PublicKey.load_pkcs1(key_str)

bytes_value = b"haha" * 30
length = len(bytes_value)

val_list = []
for i in range(0, length, 117):
    tpl = bytes_value[i:i + 117]
    val = rsa.encrypt(tpl, pk)
    val_list.append(val)

ret = b''.join(val_list)

# 解密
pk = rsa.PrivateKey.load_pkcs1(key_str)

length = len(ret)
val_list = []
for i in range(0, length, 128):
    tpl = ret[i:i + 128]
    val = rsa.decrypt(tpl, pk)
    val_list.append(val)

ret = b''.join(val_list)
```

## base64加密

```python
import base64
crypt = base64.standard_b64encode(b"haha") # 加密
print(crypt)
decrypt = base64.standard_b64decode(crypt) # 解密
print(decrypt)
```

