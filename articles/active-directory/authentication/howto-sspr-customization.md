---
title: 셀프 서비스 암호 재설정 사용자 지정-Azure Active Directory
description: Azure AD 셀프 서비스 암호 재설정에 대 한 사용자 표시 및 경험 옵션을 사용자 지정 하는 방법 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bef201cbd14a4de0c785b5f86d534f27bfedb31
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92363711"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>셀프 서비스 암호 재설정 Azure Active Directory 사용자 환경 사용자 지정

SSPR (셀프 서비스 암호 재설정)는 관리자 또는 지원 센터의 개입 없이 암호를 변경 하거나 다시 설정 하는 기능을 Azure Active Directory (Azure AD)의 사용자에 게 제공 합니다. 사용자 계정이 잠겨 있거나 암호를 잊어버린 경우 프롬프트에 따라 자신을 차단 해제하여 작업을 다시 수행할 수 있습니다. 사용자가 디바이스 또는 애플리케이션에 로그인할 수 없는 경우 이 기능을 통해 지원 센터 호출 및 생산성 저하를 줄일 수 있습니다.

사용자를 위한 SSPR 환경을 개선 하기 위해 암호 재설정 페이지, 전자 메일 알림 또는 로그인 페이지의 모양과 느낌을 사용자 지정할 수 있습니다. 이러한 사용자 지정 옵션을 사용 하면 적절 한 위치의 사용자를 명확 하 게 지정 하 고 회사 리소스에 액세스 하는 것을 확신할 수 있습니다.
    
이 문서에서는 사용자, 회사 브랜딩 및 AD FS 로그인 페이지 링크에 대 한 SSPR 전자 메일 링크를 사용자 지정 하는 방법을 보여 줍니다.

## <a name="customize-the-contact-your-administrator-link"></a>“관리자에게 문의” 링크 사용자 지정

사용자가 셀프 서비스 암호 재설정에 대 한 도움을 받을 수 있도록 "관리자에 게 문의" 링크가 암호 재설정 포털에 표시 됩니다. 사용자가이 링크를 선택 하는 경우 다음 두 가지 중 하나를 수행 합니다.

* 이 연락처 링크가 기본 상태로 유지 되 면 관리자에 게 전자 메일이 전송 되 고 사용자 암호를 변경 하는 데 도움을 요청 하는 메시지가 표시 됩니다. 다음 샘플 전자 메일은이 기본 전자 메일 메시지를 보여 줍니다.

    ![관리자에 게 보낸 전자 메일 재설정 요청 샘플](./media/howto-sspr-customization/sspr-contact-admin.png)

* 사용자 지정 하는 경우 지원을 위해 관리자가 지정한 웹 페이지 또는 전자 메일 주소로 사용자를 보냅니다.
    * 이를 사용자 지정 하는 경우 사용자가 지원을 위해 이미 익숙한 항목으로 설정 하는 것이 좋습니다.

    > [!WARNING]
    > 암호를 재설정 해야 하는 전자 메일 주소 및 계정을 사용 하 여이 설정을 사용자 지정 하는 경우 사용자가 도움을 요청 하지 못할 수 있습니다.

### <a name="default-email-behavior"></a>기본 전자 메일 동작

기본 연락처 전자 메일은 다음 순서로 받는 사람에 게 전송 됩니다.

1. *기술 지원팀 관리자* 역할 또는 *암호 관리자* 역할이 할당 된 경우 이러한 역할이 있는 관리자에 게 알림이 제공 됩니다.
1. 기술 지원팀 관리자 또는 암호 관리자가 할당 되지 않은 경우 *사용자 관리자* 역할을 가진 관리자에 게 알림이 제공 됩니다.
1. 이전 역할이 할당 되지 않은 경우 *전역 관리자* 에 게 알림이 제공 됩니다.

어떠한 경우에도 최대 100명의 받는 사람에게 알림이 제공됩니다.

다양한 관리자 역할과 각 역할을 할당하는 방법에 대해 자세히 알아보려면 [Azure Active Directory에서 관리자 역할 할당](../roles/permissions-reference.md)을 참조하세요.

### <a name="disable-contact-your-administrator-emails"></a>“관리자에게 문의” 메일 사용 안 함

조직에서 관리자에 게 암호 재설정 요청을 알리지 않으려는 경우 다음 구성 옵션을 사용할 수 있습니다.

* 사용자가 도움을 받을 수 있는 웹 URL 또는 mailto: 주소를 제공하는 고객 지원 센터 링크를 사용자 지정합니다. 이 옵션은 **암호 재설정**  >  **Customization**  >  **사용자 지정 기술 지원팀 전자 메일 또는 URL**아래에 있습니다.
* 모든 사용자에 대해 셀프 서비스 암호 재설정을 사용 하도록 설정 합니다. 이 옵션은 **암호 재설정**  >  **속성**아래에 있습니다. 사용자가 자신의 암호를 재설정할 수 없도록 하려면 빈 그룹에 대한 액세스 범위를 지정하면 됩니다. *이 옵션은 권장 되지 않습니다.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>로그인 페이지 및 액세스 패널 사용자 지정

등의 로그인 페이지를 사용자 지정 하 여 회사 브랜딩을 충족 하는 이미지와 함께 표시 되는 로고를 추가할 수 있습니다. 회사 브랜딩을 구성 하는 방법에 대 한 자세한 내용은 [AZURE AD의 로그인 페이지에 회사 브랜딩 추가](../fundamentals/customize-branding.md)를 참조 하세요.

이미지는 다음과 같은 경우에 표시됩니다.

* 사용자가 사용자 이름을 입력한 뒤
* 사용자가 다음과 같은 방식으로 사용자 지정된 URL에 액세스하는 경우
   * `whr`다음과 같이 매개 변수를 암호 재설정 페이지에 전달 합니다.`https://login.microsoftonline.com/?whr=contoso.com`
   * `username`다음과 같이 매개 변수를 암호 재설정 페이지에 전달 합니다.`https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>디렉터리 이름

사용자에 게 더 친숙 하 게 보이도록 포털 및 자동화 된 통신에서 조직 이름을 변경할 수 있습니다. Azure Portal에서 디렉터리 이름 특성을 변경 하려면 **Azure Active Directory**  >  **속성**으로 이동 합니다. 이러한 친숙 한 조직 이름 옵션은 다음 예제와 같이 자동화 된 전자 메일에서 가장 잘 표시 됩니다.

* 전자 메일의 이름 (예: "*CONTOSO 데모를 대신*하는 Microsoft")
* 전자 메일의 제목 줄 (예: "*CONTOSO 데모 계정 전자 메일 확인 코드*")

## <a name="customize-the-ad-fs-sign-in-page"></a>AD FS 로그인 페이지 사용자 지정

사용자 로그인 이벤트에 Active Directory Federation Services (AD FS)를 사용 하는 경우 로그인 페이지 [설명 추가](/windows-server/identity/ad-fs/operations/add-sign-in-page-description)에 대 한 문서에 있는 지침을 사용 하 여 로그인 페이지에 대 한 링크를 추가할 수 있습니다.

SSPR 워크플로를 입력할 수 있도록 사용자에 게 페이지에 대 한 링크를 제공 합니다 (예:) *https://passwordreset.microsoftonline.com* . AD FS 로그인 페이지에 대 한 링크를 추가 하려면 AD FS 서버에서 다음 명령을 사용 합니다.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>다음 단계

사용자 환경에서 SSPR의 사용을 이해 하려면 [AZURE AD 암호 관리에 대 한 보고 옵션](howto-sspr-reporting.md)을 참조 하세요.

사용자 또는 사용자가 SSPR에 문제가 있는 경우 [셀프 서비스 암호 재설정 문제 해결](active-directory-passwords-troubleshoot.md) 을 참조 하세요.
