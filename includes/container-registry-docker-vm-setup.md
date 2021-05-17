---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: d699e8985a3a23b3aab87601d5298d9c8f7e34e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244842"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Docker 지원 가상 머신 만들기

테스트를 위해 Docker 사용 Ubuntu VM을 사용하여 Azure 컨테이너 레지스트리에 액세스합니다. 레지스트리에 Azure Active Directory 인증을 사용하려면 VM에 [Azure CLI][azure-cli]도 설치합니다. Azure 가상 머신이 이미 있는 경우 이 생성 단계를 건너뜁니다.

가상 머신과 컨테이너 레지스트리에 동일한 리소스 그룹을 사용할 수 있습니다. 이 설정은 종료 시 정리 작업을 단순화하지만 필수는 아닙니다. 가상 머신 및 가상 네트워크에 대해 별도의 리소스 그룹을 만들도록 선택하는 경우 [az group create][az-group-create]를 실행합니다. 다음 예제에서는 리소스 그룹 이름 및 레지스트리 위치에 대한 환경 변수를 설정했다고 가정합니다.

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

이제 [az vm create][az-vm-create]를 사용하여 기본 Ubuntu Azure 가상 머신을 배포합니다. 다음 예제에서는 *myDockerVM* 이라는 VM을 만듭니다.

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM을 만드는 데 몇 분 정도 걸립니다. 명령이 완료되면 Azure CLI에 표시된 `publicIpAddress`를 기록해 둡니다. 이 주소를 사용하여 VM에 SSH 연결을 만듭니다.

### <a name="install-docker-on-the-vm"></a>VM에 Docker 설치

VM이 실행된 후 VM에 SSH 연결을 만듭니다. *publicIpAddress* 를 VM의 공용 IP 주소로 바꿉니다.

```bash
ssh azureuser@publicIpAddress
```

다음 명령을 실행하여 Ubuntu VM에 Docker를 설치합니다.

```bash
sudo apt-get update
sudo apt install docker.io -y
```

설치 후 다음 명령을 실행하여 VM에서 Docker가 제대로 실행되는지 확인합니다.

```bash
sudo docker run -it hello-world
```

출력:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure CLI 설치

[apt를 사용하여 Azure CLI 설치](/cli/azure/install-azure-cli-apt)의 단계를 따라 Ubuntu 가상 머신에 Azure CLI를 설치합니다. 예:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

SSH 연결을 종료합니다.

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-group-create]: /cli/azure/group