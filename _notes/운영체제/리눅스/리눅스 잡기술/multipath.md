#리눅스 

### multipath란 ? 
👉스토리지에 있는 디바이스 정보를 HBA Card로 연결하여 스토리지에 있는 2개의 LUN에 연결되어서 한 쪽 연결이 끊어져도 다른 한 쪽으로 통신이 가능하도록 구성하는 것
❗**HBA Card** :   HBA Card란 FC Cable(광케이블)을 이용 가능하게 해주는 Adapter (멀티패스는 HBA Card를 이용)
![](https://i.imgur.com/qpGdo4q.png)
👉이중화(다중화)가 되어있는 여러 개의 물리적인 라인을 하나의 LUN으로 묶어 논리적으로 하나의 디스크로 인식시켜주는 것 

###### 사용하는 이유
스토리지 - 스위치 - 서버가 연결되어 있을 때 연결하는 선이 하나라면, 이 선이 끊어질 경우 서버의 통신은 모두 끊기므로 서비스에 장애가 생긴다. 하지만 선이 두 개라면 서비스는 끊기지 않지만 서버에서 df-Th 명령어로 확인했을 때 같은 스토리지임에도 불구하고 두 개의 스토리지(LUN)가 보일 것이다. 한 개의 스토리지가 있지만 서버에는 두 개의 스토리지로 바라본다면 오류를 발생할 가능성이 크다. 그래서 두 개의 LUN을 하나의 LUN으로 만들어 주는 역할을 하는 것이 멀티패스 이다



- [b] 명령어 
```bash
# 초기화
multipath -F 
# 적용
multipath -v2
# 적용 리스트 확인
multipath -ll
# blacklist 설정 확인
multipath -v3 | grep blacklist
```

### '/etc/multipath.conf'파일 설정값 확인하기

✅ Multipath를 통해 연동할 때 보다 더 효율적으로 연동하고 관리하기 위해서는 설정 파일 확인이 필수

- [b]  아래는 default값
```bash
# This is a basic configuration file with some examples, for device mapper  
# multipath.  
#  
# For a complete list of the default configuration values, run either  
# multipath -t  
# or  
# multipathd show config  
#  
# For a list of configuration options with descriptions, see the multipath.conf  
# man page  
  
## By default, devices with vendor = "IBM" and product = "S/390.*" are  
## blacklisted. To enable mulitpathing on these devies, uncomment the  
## following lines.  
#**blacklist_exceptions** {  
# device {  
# vendor "IBM"  
# product "S/390.*"  
# }  
#}  
  
## Use user friendly names, instead of using WWIDs as names.  
defaults {  
user_friendly_names yes  
find_multipaths yes  
}  
##  
## Here is an example of how to configure some standard options.  
##  
#  
#**defaults** {  
# polling_interval  10  
# path_selector "round-robin 0"  
# path_grouping_policy multibus  
# uid_attribute ID_SERIAL  
# prio alua  
# path_checker readsector0  
# rr_min_io 100  
# max_fds 8192  
# rr_weight priorities  
# failback immediate  
# no_path_retry fail  
# user_friendly_names yes  
#}  
##  
## The wwid line in the following blacklist section is shown as an example  
## of how to blacklist devices by wwid.  The 2 devnode lines are the  
## compiled in default blacklist. If you want to blacklist entire types  
## of devices, such as all scsi devices, you should use a devnode line.  
## However, if you want to blacklist specific devices, you should use  
## a wwid line.  Since there is no guarantee that a specific device will  
## not change names on reboot (from /dev/sda to /dev/sdb for example)  
## devnode lines are not recommended for blacklisting specific devices.  
##  
#**blacklist** {  
#       wwid 26353900f02796769  
# devnode "^(ram|raw|loop|fd|md|dm-|sr|scd|st)[0-9]*"  
# devnode "^hd[a-z]"  
#}  
#**multipaths** {  
# multipath {  
# wwid 3600508b4000156d700012000000b0000  
# alias yellow  
# path_grouping_policy multibus  
# path_selector "round-robin 0"  
# failback manual  
# rr_weight priorities  
# no_path_retry 5  
# }  
# multipath {  
# wwid 1DEC_____321816758474  
# alias red  
# }  
#}  
#**devices** {  
# **device** {  
# vendor "COMPAQ  "  
# product "HSV110 (C)COMPAQ"  
# path_grouping_policy multibus  
# path_checker readsector0  
# path_selector "round-robin 0"  
# hardware_handler "0"  
# failback 15  
# rr_weight priorities  
# no_path_retry queue  
# }  
# **device** {  
# vendor "COMPAQ  "  
# product "MSA1000         "  
# path_grouping_policy multibus  
# }  
#}
```

### 옵션값 설명
| Sections | 설명 |
| ---- | ---- |
| blacklist | multipath에서 특정 장치군 / device를 제외하는 섹션(옵션)  <br>multipath를 사용하지 않을 device를 설정 |
| blacklist_expections | blackilst에 포함되지만 그 중 예외처리를 하는 옵션   <br>blacklist의 후보로도 볼 수 있으며 파라미터에 따라 blackilst 섹션에 추가 될 수 있음 |
| defaults | multipath의 일반적인 설정을 하는 섹션 |
| multipaths | 각각의 multipath 별로 디바이스 셋팅을 한 값 <br>기존의 defaults 섹션을 overwrite함 |
| devices | 개별 스토리지 컨트롤러에 대한 설정<br>default 섹션을 지원하지 않는다면 하위섹션으로 따로 지정해서 사용 함<br>즉 각 스토리지 설정을 따로 지정하는 섹션 |
|  |  |
