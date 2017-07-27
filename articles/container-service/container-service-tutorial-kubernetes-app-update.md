---
title: "Azure Container Service 자습서 - 응용 프로그램 업데이트 | Microsoft Docs"
description: "Azure Container Service 자습서 - 응용 프로그램 업데이트"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: ce4a88a7958116890ec17eb2405ba809487b9c0f
ms.contentlocale: ko-kr
ms.lasthandoff: 07/06/2017

---

# <a name="update-an-application-in-kubernetes"></a>Kubernetes에서 응용 프로그램 업데이트

Kubernetes에서 응용 프로그램을 배포한 후 새 컨테이너 이미지 또는 이미지 버전을 지정하여 업데이트할 수 있습니다. 응용 프로그램을 업데이트할 때 배포의 일부분만 동시에 업데이트되도록 업데이트 출시가 스테이징됩니다. 

이 스테이징된 업데이트를 사용하면 업데이트 동안 응용 프로그램을 실행 중 상태로 유지할 수 있으며 배포 오류가 발생할 경우 롤백 메커니즘이 제공됩니다. 

이 자습서에서는 샘플 Azure 투표 앱을 업데이트합니다. 완료하는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 프런트 엔드 응용 프로그램 코드 업데이트
> * 업데이트된 컨테이너 이미지 만들기
> * Azure Container Registry에 컨테이너 이미지 밀어넣기
> * 업데이트된 응용 프로그램 배포

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 응용 프로그램을 컨테이너 이미지에 패키지하고, Azure Container Registry에 이미지를 업로드하고, Kubernetes 클러스터를 만들었습니다. 그런 다음 Kubernetes 클러스터에서 응용 프로그램을 실행했습니다. 

이러한 단계를 수행하지 않은 경우 지금 시도하려면 [자습서 1 – 컨테이너 이미지 만들기](./container-service-tutorial-kubernetes-prepare-app.md)로 돌아갑니다. 

최소한 이 자습서에는 실행 중인 응용 프로그램이 있는 Kubernetes 클러스터가 필요합니다.

## <a name="update-application"></a>응용 프로그램 업데이트

이 자습서의 단계를 완료하려면 Azure 투표 응용 프로그램의 복사본을 복제한 상태여야 합니다. 필요한 경우 다음 명령을 사용하여 이 복제된 복사본을 만듭니다.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

아무 코드 또는 텍스트 편집기나 사용하여 `config_file.cfg` 파일을 엽니다. 복제된 리포지토리의 다음 디렉터리에서 이 파일을 찾을 수 있습니다.

```bash
 /azure-voting-app/azure-vote/azure-vote/config_file.cfg
```

`VOTE1VALUE` 및 `VOTE2VALUE`의 값을 변경한 다음 파일을 저장합니다.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Half Full'
VOTE2VALUE = 'Half Empty'
SHOWHOST = 'false'
```

`docker build`를 사용하여 프런트 엔드 이미지를 다시 만듭니다.

```bash
docker build --no-cache ./azure-voting-app/azure-vote -t azure-vote-front:v2
```

## <a name="test-application"></a>응용 프로그램 테스트

Docker 네트워크를 만듭니다. 이 네트워크는 컨테이너 간의 통신에 사용됩니다.  

```bash
docker network create azure-vote
```

`docker run` 명령을 사용하여 백 엔드 컨테이너 이미지의 인스턴스를 실행합니다.

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

프런트 엔드 컨테이너 이미지의 인스턴스를 실행합니다.

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front:v2
```

`http://localhost:8080`으로 이동하여 업데이트된 응용 프로그램을 확인합니다. 응용 프로그램을 초기화하는 데 몇 초가 걸립니다. 오류가 발생하면 다시 시도합니다.

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>이미지 태그 지정 및 밀어넣기

*azure-vote-front* 이미지에 컨테이너 레지스트리의 loginServer로 태그를 지정합니다.

Azure Container Registry를 사용 중인 경우 [az acr list](/cli/azure/acr#list) 명령을 사용하여 로그인 서버 이름을 가져옵니다.

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

[Docker Tag](https://docs.docker.com/engine/reference/commandline/tag/)를 사용해 이미지에 태그를 지정하여 명령을 해당 Azure Container Registry 로그인 서버 또는 공개 레지스트리 호스트 이름으로 업데이트합니다.

```bash
docker tag azure-vote-front:v2 <acrLoginServer>/azure-vote-front:v2
```

레지스트리에 이미지를 밀어넣습니다. `<acrLoginServer>`를 해당 Azure Container Registry 로그인 서버 이름 또는 공개 레지스트리 호스트 이름으로 바꿉니다.

```bash
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-update-to-kubernetes"></a>Kubernetes에 업데이트 배포

### <a name="verify-multiple-pod-replicas"></a>여러 POD 복제본 확인

최대 작동 시간을 보장하려면 응용 프로그램 Pod의 여러 인스턴스가 실행되고 있어야 합니다. [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 명령을 사용하여 이 구성을 확인합니다.

```bash
kubectl get pod
```

출력:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

azure-vote-front 이미지를 실행 중인 여러 Pod가 없는 경우 *azure-vote-front* 배포를 확장합니다.


```bash
kubectl scale --replicas=3 deployment/azure-vote-front
```

### <a name="update-application"></a>응용 프로그램 업데이트

응용 프로그램을 업데이트하려면 다음 명령을 실행합니다. `<acrLoginServer>`를 컨테이너 레지스트리의 로그인 서버 또는 호스트 이름으로 업데이트합니다.

```bash
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

배포를 모니터링하려면 [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 명령을 사용합니다. 업데이트된 응용 프로그램이 배포되면 Pod가 종료되고 새 컨테이너 이미지로 다시 만들어집니다.

```bash
kubectl get pod
```

출력:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>업데이트된 응용 프로그램 테스트

*azure-vote-front* 서비스의 외부 IP 주소를 가져옵니다.

```bash
kubectl get service
```

IP 주소로 이동하여 업데이트된 응용 프로그램을 확인합니다.

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 응용 프로그램을 업데이트하고 이 업데이트를 Kubernetes 클러스터에 배포했습니다. 다음 작업을 완료했습니다.  

> [!div class="checklist"]
> * 프런트 엔드 응용 프로그램 코드 업데이트
> * 업데이트된 컨테이너 이미지 만들기
> * Azure Container Registry에 컨테이너 이미지 밀어넣기
> * 업데이트된 응용 프로그램 배포

다음 자습서로 이동하여 Operations Management Suite로 Kubernetes를 모니터링하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [OMS로 Kubernetes 모니터링](./container-service-tutorial-kubernetes-monitor.md)
