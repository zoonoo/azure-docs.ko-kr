---
title: Azure Stack에서 제품 위임 | Microsoft Docs
description: 제품 만들기 및 사용자를 등록 해야 다른 사용자를 배치 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 1b22d465df12d313e05d3fd93437b461ef939ee7
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242487"
---
# <a name="delegate-offers-in-azure-stack"></a>Azure Stack에서 제안 위임

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 운영자 담당 사용자를 등록 하 고 구독을 만들어 다른 사용자를 배치 하려는 경우가 많습니다. 예를 들어, 서비스 공급자 인 경우 재판매인 고객을 등록 하 고 사용자를 대신해 관리에 좋습니다. 또는 다른 IT 직원에 게 사용자 등록 위임 하려는 엔터프라이즈의 중앙 IT 그룹의 일부인 경우.

위임 쉽게 연결 하 고 다음 그림에 표시 된 대로 직접 할 수 있는 것 보다 많은 사용자를 관리 합니다.

![위임 수준](media/azure-stack-delegated-provider/image1.png)

위임을 사용한 위임 된 공급자 제품 (위임 된 제안)를 관리 하 고 최종 고객 시스템 관리자에서 해당 제품의 개입 없이도 구독을 가져옵니다.

## <a name="understand-delegation-roles-and-steps"></a>위임 역할 및 단계를 이해 합니다.

### <a name="delegation-roles"></a>역할 위임

다음 역할은 일부 위임입니다.

* 합니다 *Azure Stack 운영자* Azure Stack 인프라를 관리 하 고 제안 하는 템플릿을 만듭니다. 연산자에 다른 사용자가 테 넌 트에 제공 서비스를 위임 합니다.

* 위임 된 Azure Stack 운영자는 사용자에 게 *소유자* 또는 *참가자* 구독에 권한 호출 *공급자 위임*합니다. 다른 Azure Active Directory (Azure AD) 테 넌 트와 같은 다른 조직에 속해 있습니다.

* *사용자가* 제품에 등록 하 고 해당 워크 로드 관리, 데이터를 저장 하는 Vm 만들기에 대 한 사용 등입니다.

### <a name="delegation-steps"></a>위임 단계

두 가지 기본 단계 위임을 설정 하려면:

1. **위임 된 공급자 구독을 만드는**: 사용자 구독 서비스만 포함 된 제품을 구독 합니다. 이 제품을 구독 하는 사용자 다른 사용자에 게 위임 된 제안이 해당 제품에 대해 서명 하 여 확장한 다음 수 있습니다.

2. **위임 된 공급자에 게 제품을 위임할**: 위임 된 공급자를 구독을 만드는 또는 사용자에 게 제품을 확장할 수 있습니다. 이제는 위임 된 공급자 제품을 사용 하 고 다른 사용자에 게 제공할 수 있습니다.

다음 그림에는 위임을 설정 단계를 보여 줍니다.

![위임 된 공급자를 만들고 사용자를 등록 하기](media/azure-stack-delegated-provider/image2.png)

#### <a name="delegated-provider-requirements"></a>위임 된 공급자 요구 사항

위임 된 공급자를 작동 하려면 사용자는 기본 공급자를 사용 하 여 관계를 구독을 만들어 설정 합니다. 이 구독은 기본 공급자를 대신 하 여 위임 된 제안을 제공할 수 있는 권한이 있는 것으로 위임 된 공급자를 식별 합니다.

이 관계 설정 되 면 Azure Stack 운영자 위임 된 공급자에 게 제품을 위임할 수 있습니다. 위임 된 공급자 수 제품 사용, 이름을 바꾸거나 (그러나 내용을 변경 하지) 고객에 게 제공 합니다.

## <a name="delegation-walkthrough"></a>위임 연습

다음 섹션에서는, 위임 된 공급자를 설정 하 고, 제품을 위임 하 고, 사용자 위임 된 제안에 등록할 수 있는지 확인 하는 실전 연습을 제공 합니다.

### <a name="set-up-roles"></a>역할 설정

이 연습을 사용 하려면 Azure Stack 연산자 계정 외에도 두 Azure AD 계정이 필요 합니다. 이러한 두 계정에 없는 경우 만들어야 합니다. 계정을 Azure AD 사용자에 게 속할 수 있으며 위임 된 공급자 및 사용자 라고 합니다.

| **역할** | **조직 권한** |
| --- | --- |
| 위임 된 공급자 |사용자 |
| 사용자 |사용자 |

### <a name="identify-the-delegated-provider"></a>위임 된 공급자를 식별 합니다.

1. Azure Stack 운영자로 서 관리자 포털에 로그인 합니다.

1. 사용자가 위임 된 공급자 될 수 있는 제품을 만들려면:

   a.  [계획을 만들려면](azure-stack-create-plan.md)합니다.
       이 계획은 구독 서비스만을 포함 되어야 합니다. 이 문서에서는 이라는 계획 **PlanForDelegation** 예입니다.

   b.  [제품 만들기](azure-stack-create-offer.md) 이 계획을 기반으로 합니다. 이 문서에서는 명명 된 제품 **OfferToDP** 예입니다.

   다.  위임 된 공급자를 선택 하 여이 제품에 구독자로 추가 **구독**, 한 다음 **추가**, 한 다음 **새 테 넌 트 구독**합니다.

   ![구독자로 위임 된 공급자를 추가 합니다.](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > 있는 경우 모든 Azure Stack 제품을 사용 하 여 공용 및 하도록 사용자가 제품을 만드는 옵션을 등록, 또는 개인 유지 하 고 등록을 관리 하는 Azure Stack 연산자 수 있도록 합니다. 위임 된 공급자는 일반적으로 작은 그룹. 대부분의 경우에서 합리적이 제품은 개인 유지 하므로, 참석할 사람을 제어 하려고 합니다.

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>Azure Stack 운영자 위임된 된 제안을 만듭니다.

다음 단계를 계획 및 위임 하려고 하 고 사용자가 사용 하는 제품을 만드는 것입니다. 위임 된 공급자는 계획 및 할당량 여기에 변경 될 수 없으므로 그 후 사용자가 원하는 대로 정확 하 게이 제품을 정의 하는 것이 좋습니다.

1. Azure Stack 운영자 [계획을 만듭니다](azure-stack-create-plan.md) 하 고 [제품](azure-stack-create-offer.md) 계획에 따라 합니다. 이 문서에서는 명명 된 제품 **DelegatedOffer** 예입니다.

   > [!NOTE]
   > 이 제품은 공용 이어야 없지만 공용 만들 수 있습니다. 그러나 대부분의 경우에만 원하는 제품에 액세스할 수 있도록 위임 된 공급자입니다. 다음 단계에 설명 된 대로 개인 제품 위임, 후 위임 된 공급자에 액세스할 수 있습니다.

2. 제품을 위임 합니다. 로 이동 **DelegatedOffer**합니다. 아래 **설정을**를 선택 **위임 된 공급자**을 선택한 후 **추가**합니다.

3. 드롭다운 목록에서 위임 된 공급자에 대 한 구독을 선택 하 고 선택한 **대리자**합니다.

   ![위임 된 공급자 추가](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>위임 된 공급자 제품을 사용자 지정

위임 된 공급자로 사용자 포털에 로그인 하 고 위임된 된 제안을 템플릿으로 사용 하 여 새 제품을 만듭니다.

1. 선택 **+ 리소스 만들기**, 한 다음 **테 넌 트 제공 + 계획**을 선택한 후 **제공**합니다.

    ![새 제안 만들기](media/azure-stack-delegated-provider/image5.png)

2. 제품에 이름을 할당 합니다. 이 예제에서는 **ResellerOffer**합니다. 기본을 선택한 후에 위임된 된 제안을 선택 **만들기**합니다.

   ![이름을 할당합니다](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >위임 된 공급자에 위임 된 제안만 선택할 수 있는지를 이해 하는 것이 반드시 합니다. 해당 제품을 변경할 수는 없습니다. Azure Stack 연산자만 해당 계획 및 할당량 변경 예를 들어, 이러한 제품을 변경할 수 있습니다. 위임 된 공급자는 기본 계획 및 추가 요금제에서 제공 하는 서비스를 생성 하지 않습니다.

3. 위임 된 공급자 가능 이러한 제품 자체 포털을 통해 공용 URL입니다. 제품을 공개 하려면를 선택 **찾아보기**를 차례로 **제공**합니다. 제품을 선택한 후 **상태 변경**합니다.

4. 공용 위임 된 제안을 위임 된 포털을 통해서만 표시 됩니다. 찾아서이 URL을 변경

    a.  선택 **찾아보기**, 다음 **모든 서비스**를 선택한 후는 **일반** 범주를 선택한 **구독**합니다. 위임 된 공급자 구독 선택 예를 들어 **DPSubscription**, 한 다음 **속성**합니다.

    b.  포털 복사 메모장과 같은 별도 위치에 대 한 URL입니다.

    ![위임 된 공급자 구독 선택](media/azure-stack-delegated-provider/dpportaluri.png)  

   위임된 된 제안을 위임 된 공급자를 만들기를 완료 했습니다. 위임 된 공급자 로그 아웃 하 고 브라우저 창을 닫습니다.

### <a name="sign-up-for-the-offer"></a>제품에 등록

1. 위임 된 포털로 이동 하는 새 브라우저 창에서 이전 단계에서 저장 하는 URL입니다. 사용자 포털에 로그인 합니다.

   >[!NOTE]
   >위임 된 포털을 사용 하지 않는 경우에 위임 된 제안이 표시 되지 않습니다.

1. 대시보드에서 다음을 선택 **구독**합니다. 위임 된 제안 위임 된 공급자에 의해 생성 된 사용자에 게 표시 되는 볼 수 있습니다.

   ![보기 및 제안 선택](media/azure-stack-delegated-provider/image8.png)

제품 위임 프로세스를 마쳤습니다. 이제 사용자 여 등록할 수 있습니다이 제품에 대 한 구독을 시작 합니다.

## <a name="move-subscriptions-between-delegated-providers"></a>위임 된 공급자 구독 이동

필요한 경우 구독을 동일한 디렉터리 테 넌 트에 속해 있는 기존 또는 새 위임 된 공급자 구독 간에 이동할 수 있습니다. PowerShell cmdlet을 사용 하 여 이렇게 [이동 AzsSubscription](/powershell/module/azs.subscriptions.admin)합니다.

이 경우에 유용 합니다.

* 온 보 딩 위임 된 공급자 역할에 대해 수행 하는 새 팀 멤버가 하려는 기본 공급자 구독에서 이전에 만든이 팀 멤버 사용자 구독에 할당 합니다.
* 동일한 디렉터리 테 넌 트 (Azure Active Directory)에 여러 위임 된 공급자 구독 있고 사용자 구독 간에 이동 해야 합니다. 이 시나리오는 팀 멤버가 팀 간에 이동 하 고 새 팀에 할당 해야 하는 해당 구독에는 사례를 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [VM을 프로 비전](azure-stack-provision-vm.md)