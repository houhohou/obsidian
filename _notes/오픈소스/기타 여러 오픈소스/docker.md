
- [b] 이미지 관련 
```bash
# docker hub에서 이미지 찾기 
docker search nginx

# 조건 넣기 : 별 100개 이상만 
docker search -f stars=100 nginx

# 공식적인 이미지만 찾기
docker search -f is-official=true nginx

# 10개만 출력하고 뒤쪽 설명이 풀로 나옴
docker search --limit 10 --no-trunc nginx

# 이미지를 다운받는 명령어
docker pull nginx

# 현재 갖고있는 이미지 확인
docker images
```
- [b] 컨테이너 실행관련
```bash
# 현재 
dokcer ps -a

# 실행
docker run
# -d : 백그라운드로 실행
# -it : 포그라운드로 실행

```