---
title: Azure Stack에서 제품 구독 | Microsoft Docs
description: Azure Stack에서 제품에 대 한 구독 만들기
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: efemmano
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 6f91d011e9f15b80c2efd6ad1c6bbcd549786882
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55976846"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Azure Stack에서 제품에 대 한 구독 만들기

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

한 후 [제품을 만드는](azure-stack-create-offer.md), 사용자가 해당 제품 구독이 필요 합니다. 사용할 수 있습니다. 두 가지 방법으로 사용자를 구독 가져오기는 제품:

- 클라우드 운영자로 서 관리자 포털 내에서 사용자에 대 한 구독을 만들 수 있습니다. 공용 및 개인 제품에 대 한 구독을 만들 수 있습니다.
- 테 넌 트 사용자로 사용자 포털을 사용 하는 경우 공용 제품으로 구독할 수 있습니다.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>클라우드 운영자로 서 구독 만들기

클라우드 운영자는 사용자에 대 한 제품에 대 한 구독을 만들려면 관리 포털을 사용할 수 있습니다. 고유한 디렉터리 테 넌 트의 멤버에 대 한 구독을 만들 수 있습니다. 때 [다중 테 넌 트](azure-stack-enable-multitenancy.md) 은 활성화를 만들 수도 있습니다 사용자에 대 한 구독 추가 디렉터리 테 넌 트에 있습니다.

경우는 자신의 구독을 만들고 제품을 비공개로 만든 다음 테 넌 트의 구독 테 넌 트 않으려고 합니다. 외부 청구 또는 서비스 카탈로그 시스템을 사용 하 여 Azure Stack을 통합 하는 경우이 방법은 일반적입니다.

사용자에 대 한 구독을 만든 후 사용자 포털에 로그인 수 있으며 제품에 구독을 참조 하세요.  

### <a name="to-create-a-subscription-for-a-user"></a>사용자에 대 한 구독을 만들려면

1. 관리 포털에서 이동 **사용자 구독 합니다.**
2. **추가**를 선택합니다. 아래 **새 사용자 구독**, 다음 정보를 입력 합니다.  

   - **표시 이름** – 표시 되는 구독을 식별 하는 것에 대 한 이름을 합니다 *사용자 구독 이름*합니다.
   - **사용자** –이 구독에 대 한 사용 가능한 디렉터리 테 넌 트에서 사용자를 지정 합니다. 사용자 이름으로 나타납니다 *소유자*합니다.  사용자 이름 형식의 사용자 id 솔루션에 따라 달라 집니다. 예: 

     - **Azure AD:** `<user1>@<contoso.onmicrosoft.com>`

     - **AD FS:** `<user1>@<azurestack.local>`

   - **디렉터리 테 넌 트** – 여기서 사용자 계정이 속하는 테 넌 트 디렉터리를 선택 합니다. 다중 테 넌 트를 사용 하지 않은 경우에 사용자 로컬 디렉터리 테 넌 트 제품은입니다.

3. 선택 **제공**합니다. 아래 **제공**, 선택는 **제공** 이 구독에 대 한 합니다. 사용자에 대 한 구독을 만들 수 있기 때문에 선택 **개인** 내게 필요한 옵션 상태입니다.

4. 선택 **만들기** 구독을 만듭니다. 새 구독 아래에 나타납니다 **사용자 구독**합니다. 사용자가 사용자 포털에 로그인 하는 경우 구독 세부 정보를 볼 수 있습니다.

### <a name="to-make-an-add-on-plan-available"></a>추가 요금제를 사용할 수 있도록 하려면

클라우드 운영자는 언제 든 지 구독을 이전에 만든된 계획을 추가할 수 있습니다.:

1. 관리 포털에서 선택 **모든 서비스** 한 다음는 **관리 리소스** 범주를 선택한 **사용자 구독**합니다. 변경하려는 구독을 선택합니다.

2. 선택 **추가 기능** 선택한 후 **+ 추가**합니다.  

3. 아래 **계획 추가**, 추가 기능으로 원하는 계획을 선택 합니다.

## <a name="create-a-subscription-as-a-user"></a>사용자 구독 만들기

사용자를 찾아 디렉터리 테 넌 트 (조직)에 대 한 공용 제품 및 추가 요금제를 구독할 수 있는 사용자 포털 서명할 수 있습니다.

>[!NOTE]
>Azure Stack 환경에서 지 원하는 경우 [다중 테 넌 트](azure-stack-enable-multitenancy.md), 원격 디렉터리 테 넌 트에서 제공 서비스를 구독할 수도 있습니다.

### <a name="to-subscribe-to-an-offer"></a>제품 구독

1. [에 로그인](azure-stack-connect-azure-stack.md) 에 [Azure Stack 사용자 포털](https://portal.local.azurestack.external) 선택한 **구독**합니다.

   ![구독 가져오기](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. 아래 **subscription 얻기**에서 구독의 이름을 입력 **표시 이름**합니다. 선택 **제공** 아래에서 **제안 선택**, 제품을 선택 합니다. 선택 **만들기** 구독을 만듭니다.

   ![제품 만들기](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. 제품을 구독할 서비스를 새 구독에 포함을 확인 하려면 포털을 새로 고칩니다.

4. 만든 구독을 보려면 선택 **모든 서비스** 한 다음는 **일반** 범주 선택 **구독**합니다. 구독 세부 정보를 보려면 구독을 선택 합니다.  

### <a name="to-subscribe-to-an-add-on-plan"></a>추가 기능 계획을 구독할 수

제품에는 추가 요금제가 하는 경우에 언제 든 지 구독에 해당 계획을 추가할 수 있습니다.  

1. 사용자 포털에서 선택 **모든 서비스**합니다. 다음으로는 **일반** 범주를 선택한 **구독**, 한 다음 변경 하려는 구독을 선택 합니다. 사용 가능한 모든 추가 요금제가 없으면 **+ 추가 계획** 활성에 대 한 타일 이며 **추가 요금제**합니다. 

   하는 경우 **+ 추가 계획** 활성화 되지은 해당 구독과 연결 된 제품에 대 한 추가 요금제가 없습니다.

1. 선택 **+ 추가 계획** 또는 **추가 요금제** 바둑판식으로 배열 합니다. 아래 **추가 기능 계획**를 추가 하려는 계획을 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [가상 머신 프로비전](azure-stack-provision-vm.md)
