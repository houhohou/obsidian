[[repo]] 에서 rpm들을 다 당겨오는 방법


yum install yum-utils
```bash
#옵션값 없이 바라보고있는 repo 전체에 대한 패키지를 현재 위치에 다운 
reposync  --download-metadata

#옵션값넣을때
reposync --repoid=<다운로드 진행할 Repo ID> --download-metadata --download_path=<다운로드할 디렉토리 경로>
```
![](https://i.imgur.com/Uyz0Yyd.png)


