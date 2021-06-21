# KNU 데이터 플랫폼 helm chart

Helm과 Kubernetes를 이용해 손쉽게 데이터 수집 플랫폼을 사용할 수 있습니다.

# 요구환경 Requirements

1. Kubernetes Cluster 환경 - [https://kubernetes.io/ko/docs/setup/](https://kubernetes.io/ko/docs/setup/)
    - 이용할 플랫폼 종류에 따라 Persistent
2. Helm 구동환경 - [https://helm.sh/docs/intro/install/](https://helm.sh/docs/intro/install/)

✅ 본 프로젝트는 Google Kubernetes Engine를  기반으로 테스트 완료 하였습니다.

# 설치 Installation

## Helm repository 추가

1. Helm Repository에 KNU 데이터 플랫폼 helm chart 주소를 추가 ([https://bigdataflatformdynamicconfiguration.github.io/Custom_Helm/](https://bigdataflatformdynamicconfiguration.github.io/Custom_Helm/))

    ```bash
    helm repo add "repo name" https://bigdataflatformdynamicconfiguration.github.io/Custom_Helm/
    ```

2. Helm Repository 확인
    - Helm repo 목록 확인 명령어

    ```bash
    helm repo list
    ```

    - 실행결과

    ```bash
    NAME         URL
    "repo name"  https://bigdataflatformdynamicconfiguration.github.io/Custom_Helm/
    ```

3. Helm Repository update
    - update

    ```bash
    helm repo update
    ```

    - 실행결과

    ```bash
    Hang tight while we grab the latest from your chart repositories...
    ...Successfully got an update from the "repo name" chart repository
    Update Complete. ⎈Happy Helming!⎈
    ```

‼️collect-beta의 경우 추가적인 repository 등록 필요‼️

- elastic

```bash
helm repo add elastic https://helm.elastic.co
```

- helm repo update

```bash
helm repo update
```

## Cluster에 데이터 플랫폼 설치

### collect-alpha 플랫폼

1. 플랫폼 구성 내용
    - collect-alpha API server, Kafka, Hbase, HDFS, Zookeeper
2. Helm install

    ```bash
    helm install "release name" "repo name"/collect-alpha
    ```

3. kubectl get pods
    - kubectl get pods 실행 결과 예시

    ```bash
    NAME                                          READY   STATUS    RESTARTS   AGE
    "release name"-collect-alpha-0                1/1     Running   0          8m38s
    "release name"-collect-alpha-1                1/1     Running   0          7m46s
    "release name"-collect-alpha-2                1/1     Running   0          7m14s
    "release name"-hbase-master-0                 1/1     Running   0          8m37s
    "release name"-hbase-master-1                 1/1     Running   0          7m8s
    "release name"-hbase-regionserver-0           1/1     Running   0          8m38s
    "release name"-hbase-regionserver-1           1/1     Running   0          6m57s
    "release name"-hbase-regionserver-2           1/1     Running   0          6m
    "release name"-hdfs-datanode-0                1/1     Running   0          8m37s
    "release name"-hdfs-datanode-1                1/1     Running   0          8m1s
    "release name"-hdfs-datanode-2                1/1     Running   0          7m32s
    "release name"-hdfs-httpfs-66c664bf58-7sqsh   1/1     Running   0          8m38s
    "release name"-hdfs-namenode-0                2/2     Running   0          8m38s
    "release name"-kafka-0                        1/1     Running   0          8m38s
    "release name"-kafka-1                        1/1     Running   0          6m52s
    "release name"-kafka-2                        1/1     Running   0          5m41s
    "release name"-zookeeper-0                    1/1     Running   0          8m38s
    ```

4. kubectl get svc 
    - kubectl get svc 실행 결과 예시

    ```bash
    NAME                                         TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                 AGE
    kubernetes                                   ClusterIP      "클러스터IP"    <none>         443/TCP                                 89m
    "release name"-collect-alpha-api             LoadBalancer   "클러스터IP"   "외부 Id"       2000:32183/TCP,2001:30923/TCP           95s
    "release name"-hbase-master                  ClusterIP      None           <none>          9090/TCP,9095/TCP,16000/TCP,16010/TCP   95s
    "release name"-hbase-master-metrics          ClusterIP      "클러스터IP"    <none>         5556/TCP                                95s
    "release name"-hbase-master-thrift-metrics   ClusterIP      "클러스터IP"    <none>         5557/TCP                                95s
    "release name"-hbase-region-metrics          ClusterIP      "클러스터IP"    <none>         5556/TCP                                95s
    "release name"-hbase-regionserver            ClusterIP      None           <none>          16020/TCP,16030/TCP                     95s
    "release name"-hdfs                          ClusterIP      "클러스터IP"   <none>          8020/TCP,50070/TCP                      95s
    "release name"-hdfs-datanode                 ClusterIP      None           <none>          50075/TCP                               95s
    "release name"-hdfs-httpfs                   ClusterIP      "클러스터IP"   <none>          14000/TCP                               94s
    "release name"-hdfs-namenode                 ClusterIP      None           <none>          8020/TCP,50070/TCP                      95s
    "release name"-hdfs-namenode-exporter        ClusterIP      "클러스터IP"   <none>          5556/TCP                                94s
    "release name"-kafka                         ClusterIP      "클러스터IP"   <none>          9092/TCP                                95s
    "release name"-kafka-headless                ClusterIP      None           <none>          9092/TCP                                94s
    "release name"-zookeeper                     ClusterIP      "클러스터IP"   <none>          2181/TCP,2888/TCP,3888/TCP              95s
    "release name"-zookeeper-headless            ClusterIP      None           <none>          2181/TCP,2888/TCP,3888/TCP              94s
    ```

5. collect API server 구동
    - collect-alpha pod들의 API server 실행

    ```bash
    kubectl exec "release name"-collect-alpha-0 -- python3 /modules/app.py "release name"-kafka "release name"-hbase-master
    ```

### collect-beta 플랫폼

1. 플랫폼 구성 내용
    - elasticsearch, fluentd, kibana
2. Value file 다운로드
    - elasticvalues.yaml

    ```bash
    wget https://raw.githubusercontent.com/BigdataFlatformDynamicConfiguration/valuefiles/main/elasticvalues.yaml
    ```

    - fluentdvalue.yaml

    ```bash
    wget https://raw.githubusercontent.com/BigdataFlatformDynamicConfiguration/valuefiles/main/fluentdvalue.yaml
    ```

    - kibanavalues.yaml

    ```bash
    wget https://raw.githubusercontent.com/BigdataFlatformDynamicConfiguration/valuefiles/main/kibanavalues.yaml
    ```

3. helm install
    - elastic search 설치

    ```bash
    helm install -f elasticvalues.yaml elastic elastic/elasticsearch --version 7.10.1helm install -f fluentdvalue2.yaml fld repo_name/fluentd
    ```

    - fluentd 설치

    ```bash
    helm install -f fluentdvalue.yaml fld "repo_name"/fluentd
    ```

    - kibana 설치

    ```bash
    helm install -f kibanavalues.yaml kibana elastic/kibana --version 7.10.1
    ```

4. kubectl get pods
    - kubectl get pods 실행 결과 예시

    ```bash
    NAME                             READY   STATUS    RESTARTS   AGE
    elasticsearch-master-0           1/1     Running   0          19d
    elasticsearch-master-1           1/1     Running   0          14d
    fld-fluentd-0                    1/1     Running   0          14d
    fld-fluentd-hptpp                1/1     Running   0          14d
    fld-fluentd-m2gqj                1/1     Running   0          14d
    hdfs-datanode-0                  1/1     Running   0          14d
    hdfs-datanode-1                  1/1     Running   0          19d
    hdfs-datanode-2                  1/1     Running   0          14d
    hdfs-httpfs-7546bcd8f6-9qnzr     1/1     Running   0          16d
    hdfs-namenode-0                  2/2     Running   0          19d
    kibana-kibana-5b64cd58b5-h824s   1/1     Running   0          3d22h
    ```

5. kubectl get svc 
    - kubectl get svc 실행 결과 예시

    ```bash
    NAME                                                     TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
    elasticsearch-master                                     ClusterIP   "클러스터IP"   <none>        9200/TCP,9300/TCP    19d
    elasticsearch-master-headless                            ClusterIP   None          <none>        9200/TCP,9300/TCP    19d
    fld-fluentd-aggregator                                   ClusterIP   "클러스터IP"   <none>        9880/TCP,24224/TCP   14d
    fld-fluentd-forwarder                                    ClusterIP   "클러스터IP"   <none>        9880/TCP             14d
    fld-fluentd-headless                                     ClusterIP   None          <none>        9880/TCP,24224/TCP   14d
    glusterfs-dynamic-1cb99cc2-c271-11eb-9e40-029994d0f274   ClusterIP   "클러스터IP"   <none>        1/TCP                20d
    glusterfs-dynamic-1d5b5104-b940-11eb-9f15-029994d0f274   ClusterIP   "클러스터IP"   <none>        1/TCP                32d
    glusterfs-dynamic-2642ad4a-c271-11eb-9e40-029994d0f274   ClusterIP   "클러스터IP"   <none>        1/TCP                20d
    glusterfs-dynamic-7e1feb35-a5d6-11eb-8013-029994d0f274   ClusterIP   "클러스터IP"   <none>        1/TCP                57d
    glusterfs-dynamic-8ce26e2f-a5d0-11eb-8013-029994d0f274   ClusterIP   "클러스터IP"   <none>        1/TCP                57d
    glusterfs-dynamic-ec32f7e9-c364-11eb-9c6e-029994d0f274   ClusterIP   "클러스터IP"   <none>        1/TCP                19d
    glusterfs-dynamic-edb992fa-c270-11eb-9e40-029994d0f274   ClusterIP   "클러스터IP"   <none>        1/TCP                20d
    glusterfs-dynamic-edc1b4bf-c270-11eb-9e40-029994d0f274   ClusterIP   "클러스터IP"   <none>        1/TCP                20d
    hdfs                                                     ClusterIP   "클러스터IP"   <none>        8020/TCP,50070/TCP   20d
    hdfs-datanode                                            ClusterIP   None          <none>        50075/TCP            20d
    hdfs-httpfs                                              ClusterIP   "클러스터IP"   <none>        14000/TCP            20d
    hdfs-namenode                                            ClusterIP   None          <none>        8020/TCP,50070/TCP   20d
    hdfs-namenode-exporter                                   ClusterIP   "클러스터IP"   <none>        5556/TCP             20d
    kibana-kibana                                            NodePort    "클러스터IP"   <none>        5601:30483/TCP       4d
    kubernetes                                               ClusterIP   "클러스터IP"   <none>        443/TCP              73d
    ```

# 사용 Usage

## collect-alpha

### REST API를 통한 데이터 적재 및 분석

1. SVC의 API server external IP 확인
    - "release name"-collect-alpha-api의 EXTERNAL-IP 확인

    ```bash
    NAME                                         TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                 AGE
    kubernetes                                   ClusterIP      "클러스터IP"    <none>         443/TCP                                 89m
    "release name"-collect-alpha-api             LoadBalancer   "클러스터IP"   "외부 Id"       2000:32183/TCP,2001:30923/TCP           95s
    "release name"-hbase-master                  ClusterIP      None           <none>          9090/TCP,9095/TCP,16000/TCP,16010/TCP   95s
    "release name"-hbase-master-metrics          ClusterIP      "클러스터IP"    <none>         5556/TCP                                95s
    "release name"-hbase-master-thrift-metrics   ClusterIP      "클러스터IP"    <none>         5557/TCP                                95s
    "release name"-hbase-region-metrics          ClusterIP      "클러스터IP"    <none>         5556/TCP                                95s
    "release name"-hbase-regionserver            ClusterIP      None           <none>          16020/TCP,16030/TCP                     95s
    "release name"-hdfs                          ClusterIP      "클러스터IP"   <none>          8020/TCP,50070/TCP                      95s
    "release name"-hdfs-datanode                 ClusterIP      None           <none>          50075/TCP                               95s
    "release name"-hdfs-httpfs                   ClusterIP      "클러스터IP"   <none>          14000/TCP                               94s
    "release name"-hdfs-namenode                 ClusterIP      None           <none>          8020/TCP,50070/TCP                      95s
    "release name"-hdfs-namenode-exporter        ClusterIP      "클러스터IP"   <none>          5556/TCP                                94s
    "release name"-kafka                         ClusterIP      "클러스터IP"   <none>          9092/TCP                                95s
    "release name"-kafka-headless                ClusterIP      None           <none>          9092/TCP                                94s
    "release name"-zookeeper                     ClusterIP      "클러스터IP"   <none>          2181/TCP,2888/TCP,3888/TCP              95s
    "release name"-zookeeper-headless            ClusterIP      None           <none>          2181/TCP,2888/TCP,3888/TCP              94s
    ```

2. 해당 IP와 2000번 포트를 이용해 유저의 코드 내에서 REST API 요청
- collect-alpha REST API 목록

    ## create table

    1. Hbase에 새로운 table을 추가한다. ( [http://(서버ip):2000/create-table](http://ip:2000/create-table) )
    2. 상세
        - POST method
        - body Datagram

        ```jsx
        {
        	table_name : 'table_name',
        	column_family_name : {
        		"column_family1 name": {},
        		"column_family2 name" : {},	
        	}
        }
        ```

        - table_name을 이름으로, column_family_name의 column_family들을 가지는 table을 생성한다,
    3. 결과
        - 유효하지 않은 Datagram 입력 시
            1. table_name이 없음 : "There is no table name (table_name)"
            2. column_family_name이 없음 : 해당 부분을 "cf1"으로 초기화 하여 테이블 생성
        - 테이블 생성 성공 시
            1. "Creating the 'table_name' table"

    ## table list

    1. Hbase의 table 목록을 반환한다. ( [http://(서버ip):2000/](http://ip:2000/create-table)table-list)
    2. 상세
        - GET method
        - argument 불필요
    3. 결과
        - 반환 실패 시
            1. Hbase의 오류 내용을 반환
        - 테이블 생성 성공 시
            1. table list를 반환

    ## put-rows

    1. Hbase의 특정 table에 row들을 추가한다. ( [http://(서버ip):2000/](http://ip:2000/create-table)put-rows)
    2. 상세
        - POST method
        - body Datagram

        ```jsx
        {
        	table_name : 'table_name',
        	datalist : [
        		{rowkey: '1',{'cf1:col1': '1', 'cf1:col2': '2'}},
            {rowkey: '2',{'cf1:col1': '3', 'cf1:col2': '4'}},
        			. . . 
        	],
        }
        ```

        - table_name에 해당하는 table에 datalist들을 추가한다.
    3. 결과
        - 해당 요청을 처리하는 server의 ip를 반환한다.

    ## delete table

    1. Hbase의 특정 table을 삭제한다 ( [http://(서버ip):2000/](http://ip:2000/create-table)delete-table?table_name="테이블 이름")
    2. 상세
        - GET method
        - arguments
            1. delete-table : 삭제하고자하는 table 이름
    3. 결과
        - 유효하지 않은 arguments
            1. table_name이 없음 : "There is no table name(table_name)"
        - Hbase에서 처리 실패
            1. 해당 table_name 미존재 : "There is no table name corresponding to hbase."
        - 삭제 성공 시
            1. "Deleting the 'table_name' table"

    ## scan

    1. Hbase의 특정 table에서 Scanning을 수행한다 ( [http://(서버ip):2000/](http://ip:2000/create-table)scan)
    2. 상세
        - POST method
        - body Datagram

        ```jsx
        {
        			table_name : 'table_name',
        			filter : "filter string",
        			row_start : 'row_start'
        			row_stop : 'row_stop',
        }
        ```

        - table_name에 해당하는 table에서 filter에 해당하는 row들을 Scan. 그 범위는 row_start에서 row_stop까지 해당된다.
        - filter string
            1. Hbase Scanner를 생성하는 Pseudo string
            2. Reference site : [https://docs.cloudera.com/documentation/enterprise/6/6.3/topics/admin_hbase_filtering.html](https://docs.cloudera.com/documentation/enterprise/6/6.3/topics/admin_hbase_filtering.html)
            3. 예시 : "SingleColumnValueFilter('ID','BLD_NM',=,'regexstring:경북대학교',true,true)"

                해당 필터는 Column family = ID, Column = BLD_NM인 칼럼의 값이 경북대를 포함하고 있는 row들을 반환한다.

    3. 결과
        - Scanning 결과를 반환
        - 결과 datagram

            ```jsx
            {
            			row_key: String,
            			row_data: Object,
            }
            ```

## collect-beta

### REST API를 통한 데이터 적재 및 분석

1. kibana의 NODE 확인
    - kubectl get pods -o wide

    ```bash
    NAME                             READY   STATUS    RESTARTS   AGE     IP             NODE    NOMINATED NODE   READINESS GATES
    counter2                         1/1     Running   0          14d     "IP"           node2   <none>           <none>
    elasticsearch-master-0           1/1     Running   0          19d     "IP"           node1   <none>           <none>
    elasticsearch-master-1           1/1     Running   0          14d     "IP"           node2   <none>           <none>
    fld-fluentd-0                    1/1     Running   0          14d     "IP"           node2   <none>           <none>
    fld-fluentd-hptpp                1/1     Running   0          14d     "IP"           node2   <none>           <none>
    fld-fluentd-m2gqj                1/1     Running   0          14d     "IP"           node1   <none>           <none>
    hdfs-datanode-0                  1/1     Running   0          14d     "IP"           node2   <none>           <none>
    hdfs-datanode-1                  1/1     Running   0          19d     "IP"           node1   <none>           <none>
    hdfs-datanode-2                  1/1     Running   0          14d     "IP"           node2   <none>           <none>
    hdfs-httpfs-7546bcd8f6-9qnzr     1/1     Running   0          16d     "IP"           node1   <none>           <none>
    hdfs-namenode-0                  2/2     Running   0          19d     "IP"           node1   <none>           <none>
    kibana-kibana-5b64cd58b5-h824s   1/1     Running   0          3d23h   "IP"           node1   <none>           <none>
    ```

2. 해당 NODE의 INTERNAL-IP 확인
    - kubectl get node -o wide

    ```bash
    NAME     STATUS   ROLES    AGE   VERSION    INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION       CONTAINER-RUNTIME
    master   Ready    master   73d   v1.14.10   "내부 IP"      <none>        Ubuntu 18.04.5 LTS   4.15.0-144-generic   docker://18.6.1
    node1    Ready    <none>   73d   v1.14.10   "kibana IP"    <none>        Ubuntu 18.04.5 LTS   4.15.0-144-generic   docker://18.6.1
    node2    Ready    <none>   73d   v1.14.10   "내부 IP"      <none>        Ubuntu 18.04.5 LTS   4.15.0-144-generic   docker://18.6.1
    ```

3. 내부 네트워크에서 http://"kibana IP":30483로 접속
    - 접속 예시
        
        ![1](https://user-images.githubusercontent.com/54903040/122807770-f0507000-d306-11eb-8c8f-c29494d4c8d8.png)


# Contact

Gmail : jkhk7788@gmail.com
