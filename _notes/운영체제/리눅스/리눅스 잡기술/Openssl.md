#리눅스 


#### CA Certificates 생성
```bash
# Root CA의 비밀키 생성
openssl genrsa -out ca.key 4096

# Root CA의 비밀키와 짝을 이룰 공개키 생성
# * CN은 도메인이나 아이피 입력
openssl req -x509 -new -nodes -sha512 -days 3650 \
 -subj "/C=CN/ST=seoul/L=seoul/O=kyh0703/OU=tester/CN=100.100.103.167" \
 -key ca.key \
 -out ca.crt
```
#### Server Certificates 생성
```bash
# Server의 비밀키 생성
openssl genrsa -out yourdomain.com.key 4096

# Server의 CSR 파일 생성
# * CN은 도메인이나 아이피 입력
openssl req -sha512 -new \
    -subj "/C=CN/ST=seoul/L=seoul/O=kyh0703/OU=tester/CN=100.100.103.167" \
    -key 100.100.103.167.key \
    -out 100.100.103.167.csr
```