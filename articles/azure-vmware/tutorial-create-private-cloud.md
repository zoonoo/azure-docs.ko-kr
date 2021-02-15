---
title: 자습서 - Azure에서 vSphere 클러스터 배포
description: Azure VMware Solution을 사용하여 Azure에 vSphere 클러스터를 배포하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 11/19/2020
ms.openlocfilehash: 3c8ae3673ad049153c2b9700bd7efae6c4c286ed
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093950"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>자습서: Azure에서 Azure VMware Solution 프라이빗 클라우드 배포

Azure VMware Solution은 vSphere 클러스터를 Azure에 배포할 수 있는 기능을 제공합니다. 최소 초기 배포는 세 개의 호스트입니다. 클러스터당 최대 16개의 호스트를 한 번에 하나씩 추가할 수 있습니다. 

Azure VMware Solution에서는 시작 시 온-프레미스 vCenter를 사용하여 프라이빗 클라우드를 관리할 수 없으므로 추가 구성이 필요합니다. 이러한 절차 및 관련 사전 요구 사항은 이 자습서에서 다룹니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Azure VMware Solution 프라이빗 클라우드 만들기
> * 배포된 프라이빗 클라우드 확인

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 프라이빗 클라우드를 만들 수 있는 적절한 관리 권한 및 사용 권한
- [자습서: 네트워크 검사 목록](tutorial-network-checklist.md)에서 설명한 대로 적절한 네트워킹이 구성되어 있는지 확인합니다.

## <a name="register-the-resource-provider"></a>리소스 공급자 등록

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="create-a-private-cloud"></a>프라이빗 클라우드 만들기

Azure VMware Solution 프라이빗 클라우드는 [Azure Portal](#azure-portal) 또는 [Azure CLI](#azure-cli)를 사용하여 만들 수 있습니다.

### <a name="azure-portal"></a>Azure portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

Azure VMware Solution 프라이빗 클라우드를 만드는 Azure Portal 대신 Azure Cloud Shell을 사용하여 Azure CLI를 사용할 수 있습니다.  Azure VMware Solution에서 사용할 수 있는 명령 목록은 [Azure VMware 명령](/cli/azure/ext/vmware/vmware)을 참조하세요.

#### <a name="open-azure-cloud-shell"></a>Azure Cloud Shell 열기

코드 블록의 오른쪽 위 모서리에서 **시도** 를 선택합니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, **Enter** 키를 눌러 실행합니다.

#### <a name="create-a-resource-group"></a>리소스 그룹 만들기

`[az group create](/cli/azure/group)` 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>프라이빗 클라우드 만들기

리소스 그룹 및 프라이빗 클라우드에 대한 이름, 위치 및 클러스터 크기를 제공합니다.

| 속성  | 설명  |
| --------- | ------------ |
| **-g**(리소스 그룹 이름)     | 프라이빗 클라우드 리소스에 대한 리소스 그룹의 이름입니다.        |
| **-n**(프라이빗 클라우드 이름)     | Azure VMware Solution 프라이빗 클라우드의 이름입니다.        |
| **--위치**     | 프라이빗 클라우드에 사용되는 위치입니다.         |
| **--클러스터 크기**     | 클러스터의 크기입니다. 최솟값은 3입니다.         |
| **--네트워크 블록**     | 프라이빗 클라우드에 사용할 CIDR IP 주소 네트워크 블록입니다. 주소 블록은 구독과 온-프레미스 네트워크에 있는 다른 가상 네트워크에서 사용되는 주소 블록과 겹칠 수 없습니다.        |
| **--sku** | SKU 값: AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="azure-vmware-commands"></a>Azure VMware 명령

Azure VMware Solution에서 사용할 수 있는 명령 목록은 [Azure VMware 명령](/cli/azure/ext/vmware/vmware)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure VMware Solution 프라이빗 클라우드 만들기
> * 배포된 프라이빗 클라우드 확인
> * Azure VMware Solution 프라이빗 클라우드 삭제

점프 상자를 만드는 방법을 알아보려면 다음 자습서를 계속 진행합니다. 로컬에서 프라이빗 클라우드를 관리할 수 있도록 점프 상자를 사용하여 환경에 연결합니다.


> [!div class="nextstepaction"]
> [Azure VMware Solution 프라이빗 클라우드에 액세스](tutorial-access-private-cloud.md)