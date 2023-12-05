
[[윈도우]]에서 usb잡아주는 명령어



```cmd
diskpart
```


```cmd
#디스크 목록 확인
list disk

#USB가 몇번인지 보고 지정
sel disk 3

# 디스크 초기화 ( 파티션삭제 )
clean

#주 파티션생성
create partion primary

#NTFS로 빠른 포맷 하기
format quick fs=ntfs

#활성화 하기
active

#종료
exit
```


[[윈도우]] 제어된 폴더 액세스 때문에
장치에 액세스 할수없다고 뜨는 오류있음


참고 링크
https://gariob.tistory.com/19