---
title: Azure Stack에 대 한 연결 된 azure 배포 결정 통합 시스템 | Microsoft Docs
description: 다중 노드 Azure Stack에 연결 된 배포에 대 한 결정을 계획 하는 배포를 확인 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 491bdf121729d690784324051ff701f3ed2d2b7a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243184"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Azure 연결 된 배포 계획 결정 for Azure Stack 통합 시스템
결정 한 다음 [하이브리드 클라우드 환경에 Azure Stack에서는 통합 하는 방법을](azure-stack-connection-models.md), Azure Stack 배포 관련 결정 사항을 완료 후 수 있습니다.

Azure에 연결 된 Azure Stack를 배포 하는 Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS)에 id 저장소에 대 한 것을 의미 합니다. 청구 모델 중 하나를 선택할 수도 있습니다.-수-종 또는 용량 기반 합니다. 연결 된 배포 되므로 기본 옵션을 고객 가치를 극대화 Azure Stack에서 Azure 및 Azure Stack 모두 포함 하는 하이브리드 클라우드 시나리오에 대 한 특히 수 있습니다. 

## <a name="choose-an-identity-store"></a>id 저장소를 선택 합니다.
연결 된 배포를 사용 하 여 Azure AD 또는 사용자 id 저장소에 대 한 AD FS 선택할 수 있습니다. 연결이 끊긴된 경우 배포를 인터넷 연결 되지 않은 수만 AD FS를 사용 합니다.

Id 저장소 선택 하는 테 넌 트 virtual machines (Vm)에서 관련이 없습니다. 테 넌 트 Vm 구성 방식에 따라 연결 하고자 하는 id 저장소를 선택할 수 있습니다. Azure AD에서 Windows Server Active Directory 도메인에 가입 된, workgroup, 등입니다. 이 Azure Stack identity provider 결정 관련이 없습니다. 

예를 들어, 테 넌 트 IaaS Azure Stack을 기반으로 Vm을 배포 하 고 회사 Active Directory 도메인에 가입 하 고 여기에서 계정을 사용 하도록 할 경우 여전히 이렇게 합니다. 여기에서 선택 하는 Azure AD id 저장소를 사용 하 여 해당 계정에 대 한 필요가 없습니다.

### <a name="azure-ad-identity-store"></a>Azure AD id 저장소
Id 저장소에 필요한 두 개의 Azure AD 계정에 대 한 Azure AD 사용 하는 경우: 전역 관리자 계정 및 청구 합니다. 이러한 계정은 동일한 계정 또는 다른 계정일 수 있습니다. 동일한 사용자 계정을 사용 하 여 간단 하 고 Azure 계정 수가 제한 해야 하는 경우에 유용할 수 있습니다, 있지만 두 개의 계정을 사용 하 여 비즈니스 요구 사항 제안 수 있습니다.:

1. **전역 관리자 계정** (연결 된 배포에만 필요). 이 Azure Active Directory에서 응용 프로그램 및 Azure Stack 인프라 서비스에 대 한 서비스 주체를 만드는 데 Azure 계정입니다. 이 계정에는 Azure Stack 시스템에서 배포 되는 디렉터리로 디렉터리 관리자 권한이 있어야 합니다. "클라우드 운영자" 되 게 Azure ad 전역 관리자 테 넌 트 및 사용 됩니다. 
    - 프로 비전 하 고 응용 프로그램 및 Azure Active Directory Graph API와 상호 작용 해야 하는 모든 Azure Stack 서비스에 대 한 서비스 주체를 위임 합니다. 
    - 서비스 관리자 계정. 이 구독의 기본 공급자 (나중에 변경할 수 있습니다)의 소유자입니다. 이 계정 사용 하 여 Azure Stack 관리 포털에 로그인 할 수 하 고 제품 및 계획 만들기, 할당량을 설정 및 Azure Stack에서 다른 관리 기능을 수행 하는 데 사용할 수 있습니다.
2. **대금 청구 계정** (연결 및 끊어진 배포 모두에 대 한 필수). Azure Stack 통합 시스템 및 Azure 상거래 백 엔드의 청구 관계를 설정 하려면 Azure 계정에이 사용 됩니다. Azure Stack 요금이 청구 되는 계정입니다. 이 계정은 marketplace 및 다른 하이브리드 시나리오에서 항목을 제품에 사용 됩니다. 

### <a name="ad-fs-identity-store"></a>AD FS id 저장소
서비스 관리자 계정에 회사 Active Directory와 같은 사용자 고유의 id 저장소를 사용 하려는 경우이 옵션을 선택 합니다.  

## <a name="choose-a-billing-model"></a>청구 모델을 선택 합니다.
선택할 수 있습니다 **-수-종** 또는 **용량** 청구 모델. 청구 모델 배포 수-종 사용량을 보고 연결을 통해 Azure 30 일 마다 한 번 이상 있어야 합니다. 따라서-수-종 청구 모델은 연결 된 배포에 사용할만 합니다.  

### <a name="pay-as-you-use"></a>지불으로-사용
수-종 청구 모델을 사용 하 여 사용량을 Azure 구독에 요금이 청구 됩니다. Azure Stack 서비스를 사용할 때만 지불 합니다. Azure 구독 및 해당 구독과 연결 된 계정 ID를 해야 사용 하도록 결정 하는 모델의 경우 (예를 들어 serviceadmin@contoso.onmicrosoft.com). EA, CSP 및 CSL 구독이 지원 됩니다. 하는 동안 구성 된 사용 현황 보고 [Azure Stack 등록](azure-stack-registration.md)합니다.

> [!NOTE]
> 대부분의 경우에서 기업 고객은 EA 구독 하 고 서비스 공급자는 CSP 또는 CSL 구독을 사용 합니다.

CSP 구독을 사용 하려면 사용 하는 CSP 구독을 식별 하는 아래 표를 검토 하려는 경우 올바른 접근 방식으로 종속 정확한 CSP 시나리오:

|시나리오|도메인 및 구독 옵션|
|-----|-----|
|**직접 CSP 파트너** 요소나 **간접 CSP 공급자**, Azure Stack 작동 하 고|CSL (공통 서비스 계층) 구독을 사용 합니다.<br>     또는<br>파트너 센터에서 설명 하는 이름을 사용 하 여 Azure AD 테 넌 트를 만듭니다. 예를 들어 &lt;조직 > 연결 된 Azure CSP 구독을 사용 하 여 CSPAdmin입니다.|
|한 **간접 CSP 재판매인**, Azure Stack 작동 하 고|연결 된 파트너 센터를 사용 하 여 Azure CSP 구독을 사용 하 여 조직에 대 한 Azure AD 테 넌를 만들려는 간접 CSP 공급자에 게 문의 하십시오.|

### <a name="capacity-based-billing"></a>용량 기반된 청구
용량 요금 청구 모델을 사용 하려는 경우 Azure 스택 용량 계획 SKU를 기반으로 시스템의 용량을 구입 해야 합니다. 올바른 수량을 구매 하 여 Azure Stack에서 실제 코어의 수를 해야 합니다. 

용량 요금 청구는 EA (기업 계약)를 필요한 등록에 대 한 Azure 구독. 이유는 Azure 구독이 필요는 Marketplace에서 등록 항목의 가용성 집합입니다. Azure Stack 사용에 대 한 구독이 사용 되지 않습니다.

## <a name="learn-more"></a>자세한 정보
- 사용 사례, 구매, 파트너 및 OEM 하드웨어 공급 업체에 대 한 내용은 참조는 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 제품 페이지입니다.
- 통합된 시스템, Azure Stack에 대 한 로드맵 및 지역 가용성에 대 한 자세한 백서를 참조 합니다. [Azure Stack: Azure의 확장](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)합니다. 
- Microsoft Azure Stack 패키징 및 가격 책정에 대해 자세히 알아보려면 [는.pdf 다운로드](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)합니다. 

## <a name="next-steps"></a>다음 단계
[데이터 센터 네트워크 통합](azure-stack-network.md)