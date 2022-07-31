# 实验名称
使用RFC6979实现sm2算法

# 实验内容
由于k值泄露，两个人用了同样的k，或者两个同样的算法用了同样的k都可能导致私钥的泄露，所以RFC6979规定对与k这样的随机数的选取要变的不随机

在本次实验中我规定令k等于hash（d||ID||"算法类型"）其中d为私钥，ID为ID，"算法类型"为"sm3"

#部分代码
```python
#将K的生成和私钥，ID，所使用的算法相关联
def sign_with_RFC6979(msg,ID):
    def RFC6979(num):
        return int(sm3.sm3_hash(list(num)),16)

    M=msg.encode()
    dA, PA= get_key()
    ENTL=get_bitsize(ID)*8
    data = ENTL.to_bytes(2,byteorder='big', signed=False)+int_to_bytes(ID)+int_to_bytes(a)+int_to_bytes(b)+int_to_bytes(Gx)+int_to_bytes(Gy)+int_to_bytes(PA[0])+int_to_bytes(PA[1])
    ZA = int(sm3.sm3_hash(list(data)),16)
    M_=int_to_bytes(ZA)+M
    e=int(sm3.sm3_hash(list(M_)),16)
    r=0
    s=0
    num=int_to_bytes(dA)+int_to_bytes(ID)+"sm3".encode()
    k=RFC6979(num)%n
    while 1:
        x1,y1=calculate_np(Gx, Gy, k, a, b, p)
        r=(e+x1)%n
        s=(get_inverse(1+dA,n)*(k-r*dA))%n
        if s!=0 and r!=0 and r+k != n :
            k=(k+1)%n
            break
    return r,s
```

# 实验结果
本次实验中我选取的msg为"message digest"
ID为cBALICE123@YAHOO.COM 
![Screenshot 2022-07-31 130522](https://user-images.githubusercontent.com/104854836/182011008-fb2d632a-da76-40e2-9db5-0e9bed7b1a2f.jpg)
