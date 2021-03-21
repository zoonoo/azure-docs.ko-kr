---
title: Connect Health를 사용 하 여 Azure AD의 AD FS 로그인 | Microsoft Docs
description: 이 문서에서는 Azure AD Connect Health 로그인 보고서와 AD FS 로그인을 통합 하는 방법에 대해 설명 합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/16/2021
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74769feba1d717a2f1a72d311f85bdfbeac7b7db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574792"
---
# <a name="ad-fs-sign-ins-in-azure-ad-with-connect-health---preview"></a>Connect Health를 사용 하 여 Azure AD의 AD FS 로그인-미리 보기

이제 Connect Health를 사용 하 여 AD FS 로그인을 Azure Active Directory 로그인 보고서에 통합할 수 있습니다. [AZURE ad 로그인 보고서](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-all-sign-ins#:~:text=Interactive%20user%20sign-ins%20are%20sign-ins%20where%20a%20user,to%20Azure%20AD%20or%20to%20a%20helper%20app.) 보고서에는 사용자, 응용 프로그램 및 관리 되는 리소스가 azure ad에 로그인 하 고 리소스에 액세스 하는 경우에 대 한 정보가 포함 됩니다. 

AD FS 에이전트에 대 한 Connect Health는 서버 버전에 따라 AD FS의 여러 이벤트 Id를 상호 연결 하 여 요청이 실패 하는 경우 요청 및 오류 정보에 대 한 정보를 제공 합니다. 이 정보는 Azure AD 로그인 보고서 스키마와 상호 관련 되어 있으며 Azure AD Sign-In 보고서 UX에 표시 됩니다. 보고서와 함께 새 Log Analytics 스트림은 AD FS 데이터와 새 Azure Monitor 통합 문서 템플릿과 함께 사용할 수 있습니다. 계정 잠금, 잘못 된 암호 시도, 예기치 않은 로그인 시도 급증 등의 AD FS 시나리오에 대 한 심층 분석을 위해 템플릿을 사용 하 고 수정할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* Azure AD Connect Health 설치 되 고 최신 버전으로 업그레이드 AD FS.
* Azure AD 로그인을 볼 수 있는 전역 관리자 또는 보고서 읽기 권한자 역할

## <a name="what-data-is-displayed-in-the-report"></a>보고서에 표시 되는 데이터
사용 가능한 데이터는 Azure AD 로그인에 사용할 수 있는 동일한 데이터를 미러링합니다. 로그인 유형 (Azure AD 또는 AD FS)에 따라 정보를 포함 하는 5 개의 탭을 사용할 수 있습니다. 연결 상태는 서버 버전에 따라 AD FS에서 이벤트를 상호 연결 하 여 AD FS 스키마와 일치 시킵니다. 



#### <a name="user-sign-ins"></a>사용자 로그인 
로그인 블레이드의 각 탭은 아래 기본값을 보여 줍니다.
* 로그인 날짜
* 요청 ID
* 사용자 이름 또는 사용자 ID
* 로그인 상태
* 로그인에 사용 되는 장치의 IP 주소
* Sign-In 식별자

#### <a name="authentication-method-information"></a>인증 방법 정보
다음 값이 인증 탭에 표시 될 수 있습니다. 인증 방법은 AD FS 감사 로그에서 가져옵니다.

|인증 방법|Description|
|-----|-----|
|양식|사용자 이름/암호 인증|
|Windows|Windows 통합 인증|
|인증서|스마트 카드/s s o 인증서를 사용한 인증|
|WindowsHelloForBusiness|이 필드는 비즈니스용 Windows Hello를 사용 하는 인증을 위한 것입니다. (Microsoft Passport 인증)|
|디바이스 | 장치 인증을 인트라넷/엑스트라넷의 "기본" 인증으로 선택 하 고 장치 인증을 수행 하는 경우 표시 됩니다.  이 시나리오에서는 별도의 사용자 인증을 사용 하지 않습니다.| 
|페더레이션|AD FS에서 인증을 수행 하지 않았지만 타사 id 공급자에 게 보냈습니다.|
|SSO |Single sign-on 토큰을 사용한 경우에는이 필드가 표시 됩니다. SSO에 MFA가 있는 경우 다단계으로 표시 됩니다.|
|다단계|Single Sign-On 토큰에 MFA가 있고 인증에 사용 된 경우이 필드는 다단계로 표시 됩니다.|
|Azure MFA|Azure MFA는 AD FS에서 추가 인증 공급자로 선택 되며 인증에 사용 되었습니다.|
|ADFSExternalAuthenticationProvider|이 필드는 타사 인증 공급자를 등록 하 여 인증에 사용한 경우에 해당 합니다.|


#### <a name="ad-fs-additional-details"></a>추가 세부 정보 AD FS
AD FS 로그인에 사용할 수 있는 세부 정보는 다음과 같습니다.
* 서버 이름
* IP 체인
* 프로토콜

### <a name="enabling-log-analytics-and-azure-monitor"></a>Log Analytics 및 Azure Monitor 사용
Log Analytics은 AD FS 로그인에 대해 사용 하도록 설정할 수 있으며 센티널과 같은 다른 모든 Log Analytics 통합 구성 요소와 함께 사용할 수 있습니다.

> [!NOTE] 
> AD FS 로그인은 조직에서 AD FS 로그인의 양에 따라 Log Analytics 비용이 크게 증가할 수 있습니다. Log Analytics를 사용 하거나 사용 하지 않도록 설정 하려면 해당 스트림의 확인란을 선택 합니다.

기능에 대 한 Log Analytics를 사용 하도록 설정 하려면 Log Analytics 블레이드로 이동 하 고 "ADFSSignIns" 스트림을 선택 합니다. 이 옵션을 선택 하면 AD FS 로그인이 Log Analytics으로 전달 될 수 있습니다.

업데이트 된 Azure Monitor 통합 문서 템플릿에 액세스 하려면 "Azure Monitor 템플릿"으로 이동 하 여 "로그인" 통합 문서를 선택 합니다.
통합 문서에 대 한 자세한 내용은 [Azure Monitor 통합 문서](https://aka.ms/adfssigninspreview)를 참조 하세요.




### <a name="frequently-asked-questions"></a>질문과 대답
***표시 될 수 있는 로그인의 유형은 무엇입니까?***
로그인 보고서는 O Auth, WS-급지됨, SAML 및 WS-Trust 프로토콜을 통한 로그인을 지원 합니다. 

***로그인 보고서에는 다양 한 유형의 로그인이 어떻게 표시 되나요?***
원활한 SSO 로그인이 수행 되는 경우 한 상관 관계 ID를 가진 로그인에 대 한 행이 하나씩 있습니다.
단일 단계 인증을 수행 하는 경우 두 개의 행은 동일한 상관 관계 ID를 사용 하지만 두 가지 인증 방법 (즉, 양식, SSO)으로 채워집니다.
Multi-factor Authentication의 경우 공유 상관 관계 ID와 세 개의 해당 인증 방법 (예: Forms, AzureMFA, 다단계)이 포함 된 3 개의 행이 있습니다. 이 예제의 다단계에서는이 경우에는 SSO에 MFA가 있음을 보여 줍니다.

***보고서에서 볼 수 있는 오류는 무엇 인가요?***
Sign-In 보고서 및 설명에 채워지는 AD FS 관련 오류에 대 한 전체 목록은 [AD FS 도움말 오류 코드 참조를 참조](https://adfshelp.microsoft.com/References/ConnectHealthErrorCodeReference) 하세요.

***로그인의 "사용자" 섹션에 "00000000-0000-0000-0000-000000000000"이 표시 됩니다. 그게 무슨 뜻인가요?***
로그인이 실패 하 고 시도 된 UPN이 기존 UPN과 일치 하지 않으면 "User", "Username" 및 "User ID" 필드는 "00000000-0000-0000-0000-000000000000"이 되며 "로그인 식별자"는 사용자가 입력 한 시도 값으로 채워집니다. 이러한 경우 로그인을 시도 하는 사용자가 존재 하지 않습니다.

***온-프레미스 이벤트를 Azure AD 로그인 보고서와 상호 연결 하려면 어떻게 해야 하나요?***
AD FS에 대 한 Azure AD Connect Health 에이전트는 서버 버전에 따라 AD FS에서 이벤트 Id를 연관 시킵니다. 이 이벤트는 AD FS 서버의 보안 로그에서 사용할 수 있습니다. 

***일부 AD FS 로그인에 대 한 응용 프로그램 ID/이름에 NotSet 또는 NotApplicable가 표시 되는 이유는 무엇 인가요?***
AD FS Sign-In 보고서는 OAuth 로그인에 대 한 응용 프로그램 ID 필드에 OAuth Id를 표시 합니다. WS 급지됨 WS-Trust 로그인 시나리오에서는 응용 프로그램 ID가 NotSet 또는 NotApplicable이 고 리소스 ID 및 신뢰 당사자 식별자가 리소스 ID 필드에 표시 됩니다.

***미리 보기에서 보고서에 대해 알려진 문제가 더 있나요?***
이 보고서에는 알려진 문제가 있습니다. 여기에는 "기본 정보" 탭의 "인증 요구 사항" 필드가 로그인에 관계 없이 AD FS 로그인에 대 한 단일 요소 인증 값으로 채워집니다. 또한 인증 세부 정보 탭에는 요구 사항 필드 아래에 "기본 또는 보조"가 표시 되 고, 기본 또는 보조 인증 유형을 구분 하기 위해 진행 중인 수정이 표시 됩니다.


## <a name="related-links"></a>관련 링크
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health Agent 설치](how-to-connect-health-agent-install.md)
* [위험한 IP 보고서](how-to-connect-health-adfs-risky-ip.md)





