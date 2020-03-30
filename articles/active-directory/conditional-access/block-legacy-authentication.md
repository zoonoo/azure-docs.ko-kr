---
title: 레거시 인증 차단 - Azure Active Directory
description: Azure AD 조건부 액세스를 사용하여 레거시 인증을 차단하여 보안 상태를 개선하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76dd07a59a9fa7c0d6231a766ff4090c11f9f5bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331920"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>방법: 조건부 액세스를 사용하여 Azure AD에 대한 레거시 인증 차단   

사용자가 클라우드 앱에 쉽게 액세스할 수 있도록 Azure AD(Active Directory)에서 레거시 인증을 포함한 다양한 인증 프로토콜을 지원합니다. 그러나 레거시 프로토콜은 MFA(다단계 인증)를 지원하지 않습니다. MFA는 다양한 환경에서 ID 도용 문제를 해결하기 위한 일반적인 요구 사항입니다. 

Alex Weinert, Microsoft ID 보안 디렉터는 2020년 3월 12일 블로그 게시물에서 [조직에서 레거시 인증을 차단하는 새로운 도구를](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) 게시하여 조직에서 레거시 인증을 차단해야 하는 이유와 Microsoft가 이 작업을 수행하기 위해 제공하는 추가 도구를 강조합니다.

> MFA가 효과적이려면 레거시 인증도 차단해야 합니다. POP, SMTP, IMAP 및 MAPI와 같은 레거시 인증 프로토콜은 MFA를 적용할 수 없으므로 조직을 공격하는 적에게 선호되는 진입점입니다...
> 
>... Azure Active Directory(Azure AD) 트래픽 분석의 레거시 인증 에 대한 숫자는 다음과 같습니다.
> 
> - 암호 스프레이 공격의 99% 이상이 레거시 인증 프로토콜을 사용합니다.
> - 자격 증명 스터핑 공격의 97% 이상이 레거시 인증을 사용합니다.
> - 레거시 인증을 사용하지 않도록 설정한 조직의 Azure AD 계정은 레거시 인증을 사용하도록 설정된 계정보다 67% 더 적은 손상을 경험합니다.
>

환경이 레거시 인증을 차단하여 테넌트의 보호를 개선할 준비가 된 경우 조건부 Access를 사용하여 이 목표를 달성할 수 있습니다. 이 문서에서는 테넌트의 레거시 인증을 차단하는 조건부 액세스 정책을 구성하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 사용자가 다음에 대해 잘 알고 있다고 가정합니다. 

- Azure AD 조건부 액세스의 [기본 개념](overview.md) 
- Azure 포털에서 조건부 액세스 정책을 구성하는 [모범 사례](best-practices.md)

## <a name="scenario-description"></a>시나리오 설명

Azure AD는 레거시 인증을 포함하여 가장 널리 사용되는 몇 가지 인증 및 권한 부여 프로토콜을 지원합니다. 레거시 인증은 기본 인증을 사용하는 프로토콜을 가리킵니다. 일반적으로 이러한 프로토콜은 모든 유형의 두 번째 단계 인증을 적용할 수 없습니다. 레거시 인증을 기반으로 하는 앱의 예는 다음과 같습니다.

- 이전 Microsoft Office 앱
- POP, IMAP 및 SMTP와 같은 메일 프로토콜을 사용하는 앱

요즘에는 단일 단계 인증(예: 사용자 이름 및 암호)만으로도 충분하지 않습니다. 추측하기 쉬운 암호는 적합하지 않으며, 인간이 적합한 암호를 선택하는 데도 서투릅니다. 또한 암호는 피싱 또는 암호 스프레이와 같은 다양한 공격에도 취약합니다. 암호 위협으로부터 보호하기 위해 수행할 수 있는 가장 쉬운 방법 중 하나는 MFA를 구현하는 것입니다. MFA를 사용하면 공격자가 사용자의 암호를 획득하더라도 암호만으로 데이터를 성공적으로 인증하고 액세스하기에는 충분하지 않습니다.

레거시 인증을 사용하는 애플리케이션에서 테넌트의 리소스에 액세스하지 못하도록 방지하려면 어떻게 해야 할까요? 조건부 액세스 정책으로 차단하는 것이 좋습니다. 필요한 경우 특정 사용자 및 특정 네트워크 위치만 레거시 인증을 기반으로 하는 애플리케이션을 사용하도록 허용합니다.

1단계 인증이 완료된 후 조건부 액세스 정책이 적용됩니다. 이처럼 조건부 액세스는 DoS(서비스 거부) 공격 같은 시나리오에서 최전방 방어선으로 사용하기 위해 개발된 것은 아니지만, 이러한 이벤트의 신호를 활용하여(예: 로그인 위험 수준, 요청 위치 등) 액세스를 결정할 수 있습니다.

## <a name="implementation"></a>구현

이 섹션에서는 레거시 인증을 차단하도록 조건부 액세스 정책을 구성하는 방법을 설명합니다. 

### <a name="legacy-authentication-protocols"></a>레거시 인증 프로토콜

다음 옵션은 레거시 인증 프로토콜로 간주됩니다.

- 인증된 SMTP - POP 및 IMAP 클라이언트에서 전자 메일 메시지를 보내는 데 사용됩니다.
- 자동 검색 - Outlook 및 EAS 클라이언트에서 Exchange Online에서 사서함을 찾고 연결하는 데 사용됩니다.
- 교환 온라인 파워쉘 - 원격 PowerShell으로 교환 온라인에 연결하는 데 사용됩니다. Exchange 온라인 PowerShell에 대한 기본 인증을 차단하는 경우 Exchange 온라인 PowerShell 모듈을 사용하여 연결해야 합니다. 지침은 [다단계 인증을 사용하여 Exchange 온라인 PowerShell에 연결 을](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)참조하십시오.
- 교환 웹 서비스 (EWS) - Outlook, Mac용 Outlook 및 타사 앱에서 사용하는 프로그래밍 인터페이스입니다.
- IMAP4 - IMAP 이메일 클라이언트에서 사용됩니다.
- MAPI HTTP (MAPI / HTTP) 이상 - Outlook 2010 이상에서 사용됩니다.
- OAB(오프라인 주소록) - Outlook에서 다운로드하여 사용하는 주소 목록 모음의 복사본입니다.
- Outlook 어디서나 (HTTP를 통해 RPC) - Outlook 2016 및 이전에서 사용됩니다.
- Outlook 서비스 - Windows 10용 메일 및 캘린더 앱에서 사용됩니다.
- POP3 - POP 이메일 클라이언트에서 사용됩니다.
- 웹 서비스 보고 - Exchange Online에서 보고서 데이터를 검색하는 데 사용됩니다.
- 기타 클라이언트 - 레거시 인증을 사용하는 것으로 확인된 기타 프로토콜입니다.

이러한 인증 프로토콜 및 서비스에 대한 자세한 내용은 [Azure Active Directory 포털의 로그인 활동 보고서를](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)참조하십시오.

### <a name="identify-legacy-authentication-use"></a>레거시 인증 사용 식별

디렉터리에서 레거시 인증을 차단하려면 먼저 사용자에게 레거시 인증을 사용하는 앱이 있는지, 그리고 레거시 인증을 사용하는 앱이 전체 디렉터리에 미치는 영향을 이해해야 합니다. Azure AD 로그인 로그를 사용하여 레거시 인증을 사용하는지 확인할 수 있습니다.

1. **Azure 포털** > **Azure Active 디렉터리** > **로그인으로 이동합니다.**
1. 열 클라이언트 응용 프로그램을 클릭하여 표시되지 않는 경우 클라이언트**앱 열을** **추가합니다.** > 
1. **필터 클라이언트** > **앱** > 모든 레거시 인증 프로토콜을 선택하고 **적용을**클릭합니다.

필터링은 레거시 인증 프로토콜에 의해 만들어진 로그인 시도만 표시됩니다. 각 개별 로그인 시도를 클릭하면 추가 세부 정보가 표시됩니다. **기본 정보** 탭 아래의 **클라이언트 앱** 필드는 사용된 레거시 인증 프로토콜을 나타냅니다.

이러한 로그는 레거시 인증에 따라 남아 있는 사용자와 인증 요청을 하기 위해 레거시 프로토콜을 사용하는 응용 프로그램을 나타냅니다. 이러한 로그에 나타나지 않고 레거시 인증을 사용하지 않는 것으로 확인된 사용자의 경우 이러한 사용자에 대해서만 조건부 액세스 정책을 구현합니다.

### <a name="block-legacy-authentication"></a>레거시 인증 차단 

조건부 액세스 정책에서 리소스에 액세스하는 데 사용되는 클라이언트 앱에 연결된 조건을 설정할 수 있습니다. **모바일 앱 및 데스크톱 클라이언트**에 대해 **기타 클라이언트**를 선택하여 레거시 인증을 사용하는 앱으로 앱의 범위를 좁힐 수 있습니다.

![기타 클라이언트](./media/block-legacy-authentication/01.png)

이러한 앱에 대한 액세스를 차단하려면 **액세스 차단**을 선택해야 합니다.

![액세스 차단](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>사용자 및 클라우드앱 선택

조직의 레거시 인증을 차단하려면 다음을 선택하여 이 작업을 수행할 수 있습니다.

- 모든 사용자가 액세스할 수 있습니다.
- 모든 클라우드 앱
- 액세스 차단

![할당](./media/block-legacy-authentication/03.png)

Azure에는 이 구성이 조건부 액세스 정책의 [모범 사례를](best-practices.md) 위반하기 때문에 이와 같은 정책을 만들지 못하도록 하는 안전 기능이 있습니다.
 
![지원되지 않는 정책 구성](./media/block-legacy-authentication/04.png)

*모든 사용자 및 모든 클라우드 앱 차단*은 전체 조직에서 테넌트에 로그인하지 못하도록 차단할 수 있으므로 보안 기능이 필요합니다. 최소 모범 사례 요구 사항을 충족하려면 한 명 이상의 사용자를 제외해야 합니다. 디렉터리 역할을 제외할 수도 있습니다.

![지원되지 않는 정책 구성](./media/block-legacy-authentication/05.png)

정책에서 한 명의 사용자를 제외하여 이 보안 기능을 충족할 수 있습니다. [Azure AD에서 몇 개의 응급 액세스 관리 계정](../users-groups-roles/directory-emergency-access.md)을 정의하고 정책에서 이를 제외하는 것이 좋습니다.

정책이 레거시 인증을 차단할 때 [보고서 전용 모드를](concept-conditional-access-report-only.md) 사용하면 조직에서 정책의 영향을 모니터링할 수 있습니다.

## <a name="policy-deployment"></a>정책 배포

정책을 프로덕션에 적용하기 전에 다음 사항에 주의하세요.
 
- **서비스 계정** - 회의실 전화와 같이 서비스 계정으로 사용되거나 디바이스별로 사용되는 사용자 계정을 식별합니다. 이러한 계정에 강력한 암호가 있는지 확인하고 제외된 그룹에 추가합니다.
- **로그인 보고서** - 로그인 보고서를 검토하고 **다른 클라이언트** 트래픽을 찾습니다. 상위 사용량을 식별하고 사용 중인 이유를 조사합니다. 일반적으로 트래픽은 최신 인증 또는 일부 타사 메일 앱을 사용하지 않는 이전의 Office 클라이언트에서 생성됩니다. 이러한 앱에서 사용량을 이동시키려고 계획하거나, 영향력이 적은 경우 사용자에게 해당 앱을 더 이상 사용할 수 없음을 알립니다.
 
자세한 내용은 [새 정책을 배포하려면 어떻게 해야 합니까?](best-practices.md#how-should-you-deploy-a-new-policy)를 참조하세요.

## <a name="what-you-should-know"></a>알아야 할 사항

**다른 클라이언트를** 사용하여 액세스를 차단하는 것은 또한 기본 인증을 사용하여 Exchange 온라인 PowerShell 및 역학 365를 차단합니다.

**기타 클라이언트**에 대한 정책 구성은 SPConnect와 같은 특정 클라이언트에서 전체 조직을 차단합니다. 이러한 차단은 이전 버전의 클라이언트가 예기치 않은 방식으로 인증하기 때문에 발생합니다. 이 문제는 이전 버전의 Office 클라이언트와 같은 주요 Office 애플리케이션에 적용되지 않습니다.

정책이 적용되려면 최대 24시간까지 걸릴 수 있습니다.

**기타 클라이언트** 조건에 대해 사용 가능한 모든 권한 부여 컨트롤을 선택할 수 있습니다. 그러나 최종 사용자 환경은 항상 동일합니다- 차단된 액세스.

**기타 클라이언트** 조건을 사용하여 레거시 인증을 차단하는 경우 장치 플랫폼 및 위치 조건을 설정할 수도 있습니다. 예를 들어 모바일 디바이스에 대한 레거시 인증만 차단하려면 다음을 선택하여 **디바이스 플랫폼** 조건을 설정합니다.

- Android
- iOS
- Windows Phone

![지원되지 않는 정책 구성](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>다음 단계

- [조건부 액세스 보고서 전용 모드를 사용하여 영향 확인](howto-conditional-access-report-only.md)
- 아직 조건부 액세스 정책을 구성하는 데 익숙하지 않은 경우 예를 들어 [Azure Active Directory 조건부 액세스가 있는 특정 앱에 대해 MFA가 필요합니다.](app-based-mfa.md)
- 최신 인증 지원에 대한 자세한 내용은 [Office 2013 및 Office 2016 클라이언트 앱에 대한 최신 인증 작동 방식을](/office365/enterprise/modern-auth-for-office-2013-and-2016) 참조하십시오. 
