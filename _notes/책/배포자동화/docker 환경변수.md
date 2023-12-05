
[[배포 자동화 책 공부]]

hello.py

import os
print "Hello World from %s !" % os.environ['NAME']



Dockerfile

ROM ubuntu:20.04
RUN apt-get update && \
    apt-get install -y python
COPY hello.py .
ENTRYPOINT ["python", "hello.py


#### 이미지 빌드
docker build -t hello_world_python_name .

#### 변수와 함께 컨테이너 실행
docker run -e NAME=Rafal hello_world_python_name