#kubernetes 
[[kubernetes]]에서 [[Pod]]들의 집합을 Endpoint로 유도하는 역할

👉 [[kubernetes controller manager]]에 의해 관리된다

👉 [[SVC]]에 의해 유효한 [[Pod]]들을 Endpoint 목록에 추가하고 [[Pod]]가 삭제되면 endpoint 목록에서 삭제

Endpoint들 확인하는 명령어
```bash
k get endpoints
NAME         ENDPOINTS                                                                 AGE
kubelet      192.168.34.47:10250,192.168.34.49:10250,192.168.34.46:10250 + 6 more...   13d
kubernetes   192.168.34.46:6443                                                        14d
```


실제endpoint가 작동하는 과정
![](https://i.imgur.com/uq6fOEt.png)
