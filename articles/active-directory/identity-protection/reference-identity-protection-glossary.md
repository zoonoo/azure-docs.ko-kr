---
title: Azure Active Directory ID 보호 용어집
description: Azure Active Directory ID 보호 용어집
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a3e2df956aaa4f9fd0af83dd2a18e04d731c714
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232355"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory ID 보호 용어집

### <a name="at-risk-user"></a>위험(사용자)
하나 이상의 활성 위험 검색이 있는 사용자입니다. 

### <a name="atypical-sign-in-location"></a>비정상적인 로그인 위치
특정 사용자, 비슷한 사용자 또는 테넌트에 일반적이지 않은 지리적 위치에서 시도하는 로그인입니다.

### <a name="azure-ad-identity-protection"></a>Azure AD ID 보호
조직의 ID에 영향을 미치는 위험 검색 및 잠재적 취약점에 대한 통합된 보기를 제공하는 Azure Active Directory의 보안 모듈입니다.

### <a name="conditional-access"></a>조건부 액세스
리소스에 대한 액세스를 보호하는 정책입니다. 조건부 액세스 규칙은 Azure Active Directory에 저장되며 리소스에 대한 액세스 권한을 부여하기 전에 Azure AD에서 평가합니다.  예제 규칙은 사용자 위치, 디바이스 상태 또는 사용자 인증 방법에 따라 액세스를 제한하게 됩니다.

### <a name="credentials"></a>자격 증명
로컬 및 네트워크 리소스에 대한 액세스 권한을 얻는 데 사용되는 ID 및 ID의 증명을 포함하는 정보입니다. 자격 증명의 예는 사용자 이름 및 암호, 스마트 카드 및 인증서가 있습니다.

### <a name="event"></a>행사
Azure Active Directory에서 작업의 기록입니다.

### <a name="false-positive-risk-detection"></a>거짓 긍정(위험 감지)
ID 보호 사용자가 수동으로 설정한 위험 검색 상태는 위험 검색을 조사했으며 위험 검색으로 잘못 플래그가 지정되었음을 나타냅니다.

### <a name="identity"></a>Identity
암호 또는 인증서와 같은 조건에 따라 인증이라는 방법으로 확인되어야 하는 개인 및 엔터티입니다.

### <a name="identity-risk-detection"></a>ID 위험 탐지
ID 보호에 의해 비정상적인 것으로 플래그가 지정되고 ID가 손상되었음을 나타낼 수 있는 Azure AD 이벤트입니다.

### <a name="ignored-risk-detection"></a>무시됨(위험 감지)
ID 보호 사용자가 수동으로 설정한 위험 검색 상태는 수정 작업을 수행하지 않고 위험 검색이 닫혀 있음을 나타냅니다.

### <a name="impossible-travel-from-atypical-locations"></a>비정상적 위치에서 불가능한 이동
동일한 사용자에 대한 두 개의 로그인이 감지되고, 그 중 하나 이상이 비정형 로그인 위치에서 온 위치, 로그인 사이의 시간이 물리적으로 이들 사이를 이동하는 데 걸리는 최소 시간보다 짧은 경우 위험 감지가 트리거됩니다. 위치.  

### <a name="investigation"></a>조사
위험 탐지와 관련된 활동, 로그 및 기타 관련 정보를 검토하여 수정 또는 완화 단계가 필요한지 여부를 결정하고, ID가 손상되었는지 및 어떻게 손상되었는지 이해합니다. ID가 사용되었습니다.

### <a name="leaked-credentials"></a>유출된 자격 증명
현재 사용자 자격 증명(사용자 이름 및 암호)이 연구원에 의해 다크 웹에 공개적으로 게시될 때 트리거되는 위험 감지입니다.

### <a name="mitigation"></a>완화 방법
ID 또는 디바이스를 안전한 상태로 복원하지 않고 공격자의 능력을 제한하거나 제거하여 공격에 노출된 ID 또는 디바이스를 악용하는 작업입니다. 완화는 ID 또는 장치와 관련된 이전 위험 검색을 해결하지 못합니다.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
인증서 등 사용자가 가진 정보를 포함하는 두 개 이상의 인증 방법이 필요한 인증 방법입니다. 사용자 이름, 암호 또는 암호 문구 등 사용자가 아는 정보 혹은 지문 등 물리적 특성 및 개인 서명 등 개인 특성일 수 있습니다.

### <a name="offline-detection"></a>오프라인 검색
잘못된 부분을 감지하고 이미 발생한 이벤트에 대한 사후 로그인 시도와 같은 이벤트의 위험을 평가합니다.

### <a name="policy-condition"></a>정책 조건
엔터티(그룹, 사용자, 앱, 디바이스 플랫폼, 디바이스 상태, IP 범위, 클라이언트 형식)를 정의하는 보안 정책의 일부는 정책에 포함되었거나 제외됩니다.

### <a name="policy-rule"></a>정책 규칙
정책을 트리거할 상황과 정책이 트리거될 때 수행되는 작업을 설명하는 보안 정책의 일부입니다.

### <a name="prevention"></a>방지
손상이 우려되거나 손상된 ID 또는 디바이스를 남용하여 조직에 대한 손상을 방지하는 동작입니다. 예방 조치는 장치 또는 ID를 보호하지 않으며 이전 위험 검색을 해결하지 않습니다.

### <a name="privileged-user"></a>권한 있는(사용자)
위험 검색 시 글로벌 관리자, 청구 관리자, 서비스 관리자, 사용자 관리자 및 암호와 같은 Azure Active Directory에서 하나 이상의 리소스에 대한 영구 또는 임시 관리자 권한이 있는 사용자 관리자. 

### <a name="real-time"></a>실시간
실시간 탐지를 참조하세요.

### <a name="real-time-detection"></a>실시간 탐지
잘못된 부분을 감지하고 이미 발생한 이벤트에 대한 이벤트를 진행하기 전에 로그인 시도와 같은 이벤트의 위험을 평가합니다.

### <a name="remediated-risk-detection"></a>수정(위험 감지)
ID 보호에 의해 자동으로 설정된 위험 검색 상태는 이러한 유형의 위험 검색에 대한 표준 수정 작업을 사용하여 위험 검색을 수정했음을 나타냅니다. 예를 들어 사용자 암호를 재설정하면 이전 암호가 손상되었음을 나타내는 많은 위험 검색이 자동으로 수정됩니다.

### <a name="remediation"></a>재구성
이전에 손상이 우려되거나 손상된 ID 또는 디바이스를 보호하는 작업입니다. 업데이트 관리 작업은 ID 또는 장치를 안전한 상태로 복원하고 ID 또는 장치와 관련된 이전 위험 검색을 해결합니다.

### <a name="resolved-risk-detection"></a>해결됨(위험 감지)
ID 보호 사용자가 수동으로 설정한 위험 검색 상태는 사용자가 ID 보호 외부에서 적절한 수정 작업을 수행했으며 위험 검색을 닫힌 것으로 간주해야 한다는 것을 나타냅니다.

### <a name="risk-detection-status"></a>위험 탐지 상태
이벤트가 활성 상태인지 여부와 닫혔을 경우 종료 이유를 나타내는 위험 검색의 속성입니다.

### <a name="risk-detection-type"></a>위험 탐지 유형
이벤트가 위험한 것으로 간주되도록 한 이상 유형을 나타내는 위험 검색범주입니다.

### <a name="risk-level-risk-detection"></a>위험 수준(위험 감지)
ID 보호 사용자가 조직에 대한 위험을 줄이기 위해 취하는 작업의 우선 순위를 지정하는 데 도움이 되는 위험 검색의 심각도표시(높음, 보통 또는 낮음)입니다. 

### <a name="risk-level-sign-in"></a>위험 수준(로그인)
사용자의 ID를 사용하려는 다른 사용자라는 특정 로그인에 대한 가능성을 표시(높음, 중간 또는 낮음)합니다.

### <a name="risk-level-user-compromise"></a>위험 수준(사용자 손상)
ID가 손상된 가능성을 표시(높음, 중간 또는 낮음)합니다.

### <a name="risk-level-vulnerability"></a>위험 수준(취약점)
ID 보호 사용자가 조직에 대한 위험을 줄이기 위해 수행하는 동작을 우선하도록 하는 취약점의 심각도를 표시(높음, 중간 또는 낮음)합니다.

### <a name="secure-identity"></a>보안(ID)
잠재적으로 노출된 ID를 비교할 수 없는 상태로 복원하기 위해 이미지로 다시 설치한 암호 변경 또는 컴퓨터와 같은 수정 작업을 수행합니다.

### <a name="security-policy"></a>보안 정책
정책 규칙 및 조건의 컬렉션입니다. 사용자, 그룹, 앱, 디바이스, 디바이스 플랫폼, 디바이스 상태, IP 범위 및 Auth2.0 클라이언트 유형과 같은 엔터티에 정책을 적용할 수 있습니다. 정책을 사용하는 경우 정책에 포함된 엔터티가 리소스에 토큰을 발급할 때마다 평가됩니다.

### <a name="sign-in-v"></a>로그인(v)
Azure Active Directory의 ID를 인증하려면.

### <a name="sign-in-n"></a>로그인(n)
Azure Active Directory에서 ID를 인증하는 프로세스 또는 동작이며 이 작업을 캡처하는 이벤트입니다.

### <a name="sign-in-from-anonymous-ip-address"></a>익명 IP 주소에서 로그인
익명 프록시 IP 주소로 식별된 IP 주소에서 성공적으로 로그인한 후 트리거되는 위험 검색입니다.

### <a name="sign-in-from-infected-device"></a>감염된 디바이스에서 로그인
봇 서버와 적극적으로 통신을 시도하는 하나 이상의 손상된 장치에서 사용되는 것으로 알려진 IP 주소에서 로그인이 시작될 때 위험 검색이 트리거됩니다.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>의심스러운 동작으로 IP 주소에서 로그인
짧은 기간 동안 여러 사용자 계정에서 실패한 로그인 시도가 많은 IP 주소에서 성공적으로 로그인한 후 트리거되는 위험 검색입니다.

### <a name="sign-in-from-unfamiliar-location"></a>알 수 없는 위치에서 로그인
사용자가 새 위치(IP, 위도/경도 및 ASN)에서 성공적으로 로그인하면 위험 감지가 트리거됩니다.

### <a name="sign-in-risk"></a>로그인 위험
위험 수준(로그인)을 참조하세요.

### <a name="sign-in-risk-policy"></a>로그인 위험 정책
특정 로그인에 대한 위험을 평가하고 미리 정의된 조건 및 규칙에 따라 완화를 적용하는 조건부 액세스 정책입니다.

### <a name="user-compromise-risk"></a>사용자 손상 위험
위험 수준(사용자 손상)을 참조하세요

### <a name="user-risk"></a>사용자 위험
위험 수준(사용자 손상)을 참조하세요.

### <a name="user-risk-policy"></a>사용자 위험 정책
로그인을 고려하고 미리 정의된 조건 및 규칙에 따라 완화를 적용하는 조건부 액세스 정책입니다.

### <a name="users-flagged-for-risk"></a>위험에 대한 플래그가 지정된 사용자
활성 또는 수정된 위험 탐지가 있는 사용자

### <a name="vulnerability"></a>취약점
디렉터리가 악용 또는 위협에 취약하게 만드는 Azure Active Directory의 구성 또는 조건입니다.

## <a name="see-also"></a>참조

- [Azure Active Directory ID 보호](../active-directory-identityprotection.md)
