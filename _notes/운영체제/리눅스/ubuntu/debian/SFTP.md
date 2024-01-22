
[[리눅스]]에서 ssh의 ftp버전이라고 생각하면 된다


접속
```bash
sftp root@<ip>
```


다운로드
```bash
#단일 파일 다운로드
get <file_name>

#디렉토리 하위까지 다 다운로드
get -r <folder_name>
```