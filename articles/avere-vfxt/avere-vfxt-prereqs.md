---
title: Avere vFXT 필수 조건 - Azure
description: Avere vFXT for Azure에 대한 필수 조건입니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 0dafef7cf262153ccdb3b490aa0c7bd039b4a89b
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889180"
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

구독에 대한 소유자 권한이 있는 사용자는 vFXT 클러스터를 만들어야 합니다. 소프트웨어 서비스 약관에 동의 하 고 다른 작업을 수행 하려면 구독 소유자 권한이 필요 합니다.

비 소유자가 Azure 클러스터용 Avere vFTX를 만들 수 있도록 하는 몇 가지 해결 방법이 있습니다. 이러한 시나리오에는 리소스를 제한 하 고 작성자에 게 추가 역할을 할당 하는 작업이 포함 됩니다. 두 경우 모두 구독 소유자는 미리 [Avere vFXT 소프트웨어 용어](#accept-software-terms) 를 미리 수락 해야 합니다.

| 시나리오 | 제한 | Avere vFXT 클러스터를 만드는 데 필요한 액세스 역할 |
|----------|--------|-------|
| 리소스 그룹 관리자 | 가상 네트워크, 클러스터 컨트롤러 및 클러스터 노드는 리소스 그룹 내에 만들어야 합니다. | [사용자 액세스 관리자](../role-based-access-control/built-in-roles.md#user-access-administrator) 및 [참가자](../role-based-access-control/built-in-roles.md#contributor) 역할, 둘 다 대상 리소스 그룹으로 범위가 지정 됩니다. |
| 외부 가상 네트워크 | 클러스터 컨트롤러와 클러스터 노드가 리소스 그룹 내에 만들어지지만 다른 리소스 그룹의 기존 가상 네트워크는 사용 됩니다. | (1) [사용자 액세스 관리자](../role-based-access-control/built-in-roles.md#user-access-administrator) 및 [참가자](../role-based-access-control/built-in-roles.md#contributor) 역할이 vFXT 리소스 그룹으로 범위가 지정 됩니다. 그리고 (2) [가상 머신 참가자](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [사용자 액세스 관리자](../role-based-access-control/built-in-roles.md#user-access-administrator)및 [Avere 참여자](../role-based-access-control/built-in-roles.md#avere-contributor) 역할 범위는 가상 네트워크의 리소스 그룹으로 지정 됩니다. |

또 다른 방법은 [이 문서](avere-vfxt-non-owner.md)에 설명 된 대로 사용자 지정 RBAC (역할 기반 액세스 제어) 역할을 미리 만들고 사용자에 게 권한을 할당 하는 것입니다. 이 방법을 사용하는 경우 해당 사용자에게 매우 많은 권한이 제공됩니다.

## <a name="quota-for-the-vfxt-cluster"></a>vFXT 클러스터에 대한 할당량

다음 Azure 구성 요소에는 충분한 할당량이 있어야 합니다. 필요한 경우 [할당량 증가를 요청합니다](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

> [!NOTE]
> 여기에 나와 있는 가상 머신 및 SSD 구성 요소는 vFXT 클러스터 자체에 사용되는 것입니다. 컴퓨팅 팜에 사용할 VM 및 SSD에 대한 할당량이 추가로 필요합니다.  워크플로를 실행할 지역에 대한 할당량을 사용하도록 설정되어 있는지 확인하세요.

|Azure 구성 요소|할당량|
|----------|-----------|
|Virtual Machines|3개 이상의 E32s_v3|
|프리미엄 SSD 스토리지|노드당 200GB OS 공간 + 1TB-4TB 캐시 공간 |
|스토리지 계정(선택 사항) |v2|
|데이터 백 엔드 스토리지(선택 사항) |새 LRS Blob 컨테이너 1개 |

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

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>가상 네트워크에 저장소 서비스 끝점 만들기 (필요한 경우)

[서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md) 은 Azure Blob 트래픽을 가상 네트워크 외부로 라우팅하는 대신 로컬에 유지 합니다. 백 엔드 데이터 저장소에 Azure Blob을 사용 하는 모든 Avere vFXT for Azure 클러스터에 권장 됩니다.

클러스터를 만드는 과정에서 기존 가상 네트워크를 제공 하 고 백 엔드 저장소에 대 한 새 Azure Blob 컨테이너를 만드는 경우 Microsoft 저장소에 대 한 네트워크에 서비스 끝점이 있어야 합니다. 이 끝점은 클러스터를 만들기 전에 존재 해야 합니다. 그렇지 않으면 만들기가 실패 합니다.

저장소 서비스 끝점은 나중에 저장소를 추가 하는 경우에도 Azure Blob storage를 사용 하는 모든 Avere vFXT for Azure 클러스터에 권장 됩니다.

> [!TIP]
>
>* 클러스터 만들기의 일부로 새 가상 네트워크를 만드는 경우이 단계를 건너뜁니다.
>* 클러스터를 만드는 동안 Blob 저장소를 만들지 않는 경우이 단계는 선택 사항입니다. 이 경우 나중에 Azure Blob을 사용 하기로 결정 한 경우 서비스 끝점을 만들 수 있습니다.

Azure Portal에서 저장소 서비스 끝점을 만듭니다.

1. 포털에서 가상 네트워크 목록을 엽니다.
1. 클러스터에 대 한 가상 네트워크를 선택 합니다.
1. 왼쪽 메뉴에서 **서비스 끝점** 을 클릭 합니다.
1. 위쪽에서 **추가**를 클릭합니다.
1. 서비스 ``Microsoft.Storage``를 선택 합니다.
1. 클러스터의 서브넷을 선택 합니다.
1. 아래쪽에서 **추가**를 클릭합니다.

   ![서비스 엔드포인트를 만드는 단계에 대한 주석이 있는 Azure Portal 스크린샷](media/avere-vfxt-service-endpoint.png)

## <a name="next-step-create-the-vfxt-cluster"></a>다음 단계: vFXT 클러스터 만들기

이러한 필수 조건이 완료되면 클러스터 자체 만들기로 이동할 수 있습니다. 지침은 [vFXT 클러스터 배포](avere-vfxt-deploy.md)를 참조하세요.
