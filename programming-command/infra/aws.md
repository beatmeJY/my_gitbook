# AWS

## AWS EC2 키페어로 접속하는 법

### 1. 키페어 파일 권한 설정

* chmod 400 <키파일명>.pem

### 2. Public IP 또는 DNS 확인

* Public IP: `54.123.45.67`
* Public DNS: `ec2-54-123-45-67.compute-1.amazonaws.com`

### 3. SSH 연결 명령어

```
ssh -i Aws.pem ec2-user@54.123.45.67
```
