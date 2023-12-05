[[배포 자동화 책 공부]]

Dockerfile

ROM ubuntu:20.04
RUN apt-get update && \
    apt-get install -y python
COPY hello.py .
ENTRYPOINT ["python", "hello.py"]

hello.py

print "Hello World from Python!"

#### 빌드
docker build -t hello_world_python .

#### 실행
docker run hello_world_python

