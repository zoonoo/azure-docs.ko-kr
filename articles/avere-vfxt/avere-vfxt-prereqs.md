---
title: Avere vFXT 필수 조건 - Azure
description: Avere vFXT for Azure에 대한 필수 조건입니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 823bf50a54ff43fa95f7136c137e3d8f3303c3e0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669530"
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
* 클러스터 컨트롤러 노드에서 리소스 그룹과 가상 네트워크를 관리할 수 있도록 허용 
* 클러스터 컨트롤러에서 클러스터 노드를 만들고 수정할 수 있도록 허용 

vFXT를 만드는 사용자에게 소유자 액세스 권한을 부여하지 않으려면 다음 두 가지 해결 방법이 있습니다.

* 다음 조건이 충족되는 경우 리소스 그룹 소유자가 클러스터를 만들 수 있습니다.

  * 구독 소유자는 [Avere vFXT 소프트웨어 사용 약관에 동의](#accept-software-terms-in-advance)하고 [클러스터 노드 액세스 역할을 만들어야](avere-vfxt-deploy.md#create-the-cluster-node-access-role) 합니다.
  * 다음을 포함한 모든 Avere vFXT 리소스는 리소스 그룹 내에 배포해야 합니다.
    * 클러스터 컨트롤러
    * 클러스터 노드
    * Blob 저장소
    * 네트워크 요소
 
* 추가 액세스 역할이 만들어지고 사용자에게 미리 할당된 경우 소유자 권한이 없는 사용자가 vFXT 클러스터를 만들 수 있습니다. 그러나 이 역할은 이러한 사용자에게 중요한 권한을 부여합니다. 소유자 권한이 없는 사용자에게 클러스터를 만들 수 있는 권한을 부여하는 방법은 [이 문서](avere-vfxt-non-owner.md)에서 설명하고 있습니다.

## <a name="quota-for-the-vfxt-cluster"></a>vFXT 클러스터에 대한 할당량

다음 Azure 구성 요소에는 충분한 할당량이 있어야 합니다. 필요한 경우 [할당량 증가를 요청합니다](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> 여기에 나와 있는 가상 머신 및 SSD 구성 요소는 vFXT 클러스터 자체에 사용되는 것입니다. 계산 팜에 사용할 VM 및 SSD에 대한 할당량이 추가로 필요합니다.  워크플로를 실행할 지역에 대한 할당량을 사용하도록 설정되어 있는지 확인하세요.

|Azure 구성 요소|할당량|
|----------|-----------|
|가상 머신|D16s_v3 또는 E32s_v3 3개 이상|
|프리미엄 SSD 저장소|노드당 200GB OS 공간 및 1-4TB 캐시 공간 |
|저장소 계정(선택 사항) |v2|
|데이터 백 엔드 저장소(선택 사항) |새 LRS Blob 컨테이너 1개 |

## <a name="accept-software-terms-in-advance"></a>소프트웨어 사용 약관에 미리 동의

> [!NOTE] 
> 구독 소유자가 Avere vFXT 클러스터를 만드는 경우에는 이 단계가 필요하지 않습니다.

클러스터를 만들기 전에 먼저 Avere vFXT 소프트웨어의 서비스 약관에 동의해야 합니다. 구독 소유자가 아닌 경우, 구독 소유자가 사용 약관에 미리 동의하도록 합니다. 이 단계는 구독당 한 번만 수행하면 됩니다.

소프트웨어 사용 약관에 미리 동의하려면 다음을 수행합니다. 

1. Azure Portal에서 또는 <https://shell.azure.com>으로 이동하여 클라우드 셸을 엽니다. 구독 ID로 로그인합니다.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. 다음 명령을 실행하여 서비스 약관에 동의하고, Avere vFXT for Azure 소프트웨어 이미지에 프로그래밍 방식으로 액세스하도록 설정합니다. 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-node:latest
   ```

## <a name="next-step-create-the-vfxt-cluster"></a>다음 단계: vFXT 클러스터 만들기

이러한 필수 조건이 완료되면 클러스터 자체 만들기로 이동할 수 있습니다. 지침은 [vFXT 클러스터 배포](avere-vfxt-deploy.md)를 참조하세요.