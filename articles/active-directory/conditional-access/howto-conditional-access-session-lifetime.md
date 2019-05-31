---
title: Azure Active Directory 조건부 액세스를 사용 하 여 인증 세션 관리를 구성 합니다.
description: 빈도 및 브라우저 세션 지 속성에 사용자 로그인을 포함 하 여 Azure AD 인증 세션 구성 사용자 지정 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8897de5ee86d20e52b948f21afaef4acf196539
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988571"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>조건부 액세스를 사용 하 여 인증 세션 관리를 구성 합니다.

복잡 한 배포를 조직이 인증 세션 제한할 필요가 있을 수 있습니다. 일부 시나리오 포함 될 수 있습니다.

* 관리 되지 않는 또는 공유 장치에서 리소스 액세스
* 외부 네트워크에서 중요 한 정보에 액세스
* 높은 영향 사용자
* 중요 한 비즈니스 응용 프로그램

조건부 액세스 제어를 사용 하면 모든 사용자에 게 영향을 주지 않고 조직 내의 특정 사용 사례를 대상으로 하는 정책을 만들 수 있습니다.

정책을 구성 하는 방법에 세부 정보를 살펴 보기 전에 기본 구성을 살펴보겠습니다.

## <a name="user-sign-in-frequency"></a>사용자 로그인 빈도

로그인 빈도 사용자는 리소스에 액세스 하려고 할 때 다시 로그인 하 라는 메시지가 표시 됩니다 전 까지의 기간을 정의 합니다.

사용자 로그인 빈도 대 한 Azure Active Directory (Azure AD) 기본 구성에는 90 일 롤링 창입니다. 종종 자격 증명에 대 한 사용자 요청을 위해 적절 한 것 처럼 보이지만 backfire 수 있습니다: 의도 하지 않게 생각 수 없이 자격 증명을 입력 하도록 학습 됩니다 하는 사용자가 악성 자격 증명 프롬프트를 제공 합니다.

실제로 IT 정책 위반이 세션 해지를 90 일 동안 다시 서명할 수에 대 한 요청 되지 정도로 빠른 보일 수 것입니다. 포함 (하지만에 제한 되지 않습니다) 몇 가지 예는 암호 변경, incompliant 장치 또는 계정을 사용 하지 않도록 설정 합니다. 명시적으로 할 수 있습니다 [PowerShell을 사용 하 여 사용자 세션을 취소할](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)합니다. Azure AD 기본 구성을 결국 "사용자가 해당 세션의 보안 상태 변경 되지 않은 경우 자격 증명을 제공 안 함"입니다.

로그인 빈도 설정 앱의 표준에 따라 OATH2 또는 OIDC 프로토콜 구현에서 작동 합니다. Windows, Mac 및 모바일에 대 한 대부분의 Microsoft 네이티브 앱 설정에 부합 합니다.

## <a name="persistence-of-browsing-sessions"></a>브라우징 세션의 지 속성

영구 브라우저 세션을 해당 브라우저 창을 닫은 후 로그인 상태를 유지 하는 작업을 할 수 있습니다.

브라우저 세션 지 속성에 대 한 Azure AD 기본을 "유지 로그인?"를 표시 하 여 세션을 유지할지 여부를 선택할 수 있는 개인 장치에서 사용자를 수 있습니다. 인증에 성공 하면이 메시지를 표시 합니다. 브라우저 지 속성은 문서의 지침을 사용 하 여 AD FS에서 구성 된 경우 [AD FS Single Sign On 설정](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), 해당 정책을 준수 하 고 Azure AD 세션도 유지 합니다. 테 넌 트에서 사용자를 "유지 로그인?"를 참조 하는지 여부를 구성할 수도 있습니다. 회사 브랜딩을 문서의 지침을 사용 하 여 Azure 포털의 창에서에서 적절 한 설정을 변경 하 여 프롬프트 [Azure AD 로그인 페이지 사용자 지정](../fundamentals/customize-branding.md)합니다.

## <a name="configuring-authentication-session-controls"></a>인증 세션 제어를 구성합니다.

조건부 액세스는 Azure AD Premium 기능 및 프리미엄 라이선스가 필요 합니다. 조건부 액세스에 대해 자세히 알아보려는 경우 [Azure Active Directory의 조건부 액세스란?](overview.md#license-requirements)을 참조하세요.

> [!WARNING]
> 사용 중인 경우는 [구성 가능한 토큰 수명](../develop/active-directory-configurable-token-lifetimes.md) 현재 공개 미리 보기 기능, 동일한 사용자 또는 앱 조합에 대 한 두 개의 서로 다른 정책 만들기는 지원 되지 않습니다는 점에 유의 하십시오:이 기능 및 사용 하 여 다른 구성 가능한 토큰 수명 기능입니다. Microsoft는 10 월 15에서 구성 가능한 토큰 수명 기능을 사용 중지 및 조건부 액세스 인증 세션 관리 기능을 사용 하 여 대체 계획입니다.  

### <a name="policy-1-sign-in-frequency-control"></a>정책 1: 로그인 빈도 컨트롤

1. 새 정책 만들기
1. 대상 클라우드 앱을 포함 하는 고객의 환경에 대 한 모든 필수 조건을 선택 합니다.

   > [!NOTE]
   > 최상의 사용자 경험에 대 한 Exchange Online 및 SharePoint Online과 같은 Microsoft Office 앱 키에 대 한 동일한 인증 프롬프트 빈도 설정 하는 것이 좋습니다.

1. 로 이동 **액세스 제어** > **세션** 를 클릭 하 고 **로그인 빈도**
1. 첫 번째 텍스트 상자에 필요한 날짜 및 시간 값을 입력 합니다.
1. 값 선택 **시간** 하거나 **일** 드롭다운 목록에서
1. 정책 저장

![빈도의 로그인에 대 한 구성 된 조건부 액세스 정책](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Azure AD에 등록 된 Windows 장치에 로그인 하는 장치는 프롬프트를 간주 됩니다. 예를 들어, Office 앱에 대 한 24 시간 간격으로 기호를 구성한 경우 Azure AD에서 사용자가 Windows 장치를 장치에 로그인 하 여 로그인 빈도 정책은 충족 됩니다 및 하지 묻는 메시지가 다시 Office 앱을 열 때 등록 합니다.

동일한 브라우저 세션에서 실행 되는 다른 웹 앱에 대 한 다른 로그인 빈도 구성한 경우 동일한 브라우저 세션에서 실행 중인 모든 앱에는 단일 세션 토큰을 공유 하기 때문에 가장 엄격한 정책 두 앱 모두에 적용 됩니다.

### <a name="policy-2-persistent-browser-session"></a>정책 2: 영구 브라우저 세션

1. 새 정책 만들기
1. 모든 필수 조건을 선택 합니다.

   > [!NOTE]
   > 이 컨트롤에 필요한 "모든 클라우드 앱"을 선택 하는 조건으로 note 하십시오. 브라우저 세션 지 속성은 인증 세션 토큰에 의해 제어 됩니다. 브라우저 세션에서 모든 탭에는 단일 세션 토큰을 공유 및 따라서 모든 지 속성 상태를 공유 해야 합니다.

1. 로 이동 **액세스 제어** > **세션** 를 클릭 하 고 **영구 브라우저 세션**
1. 드롭다운 목록에서 값을 선택 합니다.
1. 사용자 정책 저장

![영구 브라우저에 대 한 구성 된 조건부 액세스 정책](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> "유지 로그인 했나요?" Azure AD 조건부 액세스에서 영구 브라우저 세션 구성을 덮어씁니다. 두 정책 모두를 구성한 경우 동일한 사용자에 대 한 Azure 포털의 창 브랜딩 회사에서이 옵션을 설정 합니다.

## <a name="validation"></a>유효성 검사

가상 도구를 사용 하 여 대상 응용 프로그램 및 정책을 구성 하는 방법에 따라 다른 조건에 사용자의 로그인을 시뮬레이션 합니다. 인증 세션 관리 제어 도구의 결과에 표시 합니다.

![도구 생성 하는 경우 조건부 액세스](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>정책 배포

정책이 예상대로 작동하는지 확인하는 데 권장되는 모범 사례는 프로덕션에 배포하기 전에 테스트하는 것입니다. 테스트 테넌트를 사용하여 새 정책이 의도한 대로 작동하는지 확인하는 것이 좋습니다. 자세한 내용은 문서 참조 [Azure Active Directory에서 조건부 액세스 모범 사례](best-practices.md)합니다.

## <a name="next-steps"></a>다음 단계

* 문서를 참조 하는 조건부 액세스 정책을 구성 하는 방법을 알고 싶다면 [Azure Active Directory 조건부 액세스를 사용 하 여 특정 앱에 대 한 MFA를 요구](app-based-mfa.md)합니다.
* 사용자 환경에 대 한 조건부 액세스 정책 구성 준비 인 경우 문서를 참조 [Azure Active Directory에서 조건부 액세스 모범 사례](best-practices.md)합니다.
