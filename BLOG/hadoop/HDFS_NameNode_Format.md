HDFS를 포맷하는 방법에 대해서 알아보겠습니다. 

> HDFS를 포맷하면 데이터가 모두 사라집니다. 반드시 백업을 해두고 진행하는 것이 좋습니다. 

# 작업 순서 
이 작업 순서는 AWS EMR의 노드 기준입니다. 각 제조사의 하둡마다 순서가 바뀔수 있습니다. 

1. 서버 종료
	- 네임노드, 데이타노드 종료를 종료합니다. 
	- 모든 노드의 네임노드 프로세스와 데이타노드 프로세스를 종료합니다. 
2. 네임노드 포맷
	- 포맷
	- `hdfs namenode -format` 명령으로 네임노드를 포맷합니다. 네임노드를 포맷하면 `dfs.namenode.name.dir`경로의 fsimage와 edits 파일이 초기화 됩니다. 
	- 네임노드를 포맷하면 클러스터 ID가 신규로 생성됩니다. 이 정보는 `dfs.namednoe.name.dir` 아래 VERSION 파일에 있습니다. 
3. 설정변경 
	- `dfs.namednoe.name.dir` 경로 권한을 hdfs 프로세스가 사용가능한 권한으로 변경합니다. 
	- 모든 데이터 노드의 `dfs.datanode.data.dir` 경로에서 VERSION 파일의  clusterID를 신규로 생성된 클러스터 ID로 변경합니다. 
4. 서버 시작 
	- 데이타 노드 재시작
	- 네임노드 시작 
	- 모든 노드를 시작하고 `hdfs dfsadmin -report` 명령으로 Live Node에 모든 데이터 노드가 연결되는지 확인합니다. 노드가 연결되지 않으면 네임노드의 로그에서 오류를 확인하고 이를 보고 수정합니다. 


## VERISON 파일 
네임노드와 데이터노드의 정보를 가지고 있는 VERSION파일은 다음과 같은 정보를 담고 있습니다. 

#### 네임노드 정보 
클러스터 ID와 스토리지 정보 블록풀 정보를 가지고 있습니다. 
```bash
# namenode VERSION 정보 
clusterID=CID-XX
cTime=0
storageType=NAME_NODE
blockpoolID=BP-XX
layoutVersion=63
```

#### 데이터노드 정보 
스토리지 ID와 클러스터 ID 정보를 가지고 있습니다. 
```bash
# datanode VERSION 정보 
storageID=DS-XXX
clusterID=CID-XX
cTime=0
datanodeUuid=XXXX-XX
storageType=DATA_NODE
layoutVersion=56
```
