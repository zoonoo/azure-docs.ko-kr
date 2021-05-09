---
title: Azure VMware Solution 프라이빗 클라우드 만들기
description: Azure Portal을 사용하여 Azure VMware Solution 프라이빗 클라우드를 만드는 단계입니다.
ms.topic: include
ms.date: 04/23/2021
ms.openlocfilehash: 40bd1880511f22d9518d0c4526bc697a3693a518
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107945835"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Azure VMware Solution 프라이빗 클라우드는 Azure Portal 또는 Azure CLI를 사용하여 만들 수 있습니다.


### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **새 리소스 만들기** 를 선택합니다. 

1. **Marketplace 검색** 텍스트 상자에서 `Azure VMware Solution`을 입력하고, 목록에서 **Azure VMware 솔루션** 을 선택합니다. 

1. **Azure VMware Solution** 창에서 **만들기** 를 선택합니다.

1. **기본** 탭에서 필드 값을 입력합니다. 

   >[!TIP]
   >이 빠른 시작의 [계획 단계](../production-ready-deployment-steps.md) 중에 이 정보를 수집했습니다.

   | 필드   | 값  |
   | ---| --- |
   | **구독** | 배포에 사용하려는 구독을 선택합니다.|
   | **리소스 그룹** | 프라이빗 클라우드 리소스의 리소스 그룹을 선택합니다. |
   | **위치** | 위치(예: **미국 동부**)를 선택합니다. 위치는 계획 단계 중에 정의한 *지역* 입니다. |
   | **리소스 이름** | Azure VMware Solution 프라이빗 클라우드 이름을 제공합니다. |
   | **SKU** | **AV36** 을 선택합니다. |
   | **호스트** | 프라이빗 클라우드 클러스터에 할당된 호스트 수를 표시합니다. 기본값은 3이며, 배포 후에 늘리거나 줄일 수 있습니다.  |
   | **주소 블록** | 프라이빗 클라우드의 CIDR 네트워크에 대한 IP 주소 블록(예: 10.175.0.0/22)을 입력합니다. |
   | **Virtual Network** | Azure VMware Solution ExpressRoute 회로가 배포 후 단계로 설정되므로 이를 비워 둡니다.   |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="기본 탭에서 필드 값을 입력합니다." border="true":::

1. 완료되면 **검토 + 만들기** 를 선택합니다. 다음 화면에서 입력한 정보를 확인합니다. 정보가 모두 올바르면 **만들기** 를 선택합니다.

   > [!NOTE]
   > 이 단계는 약 3~4시간이 걸립니다. 기존 또는 동일한 클러스터에 단일 노드를 추가하는 데는 30~45분이 소요됩니다.

1. 배포가 성공했는지 확인합니다. 만든 리소스 그룹으로 이동하고 프라이빗 클라우드를 선택합니다.  배포가 완료되면 **성공** 상태가 표시됩니다. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="배포가 성공했는지 확인합니다." border="true":::


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure VMware Solution 프라이빗 클라우드를 만드는 Azure Portal 대신 Azure Cloud Shell을 사용하여 Azure CLI를 사용할 수 있습니다. Azure VMware Solution에서 사용할 수 있는 명령 목록은 [Azure VMware 명령](/cli/azure/ext/vmware/vmware)을 참조하세요.

Azure CLI 사용을 시작하려면 다음을 수행합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]


1. ['az group create'](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

   ```azurecli-interactive
   
   az group create --name myResourceGroup --location eastus
   ```

2. 리소스 그룹 및 프라이빗 클라우드에 대한 이름, 위치 및 클러스터 크기를 제공합니다.

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
