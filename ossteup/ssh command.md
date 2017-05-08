java：如何生成以及导入x.509证书
发表回复
java：如何生成以及导入x.509证书

在OpenSSL官网上下栽了OpenSSL工具，在配置环境变量后，执行以下命令：
//创建根证书，并采用自签名签署它
//创建私钥
openssl genrsa -out root/root-key.pem 1024
//创建证书请求
openssl req -new -out root/root-req.csr -key root/root-key.pem
//自签署根证书
openssl x509 -req -in root/root-req.csr -out root/root-cert.pem -signkey root/root-key.pem -days 3650
//将根证书导出成浏览器支持的.p12（PKCS12）格式
openssl pkcs12 -export -clcerts -in root/root-cert.pem -inkey root/root-key.pem -out root/root.p12
//创建服务器证书，并采用根证书签署它
//创建私钥
openssl genrsa -out server/server-key.pem 1024
//创建证书请求
openssl req -new -out server/server-req.csr -key server/server-key.pem
//签署服务器证书
openssl x509 -req -in server/server-req.csr -out server/server-cert.pem -signkey server/server-key.pem -CA root/root-cert.pem -CAkey root/root-key.pem -CAcreateserial -days 3650
//将客户证书导出成浏览器支持的.p12（PKCS12）格式
openssl pkcs12 -export -clcerts -in server/server-cert.pem -inkey server/server-key.pem -out server/server.p12
//创建客户证书，并采用根证书签署它
//创建私钥
openssl genrsa -out client/client-key.pem 1024
//创建证书请求
openssl req -new -out client/client-req.csr -key client/client-key.pem
//签署客户证书
openssl x509 -req -in client/client-req.csr -out client/client-cert.pem -signkey client/client-key.pem -CA root/root-cert.pem -CAkey root/root-key.pem -CAcreateserial -days 3650
//将客户证书导出成浏览器支持的.p12（PKCS12）格式
openssl pkcs12 -export -clcerts -in client/client-cert.pem -inkey client/client-key.pem -out client/client.p12
//将根证书导入到trustStore中
keytool -import -v -trustcacerts -storepass password -alias root -file root-cert.pem -keystore root.jksv
