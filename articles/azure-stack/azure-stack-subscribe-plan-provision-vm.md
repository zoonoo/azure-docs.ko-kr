---
title: Azure 스택에 제품 구독 | Microsoft Docs
description: Azure 스택에서 제안에 대 한 구독 만들기
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/11/2018
ms.author: brenduns
ms.openlocfilehash: 9153649774a67533649fb62da83a3f50abd592da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295213"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Azure 스택에서 제안에 대 한 구독 만들기

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

후 [제안을 만들](azure-stack-create-offer.md), 사용자를 사용할 수 있습니다 해당 제품에 대 한 구독에 필요 합니다. 두 가지 방법으로 사용자가 구독 가져오기는 오퍼:

- 클라우드 연산자로 관리자 포털 내에서 사용자에 대 한 구독을 만들 수 있습니다. 공용 및 개인 제품에 대 한 구독을 만들 수 있습니다.
- 테 넌 트 사용자로 사용자 포털을 사용 하 여 때 공용 제안에 가입할 수 있습니다.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>클라우드 연산자로 구독 만들기

클라우드 운영자는 사용자에 대 한 제안에 구독을 만들려면 관리 포털을 사용할 수 있습니다.  자신의 디렉터리 테 넌 트의 멤버에 대 한 구독을 만들 수 있습니다.  때 [다중 테 넌 트](azure-stack-enable-multitenancy.md) 가 활성화 만들 수도 있습니다 사용자에 대 한 구독 추가 디렉터리 테 넌 트에 있습니다.

경우을 자신의 구독을 만들고 해당 제공은 private, 테 넌 트에 대 한 구독을 만들려면 다음 테 넌 트 않으려고 합니다. Azure 스택 외부 청구 또는 카탈로그 시스템 서비스와 통합 하는 경우이 방법이 일반적입니다.

사용자에 대 한 구독을 만든 후에 사용자 포털에 로그인 수 있으며 제품에 구독 하는 것을 참조 하십시오.  

### <a name="to-create-a-subscription-for-a-user"></a>사용자에 대 한 구독을 만들려면

1. 관리 포털에서로 이동 **사용자 구독 합니다.**
2. **추가**를 선택합니다. 아래 **새 사용자 구독**, 다음 정보를 입력 합니다.  

   - **표시 이름** – 이름으로 표시 되는 구독을 식별 하는 데는 *사용자 구독 이름*합니다.
   - **사용자** –이 구독에 대 한 사용 가능한 디렉터리 테 넌 트의 사용자를 지정 합니다. 사용자 이름으로 표시 *소유자*합니다.  사용자 이름의 형식은 id 솔루션에 따라 달라 집니다. 예: 

     - **Azure AD:***&lt;user1 > @&lt;contoso.onmicrosoft.com >* 

     - **AD FS:**  *&lt;user1>@&lt;azurestack.local>*

   - **디렉터리 테 넌 트** – 디렉터리 테 넌 트 사용자 계정이 속하는 위치를 선택 합니다. 다중 테 넌 트를 활성화 하지 않은 경우 로컬 디렉터리 테 넌 트만 ´ ù.

3. 선택 **제공**합니다. 아래 **제공**, 선택는 **제공** 이 구독에 대 한 합니다. 사용자에 대 한 구독을 만드는 있기 때문에 선택 **개인** 내게 필요한 옵션 상태입니다.

4. 선택 **만들기** 구독을 만듭니다. 새 구독 표시 **사용자 구독**합니다. 사용자가 사용자 포털에 로그인 하는 경우 구독 세부 정보를 볼 수 있습니다.

### <a name="to-make-an-add-on-plan-available"></a>추가 기능 계획을 사용할 수 있도록 하려면

클라우드 운영자는 언제 든 지 이전에 만든된 구독에 추가 기능 계획을 추가할 수 있습니다.

1. 관리 포털에서 선택 **더 서비스** > **사용자 구독**합니다. 변경하려는 구독을 선택합니다.

2. 선택 **추가 기능** 선택한 후 **+ 추가**합니다.  

3. 아래 **계획 추가**, 추가 기능으로 계획을 선택 합니다.

## <a name="create-a-subscription-as-a-user"></a>사용자로 구독 만들기

사용자를 찾고 여 디렉터리 테 넌 트 (조직)에 대 한 공용 혜택 및 추가 기능이 계획에 등록 하는 사용자 포털로 서명할 수 있습니다.

>[!NOTE]
>Azure 스택 환경을 지 원하는 경우 [다중 테 넌 트](azure-stack-enable-multitenancy.md) 원격 디렉터리 테 넌 트에서 제공을 구독할 수도 있습니다.

### <a name="to-subscribe-to-an-offer"></a>제공 하는 서비스를 구독 하려면

1. [로그인](azure-stack-connect-azure-stack.md) Azure 스택 사용자 포털에 (https://portal.local.azurestack.external) 선택 **구독**합니다.

   ![구독 가져오기](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. 아래 **구독**에서 구독의 이름을 입력 하십시오. **표시 이름**합니다. 선택 **제공** 고 **제안을 선택**를 제공 하는 서비스를 선택 합니다. 선택 **만들기** 구독을 만듭니다.

   ![제품 만들기](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. 제공 하는 서비스를 구독 한 후 새 구독의 일부인 서비스를 확인 하기 위해 포털을 새로 고칩니다.
4. 만든 구독을 보려면 선택 **더 많은 서비스** 선택한 후 **구독**합니다. 구독 세부 정보를 보려면 구독을 선택 합니다.  

### <a name="to-subscribe-to-an-add-on-plan"></a>추가 기능 계획을 구독 하려면

제공 하는 추가 기능이 계획에 있는 경우 언제 든 지 구독에 해당 하는 계획을 추가할 수 있습니다.  

1. 사용자 포털에서 선택 **더 많은 서비스** > **구독**, 한 다음 변경 구독을 선택 합니다. 를 사용할 수 있는 추가 기능 계획이 있는 경우 **+ 계획 추가** 활성화 되어 있으면 다음에 대 한 타일 **추가 기능이 계획**합니다.

   >[!NOTE]
   >경우 **+ 계획 추가** , 활성화 되어 있지 않으면 없습니다. 해당 구독과 연결 된 제품에 대 한 추가 기능 계획 합니다.

1. 선택 **+ 계획 추가** 또는 **추가 기능이 계획** 바둑판식으로 배열입니다. 아래 **추가 기능이 계획**을 추가할 계획을 선택 합니다.

## <a name="next-steps"></a>다음 단계

[가상 머신 프로비전](azure-stack-provision-vm.md)
