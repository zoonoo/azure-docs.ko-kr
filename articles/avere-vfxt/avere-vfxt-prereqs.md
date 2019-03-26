---
title: Avere vFXT 필수 조건 - Azure
description: Avere vFXT for Azure에 대한 필수 조건입니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 04af92f21cecaa832e857a7017b67f815f6ab685
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417975"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Avere vFXT 만들기 준비

이 문서에서는 Avere vFXT 클러스터를 만들기 위한 필수 작업에 대해 설명합니다.

## <a name="create-a-new-subscription"></a>새 구독 만들기

먼저 새 Azure 구독을 만듭니다. 각 Avere vFXT 프로젝트에 대해 별도의 구독을 사용하면 모든 프로젝트 리소스와 비용을 쉽게 추적하고, 프로비전하는 동안 가능한 리소스 제한으로부터 다른 프로젝트를 보호하고, 정리 작업을 간소화할 수 있습니다.  

Azure Portal에 새 Azure 구독을 만들려면 다음을 수행합니다.

* [구독 블레이드](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)로 이동합니다.
* 위쪽에 있는 **+ 추가** 단추를 클릭합니다.
* 메시지가 표시되면 로그인합니다.
* 제안을 선택하고 새 구독을 만드는 단계를 수행합니다.

## <a name="configure-subscription-owner-permissions"></a>구독 소유자 권한 구성

구독에 대한 소유자 권한이 있는 사용자는 vFXT 클러스터를 만들어야 합니다. 특히 다음과 같은 작업에는 구독 소유자 권한이 필요합니다.

* Avere vFXT 소프트웨어 사용 약관에 동의
* 클러스터 노드 액세스 역할 만들기 

vFXT를 만드는 사용자에게 소유자 액세스 권한을 부여하지 않으려면 다음 두 가지 해결 방법이 있습니다.

* 다음 조건이 충족되는 경우 리소스 그룹 소유자가 클러스터를 만들 수 있습니다.

  * 구독 소유자는 [Avere vFXT 소프트웨어 사용 약관에 동의](#accept-software-terms)하고 [클러스터 노드 액세스 역할을 만들어야](#create-the-cluster-node-access-role) 합니다. 
  * 다음을 포함한 모든 Avere vFXT 리소스는 리소스 그룹 내에 배포해야 합니다.
    * 클러스터 컨트롤러
    * 클러스터 노드
    * Blob 저장소
    * 네트워크 요소
 
* RBAC(역할 기반 액세스 제어)를 미리 사용하여 사용자에게 권한을 할당하면 소유자 권한이 없는 사용자가 vFXT 클러스터를 만들 수 있습니다. 이 방법을 사용하는 경우 해당 사용자에게 매우 많은 권한이 제공됩니다. 액세스 역할을 만들어 소유자가 아닌 사용자에게 클러스터 만들기 권한을 부여하는 방법에 대한 설명은 [이 문서](avere-vfxt-non-owner.md)에 나와 있습니다.

## <a name="quota-for-the-vfxt-cluster"></a>vFXT 클러스터에 대한 할당량

다음 Azure 구성 요소에는 충분한 할당량이 있어야 합니다. 필요한 경우 [할당량 증가를 요청합니다](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> 여기에 나와 있는 가상 머신 및 SSD 구성 요소는 vFXT 클러스터 자체에 사용되는 것입니다. 계산 팜에 사용할 VM 및 SSD에 대한 할당량이 추가로 필요합니다.  워크플로를 실행할 지역에 대한 할당량을 사용하도록 설정되어 있는지 확인하세요.

|Azure 구성 요소|할당량|
|----------|-----------|
|가상 머신|3 개 이상의 e32s_v3와|
|프리미엄 SSD 저장소|노드당 200GB OS 공간 + 1TB-4TB 캐시 공간 |
|저장소 계정(선택 사항) |v2|
|데이터 백 엔드 저장소(선택 사항) |새 LRS Blob 컨테이너 1개 |

## <a name="accept-software-terms"></a>소프트웨어 약관 동의

> [!NOTE] 
> 구독 소유자가 Avere vFXT 클러스터를 만드는 경우에는 이 단계가 필요하지 않습니다.

클러스터를 만들 때 Avere vFXT 소프트웨어의 서비스 약관에 동의해야 합니다. 구독 소유자가 아닌 경우, 구독 소유자가 사용 약관에 미리 동의하도록 합니다. 이 단계는 구독당 한 번만 수행하면 됩니다.

소프트웨어 사용 약관에 미리 동의하려면 다음을 수행합니다. 

1. Azure Portal에서 또는 <https://shell.azure.com>으로 이동하여 클라우드 셸을 엽니다. 구독 ID로 로그인합니다.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. 다음 명령을 실행하여 서비스 약관에 동의하고, Avere vFXT for Azure 소프트웨어 이미지에 프로그래밍 방식으로 액세스하도록 설정합니다. 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-access-roles"></a>액세스 역할 만들기 

[RBAC(역할 기반 액세스 제어)](../role-based-access-control/index.yml)는 필요한 작업을 수행할 수 있는 권한을 vFXT 클러스터 컨트롤러 및 클러스터 노드에 부여합니다.

* 클러스터를 만들려면 클러스터 컨트롤러에 VM을 만들고 수정하는 권한이 필요합니다. 

* 개별 vFXT 노드는 일반적인 클러스터 작업의 일환으로 Azure 리소스 속성 읽기, 스토리지 관리, 다른 노드의 네트워크 인터페이스 설정 제어 등의 작업을 수행해야 합니다.

Avere vFXT 클러스터를 만들려면 먼저 클러스터 노드에 사용할 사용자 지정 역할을 정의해야 합니다. 

클러스터 컨트롤러의 경우 템플릿에서 기본 역할을 수락할 수 있습니다. 기본 역할은 클러스터 컨트롤러에 리소스 그룹 소유자 권한을 제공합니다. 컨트롤러용으로 사용자 지정 역할을 만들려는 경우 [사용자 지정된 컨트롤러 액세스 역할](avere-vfxt-controller-role.md)을 참조하세요.

> [!NOTE] 
> 구독 소유자 또는 소유자/사용자 액세스 관리자 역할의 사용자만 역할을 만들 수 있습니다. 이러한 역할은 미리 만들 수 있습니다.  

### <a name="create-the-cluster-node-access-role"></a>클러스터 노드 액세스 역할 만들기

<!-- caution - this header is linked to in the template so don't change it unless you can change that -->

클러스터 노드 역할을 만들어야 용 Avere vFXT for Azure 클러스터를 만들 수 있습니다.

> [!TIP] 
> Microsoft 내부 사용자는 새로 만들려고 시도하는 대신 “Avere 클러스터 런타임 운영자”라는 기존 역할을 사용해야 합니다. 

1. 이 파일을 복사합니다. AssignableScopes 줄에 구독 ID를 추가합니다.

   이 파일의 최신 버전은 github.com/Azure/Avere 리포지토리에 [AvereOperator.txt](https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereOperator.txt)로 저장되어 있습니다.  

   ```json
   {
      "AssignableScopes": [
          "/subscriptions/PUT_YOUR_SUBSCRIPTION_ID_HERE"
      ],
      "Name": "Avere Operator",
      "IsCustom": "true",
      "Description": "Used by the Avere vFXT cluster to manage the cluster",
      "NotActions": [],
      "Actions": [
          "Microsoft.Compute/virtualMachines/read",
          "Microsoft.Network/networkInterfaces/read",
          "Microsoft.Network/networkInterfaces/write",
          "Microsoft.Network/virtualNetworks/read",
          "Microsoft.Network/virtualNetworks/subnets/read",
          "Microsoft.Network/virtualNetworks/subnets/join/action",
          "Microsoft.Network/networkSecurityGroups/join/action",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
          "Microsoft.Storage/storageAccounts/blobServices/containers/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "DataActions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ]
   }
   ```

1. 파일을 ``avere-operator.json`` 또는 쉽게 기억할 수 있는 비슷한 파일 이름으로 저장합니다. 


1. Azure Cloud Shell을 열고 [이 문서 앞부분에서 설명](#accept-software-terms)한 구독 ID로 로그인합니다. 다음 명령을 사용하여 역할을 만듭니다.

   ```bash
   az role definition create --role-definition /avere-operator.json
   ```

역할 이름은 클러스터를 만들 때 사용됩니다. 이 예제에서 이름은 ``avere-operator``입니다.

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>가상 네트워크 (필요한 경우)에서 저장소 서비스 끝점 만들기

A [서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md) Azure Blob 트래픽이 로컬 virtual network 외부 라우팅 하지 않고 유지 합니다. 백 엔드 데이터 저장소에 대 한 Azure Blob을 사용 하는 Azure 클러스터에 대 한 모든 Avere vFXT에 대 한 것이 좋습니다. 

기존 vnet을 제공 하 고 클러스터 만들기의 일환으로 백 엔드 저장소에 대 한 새 Azure Blob 컨테이너를 만드는 하는 경우 서비스 끝점을 Microsoft storage에 대 한 vnet에 있어야 합니다. 이 끝점은 클러스터를 만들기 전에 존재 해야 합니다 또는 생성에 실패 합니다. 

저장소 서비스 끝점을 나중에 저장소를 추가 하는 경우에 Azure Blob storage를 사용 하는 Azure 클러스터에 대 한 모든 Avere vFXT에 권장 됩니다. 

> [!TIP] 
> * 클러스터 만들기의 일환으로 새 가상 네트워크를 만들려는 경우이 단계를 건너뜁니다. 
> * 이 단계는 클러스터를 만드는 동안 Blob storage를 만들지 않는 경우에 선택 사항입니다. 이 경우 만들 수 있습니다 서비스 끝점을 나중에 Azure Blob을 사용 하려는 경우.

Azure portal에서 저장소 서비스 끝점을 만듭니다. 

1. 포털의 왼쪽에서 **가상 네트워크**를 클릭합니다.
1. 클러스터에 대 한 vnet을 선택 합니다. 
1. 왼쪽에서 **서비스 엔드포인트**를 클릭합니다.
1. 위쪽에서 **추가**를 클릭합니다.
1. 로 서비스를 중지할 ``Microsoft.Storage`` 클러스터의 서브넷을 선택 합니다.
1. 아래쪽에서 **추가**를 클릭합니다.

   ![서비스 엔드포인트를 만드는 단계에 대한 주석이 있는 Azure Portal 스크린샷](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>다음 단계: vFXT 클러스터 만들기

이러한 필수 조건이 완료되면 클러스터 자체 만들기로 이동할 수 있습니다. 지침은 [vFXT 클러스터 배포](avere-vfxt-deploy.md)를 참조하세요.