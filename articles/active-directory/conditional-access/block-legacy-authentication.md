---
title: 조건부 액세스를 사용 하 여 Azure Active Directory (Azure AD)에 대 한 레거시 인증을 차단 하는 방법 | Microsoft Docs
description: Azure AD 조건부 액세스를 사용 하 여 레거시 인증을 차단 하 여 보안 태세를 향상 시키는 방법에 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b2120466652db363206ec20c2303ad56670228c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164795"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>방법: Azure ad 조건부 액세스를 사용 하 여 레거시 인증 블록   

사용자가 클라우드 앱에 쉽게 액세스할 수 있도록 Azure AD(Active Directory)에서 레거시 인증을 포함한 다양한 인증 프로토콜을 지원합니다. 그러나 레거시 프로토콜은 MFA(다단계 인증)를 지원하지 않습니다. MFA는 다양한 환경에서 ID 도용 문제를 해결하기 위한 일반적인 요구 사항입니다. 

환경의 테 넌 트의 보호를 개선 하기 위해 레거시 인증 블록으로 준비 되 면 경우에 조건부 액세스를 사용 하 여이 목표를 수행할 수 있습니다. 이 문서에서는 테 넌 트에 대 한 레거시 인증을 차단 하는 조건부 액세스 정책을 구성할 수는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 사용자가 다음에 대해 잘 알고 있다고 가정합니다. 

- 합니다 [기본 개념](overview.md) Azure AD 조건부 액세스 
- 합니다 [모범 사례](best-practices.md) Azure portal에서 조건부 액세스 정책을 구성 하기 위한

## <a name="scenario-description"></a>시나리오 설명

Azure AD는 레거시 인증을 포함하여 가장 널리 사용되는 몇 가지 인증 및 권한 부여 프로토콜을 지원합니다. 레거시 인증은 기본 인증을 사용하는 프로토콜을 가리킵니다. 일반적으로 이러한 프로토콜은 모든 유형의 두 번째 단계 인증을 적용할 수 없습니다. 레거시 인증을 기반으로 하는 앱의 예는 다음과 같습니다.

- 이전 Microsoft Office 앱

- POP, IMAP 및 SMTP와 같은 메일 프로토콜을 사용하는 앱

요즘에는 단일 단계 인증(예: 사용자 이름 및 암호)만으로도 충분하지 않습니다. 추측하기 쉬운 암호는 적합하지 않으며, 인간이 적합한 암호를 선택하는 데도 서투릅니다. 또한 암호는 피싱 또는 암호 스프레이와 같은 다양한 공격에도 취약합니다. 암호 위협으로부터 보호하기 위해 수행할 수 있는 가장 쉬운 방법 중 하나는 MFA를 구현하는 것입니다. MFA를 사용하면 공격자가 사용자의 암호를 획득하더라도 암호만으로 데이터를 성공적으로 인증하고 액세스하기에는 충분하지 않습니다.

레거시 인증을 사용하는 애플리케이션에서 테넌트의 리소스에 액세스하지 못하도록 방지하려면 어떻게 해야 할까요? 방금 조건부 액세스 정책을 사용 하 여 차단 하는 것이 좋습니다. 필요한 경우 특정 사용자 및 특정 네트워크 위치만 레거시 인증을 기반으로 하는 애플리케이션을 사용하도록 허용합니다.

조건부 액세스 정책에는 첫 번째 단계 인증을 완료 된 후 적용 됩니다. 따라서 조건부 액세스는 서비스 거부 (DoS) 공격 등의 시나리오에 대 한 첫 번째 줄 방어로 사용할 수 없는 있지만 액세스 확인을 위해 이러한 이벤트 (예: 로그인 위험 수준, 요청, 및 등의 위치)에서 신호를 활용할 수 있습니다.

## <a name="implementation"></a>구현

이 섹션에서는 블록 레거시 인증 하는 조건부 액세스 정책을 구성 하는 방법에 설명 합니다. 

### <a name="identify-legacy-authentication-use"></a>레거시 인증 사용을 식별

디렉터리에서 레거시 인증을 차단할 수 있습니다, 전에 먼저 사용자가 앱 레거시 인증 및 전반적 디렉토리에 미치는 영향을 사용 하는 경우를 이해 해야 합니다. 레거시 인증을 사용 하는 경우를 이해 하려면 azure AD 로그인 로그를 사용할 수 있습니다.

1. 로 이동 합니다 **Azure portal** > **Azure Active Directory** > **로그인**합니다.
1. 클릭 하 여 표시 되지 않으면 클라이언트 앱 열을 추가 **열** > **클라이언트 앱**합니다.
1. 기준으로 필터링 **클라이언트 앱** > **기타 클라이언트** 누릅니다 **적용**합니다.

로그인 하면 표시 하는 시도 필터링을 레거시 인증 프로토콜에서 했습니다. 각 개별 로그인 시도에서 클릭 하면 추가 세부 정보 표시 됩니다. 합니다 **클라이언트 앱** 아래에 있는 필드를 **기본 정보** 탭 사용 된 레거시 인증 프로토콜을 표시 합니다.

이러한 로그는 응용 프로그램 인증 요청을 레거시 프로토콜을 사용 하 고 있는 사용자는 레거시 인증에 따라 계속 표시 됩니다. 이러한 로그에 표시 되지 않으며 레거시 인증 사용 하지 않는으로 확인 되는 사용자의 경우에 이러한 사용자에 대 한 조건부 액세스 정책을 구현 합니다.

### <a name="block-legacy-authentication"></a>레거시 인증 차단 

조건부 액세스 정책에서 리소스에 액세스 하는 데 사용 되는 클라이언트 앱에 연결 된 조건을 설정할 수 있습니다. **모바일 앱 및 데스크톱 클라이언트**에 대해 **기타 클라이언트**를 선택하여 레거시 인증을 사용하는 앱으로 앱의 범위를 좁힐 수 있습니다.

![다른 클라이언트](./media/block-legacy-authentication/01.png)

이러한 앱에 대한 액세스를 차단하려면 **액세스 차단**을 선택해야 합니다.

![액세스 차단](./media/block-legacy-authentication/02.png)


### <a name="select-users-and-cloud-apps"></a>사용자 및 클라우드앱 선택

조직의 레거시 인증을 차단하려면 다음을 선택하여 이 작업을 수행할 수 있습니다.

- 모든 사용자

- 모든 클라우드 앱

- 액세스 차단
 

![할당](./media/block-legacy-authentication/03.png)



Azure에이 구성은 위반 때문에이 정책을 만들지 못하게 방지 하는 보안 기능을 [모범 사례](best-practices.md) 조건부 액세스 정책에 대 한 합니다.
 
![지원되지 않는 정책 구성](./media/block-legacy-authentication/04.png)


*모든 사용자 및 모든 클라우드 앱 차단*은 전체 조직에서 테넌트에 로그인하지 못하도록 차단할 수 있으므로 보안 기능이 필요합니다. 최소 모범 사례 요구 사항을 충족하려면 한 명 이상의 사용자를 제외해야 합니다. 디렉터리 역할을 제외할 수도 있습니다.

![지원되지 않는 정책 구성](./media/block-legacy-authentication/05.png)


정책에서 한 명의 사용자를 제외하여 이 보안 기능을 충족할 수 있습니다. [Azure AD에서 몇 개의 응급 액세스 관리 계정](../users-groups-roles/directory-emergency-access.md)을 정의하고 정책에서 이를 제외하는 것이 좋습니다.
 

## <a name="policy-deployment"></a>정책 배포

정책을 프로덕션에 적용하기 전에 다음 사항에 주의하세요.
 
- **서비스 계정** - 회의실 전화와 같이 서비스 계정으로 사용되거나 디바이스별로 사용되는 사용자 계정을 식별합니다. 이러한 계정에 강력한 암호가 있는지 확인하고 제외된 그룹에 추가합니다.
 
- **로그인 보고서** - 로그인 보고서를 검토하고 **다른 클라이언트** 트래픽을 찾습니다. 상위 사용량을 식별하고 사용 중인 이유를 조사합니다. 일반적으로 트래픽은 최신 인증 또는 일부 타사 메일 앱을 사용하지 않는 이전의 Office 클라이언트에서 생성됩니다. 이러한 앱에서 사용량을 이동시키려고 계획하거나, 영향력이 적은 경우 사용자에게 해당 앱을 더 이상 사용할 수 없음을 알립니다.
 
자세한 내용은 [새 정책을 배포하려면 어떻게 해야 합니까?](best-practices.md#how-should-you-deploy-a-new-policy)를 참조하세요.



## <a name="what-you-should-know"></a>알아야 할 사항

사용 하 여 액세스 차단 **다른 클라이언트** 기본 인증을 사용 하 여 Exchange Online PowerShell 차단

**기타 클라이언트**에 대한 정책 구성은 SPConnect와 같은 특정 클라이언트에서 전체 조직을 차단합니다. 이러한 차단은 이전 버전의 클라이언트가 예기치 않은 방식으로 인증하기 때문에 발생합니다. 이 문제는 이전 버전의 Office 클라이언트와 같은 주요 Office 애플리케이션에 적용되지 않습니다.

정책이 적용되려면 최대 24시간까지 걸릴 수 있습니다.

다른 클라이언트 조건에 사용할 수 있는 모든 권한 부여 제어를 선택할 수 있지만, 최종 사용자 환경은 항상 동일합니다(액세스 차단).

다른 클라이언트 조건을 사용하여 레거시 인증을 차단하는 경우 디바이스 플랫폼 및 위치 조건을 설정할 수도 있습니다. 예를 들어 모바일 디바이스에 대한 레거시 인증만 차단하려면 다음을 선택하여 **디바이스 플랫폼** 조건을 설정합니다.

- Android

- iOS

- Windows Phone

![지원되지 않는 정책 구성](./media/block-legacy-authentication/06.png)




## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책을 아직 구성 잘 모르는 경우 [Azure Active Directory 조건부 액세스를 사용 하 여 특정 앱에 대 한 mfa](app-based-mfa.md) 예입니다.

- 최신 인증 지원에 대 한 자세한 내용은 참조 하세요. [Office 2013 및 Office 2016 클라이언트 앱에 대 한 최신 인증 작동](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
