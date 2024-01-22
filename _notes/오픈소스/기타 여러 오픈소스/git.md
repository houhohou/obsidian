
- [b] github에 등록 
```bash
# git 접속
git init

# 이름과 이메일 주소 등록
git config --global user.email "dochuk9@naver.com"
git config --global user.name "houhohou"

# 변경된 사항 준비
git add . 
# 메시지와 함꼐 준비된 원격 사항 커밋
git commit -m "<comment>"

# 원격 저장소에 푸쉬
git push -u origin main
# 저장소의 원격 ssh 로 변경
git remote set-url origin git@github.com:houhohou/test_file.git
```


