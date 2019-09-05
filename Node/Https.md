### 密钥

每个服务器端和客户端都有自己的公私钥。公钥用来加密要传输的数据，私钥用来解密接收到的数据。公钥和私钥是配对的。

在建立安全传输之前，客户端和服务器之间需要互换公钥。客户端发送数据时需要服务器端的公钥进行加密，服务器端发送数据时需要客户端的公钥进行加密。



```shell
# 生成服务器端私钥
openssl genrsa -out server.key 1024

# 生成客户端私钥
openssl genrsa -out client.key 1024


# 上述命令生成了两个1024位长的RSA私钥文件，我们可以通过它继续生成公钥

openssl rsa -in server.key -pubout -out server.pem

openssl rsa -in client.key -pubout -out client.pem


# 生成私钥，生成CSR文件，通过私钥自签名生成证书的过程：

openssl genrsa -out ca.key 1024

openssl req -new -key ca.key -out ca.csr

openssl x509 -req -in ca.csr -signkey -out ca.crt

```



