#리눅스
[[오픈스택]]할때, 가상브릿지 생성해서 작업했음
##### 브릿지 생성
ovs-vsctl add-br <생성할 브릿지 이름>

ovs-vsctl add-port <브릿지 이름> 

##### 브릿지 목록
ovs-vsctl show: ovs bridge



##### br-ex라는 브릿지 삭제
ovs-vsctl del-br br-ex 

##### ens35라는 브릿지 전체 삭제
ovs-vsctl del-br ens35 

##### br-provider라는 브릿지를 ens35와 연결
ovs-vsctl add-port br-provideer ens35

















