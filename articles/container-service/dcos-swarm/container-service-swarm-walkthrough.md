---
title: 빠른 시작 - Linux용 Azure Docker Swarm 클러스터
description: Azure Container Service에서 Azure CLI를 사용하여 Linux 컨테이너용 Docker Swarm 클러스터를 빠르게 만드는 방법에 대해 알아봅니다.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 1d658449e688c007560489f1370219cebce09df1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-docker-swarm-cluster"></a>Docker Swarm 클러스터 배포

이 빠른 시작에서는 Azure CLI를 사용하여 Docker Swarm 클러스터가 배포됩니다. 웹 프런트 엔드 및 Redis 인스턴스로 구성된 다중 컨테이너 응용 프로그램이 클러스터에 배포되어 실행됩니다. 완료되면 인터넷을 통해 응용 프로그램에 액세스할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 빠른 시작에서는 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다.

다음 예제에서는 *westus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

출력

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westcentralus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Docker Swarm 클러스터 만들기

[az acs create](/cli/azure/acs#az_acs_create) 명령을 사용하여 Azure Container Service에서 Docker Swarm 클러스터를 만듭니다. 

다음 예제에서는 하나의 Linux 마스터 노드와 세 개의 Linux 에이전트 노드가 있는 *mySwarmCluster*라는 클러스터를 만듭니다.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type Swarm --resource-group myResourceGroup --generate-ssh-keys
```

제한 평가판과 같이 Azure 구독의 Azure 리소스 액세스 권한이 제한되는 경우도 있습니다. 사용 가능한 코어 제한으로 인해 배포가 실패하는 경우 [az acs create](/cli/azure/acs#az_acs_create) 명령에 `--agent-count 1`을 추가하여 기본 에이전트 수를 줄이세요. 

몇 분 후 명령이 완료되고 클러스터에 대해 json으로 형식화된 정보가 반환됩니다.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

이 빠른 시작 전체에서 Docker Swarm 마스터와 Docker 에이전트 풀의 IP 주소가 모두 필요합니다. 두 IP 주소를 모두 반환하려면 다음 명령을 실행합니다.


```bash
az network public-ip list --resource-group myResourceGroup --query "[*].{Name:name,IPAddress:ipAddress}" -o table
```

출력

```bash
Name                                                                 IPAddress
-------------------------------------------------------------------  -------------
swarmm-agent-ip-myswarmcluster-myresourcegroup-d5b9d4agent-66066781  52.179.23.131
swarmm-master-ip-myswarmcluster-myresourcegroup-d5b9d4mgmt-66066781  52.141.37.199
```

Swarm 마스터에 대한 SSH 터널을 만듭니다. `IPAddress`를 Swarm 마스터의 IP 주소로 바꿉니다.

```bash
ssh -p 2200 -fNL 2375:localhost:2375 azureuser@IPAddress
```

`DOCKER_HOST` 환경 변수를 설정합니다. 이 옵션을 사용하면 호스트의 이름을 지정하지 않고 Docker Swarm에 대해 docker 명령을 실행할 수 있습니다.

```bash
export DOCKER_HOST=:2375
```

이제 Docker Swarm에서 Docker 서비스를 실행할 준비가 되었습니다.


## <a name="run-the-application"></a>응용 프로그램 실행

`docker-compose.yaml`이라는 파일을 만들고 다음 콘텐츠를 복사합니다.

```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

다음 명령을 실행하여 Azure Vote 서비스를 만듭니다.

```bash
docker-compose up -d
```

출력

```bash
Creating network "user_default" with the default driver
Pulling azure-vote-front (microsoft/azure-vote-front:v1)...
swarm-agent-EE873B23000005: Pulling microsoft/azure-vote-front:v1...
swarm-agent-EE873B23000004: Pulling microsoft/azure-vote-front:v1... : downloaded
Pulling azure-vote-back (redis:latest)...
swarm-agent-EE873B23000004: Pulling redis:latest... : downloaded
Creating azure-vote-front ... 
Creating azure-vote-back ... 
Creating azure-vote-front
Creating azure-vote-back ...
```

## <a name="test-the-application"></a>응용 프로그램 테스트

Azure Vote 응용 프로그램을 테스트하려면 Swarm 에이전트 풀의 IP 주소를 찾습니다.

![Azure Vote로 이동하는 이미지](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>클러스터 삭제
클러스터가 더 이상 필요하지 않으면 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>코드 가져오기

이 빠른 시작에서는 Docker 서비스를 만드는 데 미리 생성된 컨테이너 이미지를 사용했습니다. 관련된 응용 프로그램 코드, Dockerfile 및 Compose 파일을 GitHub에서 사용할 수 있습니다.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Docker Swarm 클러스터를 배포하고, 이 클러스터에 다중 컨테이너 응용 프로그램을 배포했습니다.

Docker warm을 Visual Studio Team Services와 통합하는 방법에 대해 자세히 알아보려면 Docker Swarm 및 VSTS를 사용하는 CI/CD를 진행합니다.

> [!div class="nextstepaction"]
> [Docker Swarm 및 VSTS를 사용하는 CI/CD](./container-service-docker-swarm-setup-ci-cd.md)
