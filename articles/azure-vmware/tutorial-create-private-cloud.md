---
title: 자습서 - Azure에서 vSphere 클러스터 배포
description: Azure VMWare Solution을 사용하여 Azure에 vSphere 클러스터를 배포하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 09/07/2020
ms.openlocfilehash: 69a29a459ba283bb34169112ac2fa174ac6a14af
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512376"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>자습서: Azure에서 Azure VMware Solution 프라이빗 클라우드 배포

Azure VMware Solution은 vSphere 클러스터를 Azure에 배포할 수 있는 기능을 제공합니다. 최소 초기 배포는 세 개의 호스트입니다. 클러스터당 최대 16개의 호스트를 한 번에 하나씩 추가할 수 있습니다. 

Azure VMware Solution에서는 시작 시 온-프레미스 vCenter를 사용하여 프라이빗 클라우드를 관리할 수 없으므로 로컬 vCenter 인스턴스, 가상 네트워크 등에 대한 추가 구성 및 연결이 필요합니다. 이러한 절차 및 관련 사전 요구 사항은 이 자습서에서 다룹니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

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

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-avs-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

Azure VMware Solution 프라이빗 클라우드를 만드는 Azure Portal 대신 Azure Cloud Shell을 사용하여 Azure CLI를 사용할 수 있습니다. 공통 Azure 도구가 사전 설치되고 계정으로 사용하도록 구성된 체험 대화형 셸입니다. 

#### <a name="open-azure-cloud-shell"></a>Azure Cloud Shell 열기

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택합니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, **Enter** 키를 눌러 실행합니다.

#### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```
azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>프라이빗 클라우드 만들기

리소스 그룹 이름, 프라이빗 클라우드 이름, 위치, 클러스터 크기를 제공합니다.

| 속성  | 설명  |
| --------- | ------------ |
| **-g**(리소스 그룹 이름)     | 프라이빗 클라우드 리소스에 대한 리소스 그룹의 이름입니다.        |
| **-n**(프라이빗 클라우드 이름)     | Azure VMware Solution 프라이빗 클라우드의 이름입니다.        |
| **--위치**     | 프라이빗 클라우드에 사용되는 위치입니다.         |
| **--클러스터 크기**     | 클러스터의 크기입니다. 최솟값은 3입니다.         |
| **--네트워크 블록**     | 프라이빗 클라우드에 사용할 CIDR IP 주소 네트워크 블록입니다. 주소 블록은 구독과 온-프레미스 네트워크에 있는 다른 가상 네트워크에서 사용되는 주소 블록과 겹칠 수 없습니다.        |
| **--sku** | SKU 값: AV36 |

```
azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-a-private-cloud-azure-portal"></a>프라이빗 클라우드 삭제(Azure Portal)

더 이상 필요하지 않은 Azure VMware Solution 프라이빗 클라우드가 있으면 이를 삭제할 수 있습니다. 프라이빗 클라우드를 삭제하면 모든 클러스터가 해당 구성 요소와 함께 삭제됩니다.

이렇게 하려면 Azure Portal에서 프라이빗 클라우드로 이동하여 **삭제**를 선택합니다. 확인 페이지에서 프라이빗 클라우드 이름으로 확인하고, **예**를 선택합니다.

> [!CAUTION]
> 프라이빗 클라우드 삭제는 되돌릴 수 없는 작업입니다. 프라이빗 클라우드를 삭제하면 실행 중인 모든 워크로드, 구성 요소가 종료되고 공용 IP 주소를 포함하여 모든 프라이빗 클라우드 데이터 및 구성 설정이 삭제되므로 데이터를 복구할 수 없습니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure VMware Solution 프라이빗 클라우드 만들기
> * 배포된 프라이빗 클라우드 확인

프라이빗 클라우드 클러스터에 대한 로컬 관리 설정의 일부로 프라이빗 클라우드에서 사용할 가상 네트워크를 만드는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Virtual Network 만들기](tutorial-configure-networking.md)
