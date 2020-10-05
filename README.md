# Gremlin Helm Charts

해당 문서는 인터넷 접속이 제한된 프라이빗망에 설치된 쿠버네티스 환경에 Gremlin을 설치하는 방법에 대해서 안내하고 있습니다.

## 도커 이미지 및 Helm Charts 다운로드

인터넷 접근이 가능한 호스트에서 Gremlin 설치에 필요한 파일들을 다운 받습니다

1. 도커 이미지 다운로드

    ```bash
    docker pull gremlin/gremlin
    docker pull gremlin/chao
    ```

2. 도커 이미지를 tar 파일로 저장

    ```bash
    docker save -o gremlin.tar gremlin/gremlin
    docker save -o chao.tar gremlin/chao
    ```

3. Gremlin Helm Charts 다운로드

    ```bash
    git clone https://github.com/gremlin/helm.git
    ```

4. 미디어 저장장치를 이용하거나 내부 네트워크를 통해서 gremlin.tar, chao.tar, helm 폴더를 kubectl 명령어를 실행시킬수 있는 호스트로 복사합니다

## 도커 이미지를 Private Registry에 저장

Private Registry에 접근이 가능한 호스트에서 gremlin.tar, chao.tar 파일을 복사하고 아래 명령어를 실행

1. 도커 이미지 불러오기

    ```bash
    docker load -i gremlin.tar
    docker load -i chao.tar
    ```

2. 불러온 두개의 도커이미지에 새로운 Tag를 부여하고 Private Registry로 Push

## Install Charts

위에서 다운받은 Helm Charts를 kubectl 및 Helm을 실행시킬수 있는 호스트에서 복사하고 아래의 명령어를 실행

```bash
kubectl create namespace gremlin

helm install gremlin HELM_CHARTS_PATH \
    --namespace gremlin \
    --set gremlin.secret.managed=true \
    --set gremlin.secret.type=secret \
    --set gremlin.secret.teamID=TEAM_ID \
    --set gremlin.secret.clusterID=CLUSTER_ID \
    --set gremlin.secret.teamSecret=TEAM_SERCET \
    --set image.repository=GREMLIN_IMAGE_REPO_URL \
    --set chaoimage.repository=CHAO_IMAGE_REPO_URL
```

- HELM_CHARTS_PATH는 로컬 호스트에 복사된 Helm Charts 폴더경로 입력 (e.g. helm/gremlin)
- TEAM_ID 및 TEAM_SERCET은 Gremlin 관리 콘솔에서 생성 및 확인 가능
- CLUSTER_ID는 Gremlin 콘솔에서 쿠버네티스 클러스터들을 구별하는 Identifier로 사용자 편의게 맞게 입력
- GREMLIN_IMAGE_REPO_URL에 gremlin.tar에서 불러온 도커이미지가 저장된 리포지토리 URL 입력
- CHAO_IMAGE_REPO_URL chao.tar에서 불러온 도커이미지가 저장된 리포지토리 URL 입력
