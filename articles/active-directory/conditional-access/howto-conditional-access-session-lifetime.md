---
title: 인증 세션 관리 구성 - Azure Active Directory
description: 사용자 로그인 빈도 및 브라우저 세션 지속성을 포함하여 Azure AD 인증 세션 구성을 사용자 지정합니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e9c0c88064c00c97de7dc58a500910e81c04eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263284"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>조건부 액세스를 통해 인증 세션 관리 구성

복잡한 배포에서 조직은 인증 세션을 제한해야 할 수 있습니다. 일부 시나리오에는 다음이 포함될 수 있습니다.

* 관리되지 않거나 공유된 장치에서 리소스 액세스
* 외부 네트워크에서 중요한 정보에 액세스
* 영향이 큰 사용자
* 중요한 비즈니스 애플리케이션

조건부 액세스 제어를 사용하면 모든 사용자에게 영향을 주지 않고 조직 내의 특정 사용 사례를 대상으로 하는 정책을 만들 수 있습니다.

정책을 구성하는 방법에 대한 자세한 내용을 살펴보기 전에 기본 구성을 살펴보겠습니다.

## <a name="user-sign-in-frequency"></a>사용자 로그인 빈도

로그인 빈도는 사용자가 리소스에 액세스하려고 할 때 다시 로그인하라는 메시지가 표시되기 전의 기간을 정의합니다.

사용자 로그인 빈도에 대한 Azure Active Directory(Azure AD) 기본 구성은 90일의 롤링 기간입니다. 사용자에게 자격 증명을 요청하는 것은 현명한 일처럼 보이지만, 생각없이 자격 증명을 입력하도록 훈련받은 사용자는 실수로 악의적인 자격 증명 프롬프트에 제공할 수 있습니다.

실제로 IT 정책을 위반하면 세션이 취소됩니다, 다시 로그인하도록 요청하지 않는 것이 놀라운 소리가 들릴 수 있습니다. 몇 가지 예로는 암호 변경, 비준수 장치 또는 계정 사용 안 함등이 있습니다(이에 국한되지 않음). PowerShell 을 사용하여 사용자의 세션을 명시적으로 [취소할](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)수도 있습니다. Azure AD 기본 구성은 "세션의 보안 태세가 변경되지 않은 경우 사용자에게 자격 증명을 제공하도록 요청하지 마십시오"로 표시됩니다.

로그인 빈도 설정은 표준에 따라 OAUTH2 또는 OIDC 프로토콜을 구현한 앱과 함께 작동합니다. 다음 웹 응용 프로그램을 포함하여 Windows, Mac 및 모바일용 대부분의 Microsoft 기본 앱은 설정을 준수합니다.

- 워드, 엑셀, 파워 포인트 온라인
- 원노트 온라인
- Office.com
- O365 관리 포털
- Exchange Online
- SharePoint 및 OneDrive
- 팀 웹 클라이언트
- Dynamics CRM Online
- Azure portal

### <a name="user-sign-in-frequency-and-device-identities"></a>사용자 로그인 빈도 및 장치 ID

Azure AD가 조인된 경우 하이브리드 Azure AD 가 조인된 경우 또는 Azure AD 등록 된 장치, 사용자가 대화형으로 자신의 장치 또는 로그인을 잠금 해제 하는 경우이 이벤트 뿐만 아니라 로그인 주파수 정책을 만족 합니다. 다음 2가지 예에서 사용자 로그인 빈도는 1시간으로 설정됩니다.

예제 1:

- 00:00에 사용자는 Windows 10 Azure AD에 가입한 장치에 서명하고 SharePoint Online에 저장된 문서에 대한 작업을 시작합니다.
- 사용자는 한 시간 동안 장치에서 동일한 문서에서 작업을 계속합니다.
- 01:00에 사용자는 관리자가 구성한 조건부 액세스 정책의 로그인 빈도 요구 사항에 따라 다시 로그인하라는 메시지가 표시됩니다.

예제 2:

- 00:00에 사용자는 Windows 10 Azure AD에 가입한 장치에 서명하고 SharePoint Online에 저장된 문서에 대한 작업을 시작합니다.
- 00:30에, 사용자는 일어나서 장치를 잠그는 휴식을 취합니다.
- 00:45에 사용자는 휴식 후 돌아와 장치의 잠금을 해제합니다.
- 01:45에서 마지막 로그인이 00:45에 발생한 이후 관리자가 구성한 조건부 액세스 정책의 로그인 빈도 요구 사항에 따라 다시 로그인하라는 메시지가 표시됩니다.

## <a name="persistence-of-browsing-sessions"></a>브라우징 세션의 지속성

영구 브라우저 세션을 사용하면 브라우저 창을 닫고 다시 연 후에도 로그인 상태를 유지할 수 있습니다.

브라우저 세션 지속성에 대한 Azure AD 기본값을 사용하면 개인 장치의 사용자가 "로그인 유지"를 표시하여 세션을 유지할지 여부를 선택할 수 있습니다. 인증에 성공한 후 프롬프트가 표시됩니다. AD FS [단일 사인온 설정의](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)지침을 사용하여 AD FS에서 브라우저 지속성을 구성하는 경우 해당 정책을 준수하고 Azure AD 세션도 유지합니다. 테넌트의 사용자에게 "로그인 유지" 표시 여부를 구성할 수도 있습니다. Azure [AD 로그인 페이지를 사용자 지정하는](../fundamentals/customize-branding.md)문서의 지침을 사용하여 Azure Portal의 회사 브랜딩 창에서 적절한 설정을 변경하여 프롬프트합니다.

## <a name="configuring-authentication-session-controls"></a>인증 세션 컨트롤 구성

조건부 액세스는 Azure AD Premium 기능이며 프리미엄 라이선스가 필요합니다. 조건부 액세스에 대해 자세히 알아보려면 [Azure Active Directory의 조건부 액세스란 무엇입니까?](overview.md#license-requirements)

> [!WARNING]
> 현재 공개 미리 보기에서 [구성 가능한 토큰 수명](../develop/active-directory-configurable-token-lifetimes.md) 기능을 사용하는 경우 동일한 사용자 또는 앱 조합에 대해 두 가지 다른 정책(이 기능이 있는 정책과 구성 가능한 토큰 수명 기능이 있는 정책)을 만드는 것은 지원되지 않습니다. Microsoft는 2020년 5월 1일에 구성 가능한 토큰 수명 기능을 폐기하고 조건부 액세스 인증 세션 관리 기능으로 대체할 계획입니다.  

### <a name="policy-1-sign-in-frequency-control"></a>정책 1: 로그인 주파수 제어

1. 새 정책 만들기
1. 대상 클라우드 앱을 포함하여 고객 환경에 필요한 모든 조건을 선택합니다.

   > [!NOTE]
   > 최상의 사용자 환경을 위해 Exchange Online 및 SharePoint Online과 같은 주요 Microsoft Office 앱에 대해 동일한 인증 프롬프트 빈도를 설정하는 것이 좋습니다.

1. 액세스 **제어** > **세션으로** 이동하여 **로그인 빈도를 클릭합니다.**
1. 첫 번째 텍스트 상자에 필요한 일 및 시간 값을 입력합니다.
1. 드롭다운에서 **시간** 또는 **일** 값을 선택합니다.
1. 정책 저장

![로그인 빈도에 대해 구성된 조건부 액세스 정책](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Azure AD에 등록된 Windows 장치에서 장치에 로그인하는 것은 프롬프트로 간주됩니다. 예를 들어 Office 앱의 경우 로그인 빈도를 24시간으로 구성한 경우 Azure AD에 등록된 Windows 장치의 사용자는 장치에 로그인하여 로그인 빈도 정책을 충족하고 Office 앱을 열 때 다시 메시지가 표시되지 않습니다.

동일한 브라우저 세션에서 실행 중인 다른 웹 앱에 대해 서로 다른 로그인 빈도를 구성한 경우 동일한 브라우저 세션에서 실행되는 모든 앱이 단일 세션 토큰을 공유하기 때문에 두 앱 모두에 가장 엄격한 정책이 적용됩니다.

### <a name="policy-2-persistent-browser-session"></a>정책 2: 영구 브라우저 세션

1. 새 정책 만들기
1. 필요한 모든 조건을 선택합니다.

   > [!NOTE]
   > 이 컨트롤은 조건으로 "모든 클라우드 앱"을 선택해야 합니다. 브라우저 세션 지속성은 인증 세션 토큰에 의해 제어됩니다. 브라우저 세션의 모든 탭은 단일 세션 토큰을 공유하므로 모두 지속성 상태를 공유해야 합니다.

1. 액세스 **제어** > **세션으로** 이동하여 **영구 브라우저 세션을 클릭합니다.**
1. 드롭다운에서 값 선택
1. 정책 저장

![영구 브라우저에 대해 구성된 조건부 액세스 정책](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Azure AD 조건부 액세스의 영구 브라우저 세션 구성은 "로그인 유지" 를 덮어씁니다. 두 정책을 모두 구성한 경우 동일한 사용자에 대해 Azure 포털의 회사 브랜딩 창에서 설정합니다.

## <a name="validation"></a>유효성 검사

What-If 도구를 사용하여 정책을 구성한 방법에 따라 사용자에서 대상 응용 프로그램 및 기타 조건으로 로그인을 시뮬레이션합니다. 인증 세션 관리 컨트롤은 도구의 결과에 표시됩니다.

![조건부 액세스 무엇 경우 도구 결과](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>정책 배포

정책이 예상대로 작동하는지 확인하는 데 권장되는 모범 사례는 프로덕션에 배포하기 전에 테스트하는 것입니다. 테스트 테넌트를 사용하여 새 정책이 의도한 대로 작동하는지 확인하는 것이 좋습니다. 자세한 내용은 [Azure Active Directory의 조건부 액세스에 대한 모범 사례](best-practices.md)문서를 참조하십시오.

## <a name="next-steps"></a>다음 단계

* 조건부 액세스 정책을 구성하는 방법을 알고 싶으시면 [Azure Active Directory 조건부 액세스가 있는 특정 앱에 대해 MFA 필요](app-based-mfa.md)문서를 참조하십시오.
* 환경에 대한 조건부 액세스 정책을 구성할 준비가 되면 [Azure Active Directory의 조건부 액세스에 대한 모범 사례](best-practices.md)문서를 참조하십시오.
