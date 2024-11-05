# k8s - Elasticsearch, Logstash 환경 구축하기

[➡️ 블로그 주소](https://shinebyul.tistory.com/73)

---
## Elasticsearch 7.17.16
- nori-analyzer와 korean-analyzer(초성검색)을 사용하지 않을 경우
  ```Statefulset.yml```, ```Statefulset-Service.yml``` 사용
- nori-analyzer와 korean-analyzer(초성검색)을 사용할 경우
  ```ECK.yml```, ```ECK-Servcie.yml``` 사용

### ECK 다운로드
```agsl
kubectl create -f https://download.elastic.co/downloads/eck/2.14.0/crds.yaml
kubectl apply -f https://download.elastic.co/downloads/eck/2.14.0/operator.yaml
```
### Korean-analyzer
[korean-analyzer 깃허브 주소](https://github.com/HYS1753/elasticsearch-custom-plugin-korean-analyzer)

---
## Logstash 7.17.16
### mariadb-jdbc-driver 다운로드
[다운로드 링크](https://downloads.mariadb.com/Connectors/java/connector-java-2.6.2/)
### jdbc-driver 파일로 ConfigMap 만드는 방법
1. scp 사용해서 로컬 컴퓨터의 jdbc-driver 파일을 마스터 노드로 옮기기
```agsl
scp [jdbc 파일이 저장된 로컬경로]/mariadb-java-client-3.4.1.jar 마스터노드사용자@[마스터노드 ip주소]:/home/test 

# 예시
scp 로컬경로/mariadb-java-client-3.4.1.jar test@123.123.123.123:/home/test 
```
2. 마스터에 ssh로 접속
```agsl
ssh [마스터 노드 사용자]@[마스터 노드 ip주소] -p [ssh 접속 포트 번호]

#예시
ssh test@123.123.123 -p 22 
# ssh test@123.123.123
# (ssh 기본포트인 22일 경우 생략 가능)
```
3. jdbc driver 파일을 컨피그맵으로 들기
```agsl
kubectl create configmap [만들 컨피그맵 이름] --from-file=/home/test/mariadb-java-client-3.4.1.jar -n [네임스페이스 이름] 


아래 명령어를 사용하면 잘 만들어졌는지 확인 가능
kubectl describe configmap [만든 컨피그맵 이름] -n [네임스페이스 이름]
```