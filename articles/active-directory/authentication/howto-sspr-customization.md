---
title: 셀프 서비스 암호 재설정 사용자 지정 - Azure Active Directory
description: Azure AD 셀프 서비스 암호 재설정에 대한 사용자 표시 및 환경 옵션을 사용자 지정하는 방법 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a45602b80db965e3cc79d188dd40034a320b79
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394255"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Azure Active Directory 셀프 서비스 암호 재설정에 대한 사용자 환경 사용자 지정

SSPR(셀프 서비스 암호 재설정)은 Azure Active Directory(Azure AD)의 사용자에게 관리자 나 헬프 데스크의 개입 없이 암호를 변경하거나 재설정할 수 있는 기능을 제공합니다. 사용자 계정이 잠겨 있거나 암호를 잊어버린 경우 프롬프트에 따라 자신을 차단 해제하여 작업을 다시 수행할 수 있습니다. 사용자가 디바이스 또는 애플리케이션에 로그인할 수 없는 경우 이 기능을 통해 지원 센터 호출 및 생산성 저하를 줄일 수 있습니다.

사용자에 대한 SSPR 환경을 개선하려면 암호 재설정 페이지, 전자 메일 알림 또는 로그인 페이지의 모양과 느낌을 사용자 지정할 수 있습니다. 이러한 사용자 지정 옵션을 사용하면 사용자가 올바른 위치에 있음을 분명히 하고 회사 리소스에 액세스하고 있다는 확신을 줄 수 있습니다.
    
이 문서에서는 사용자, 회사 브랜딩 및 AD FS 로그인 페이지 링크를 위해 SSPR 전자 메일 링크를 사용자 지정하는 방법을 보여 주십습니다.

## <a name="customize-the-contact-your-administrator-link"></a>“관리자에게 문의” 링크 사용자 지정

사용자가 셀프 서비스 암호 재설정에 대한 도움을 받을 수 있도록 암호 재설정 포털에 "관리자에게 문의" 링크가 표시됩니다. 사용자가 이 링크를 선택하면 다음 두 가지 중 하나를 수행합니다.

* 이 연락처 링크가 기본 상태로 남아 있으면 관리자에게 전자 메일이 전송되고 사용자의 암호를 변경하는 데 도움을 요청합니다. 다음 샘플 전자 메일에는 이 기본 전자 메일 메시지가 표시됩니다.

    ![관리자에게 보낸 이메일 재설정에 대한 샘플 요청](./media/howto-sspr-customization/sspr-contact-admin.png)

* 사용자 지정한 경우 관리자가 지정한 웹 페이지 또는 전자 메일 주소로 사용자를 보내 도움을 요청합니다.
    * 이 사용자 지정하는 경우 사용자가 이미 지원에 익숙한 것으로 설정하는 것이 좋습니다.

    > [!WARNING]
    > 암호 재설정이 필요한 이메일 주소와 계정으로 이 설정을 사용자 지정하면 사용자가 도움을 요청하지 못할 수 있습니다.

### <a name="default-email-behavior"></a>기본 이메일 동작

기본 연락처 전자 메일은 다음 순서로 받는 사람에게 전송됩니다.

1. *헬프 데스크 관리자* 역할 또는 *암호 관리자* 역할이 할당된 경우 이러한 역할을 가진 관리자에게 알림이 전송됩니다.
1. 헬프 데스크 관리자 또는 암호 관리자가 할당되지 않은 경우 *사용자 관리자* 역할을 가진 관리자에게 알림이 전송됩니다.
1. 이전 역할이 할당되지 않으면 전역 *관리자에게* 알림이 전송됩니다.

어떠한 경우에도 최대 100명의 받는 사람에게 알림이 제공됩니다.

다양한 관리자 역할과 각 역할을 할당하는 방법에 대해 자세히 알아보려면 [Azure Active Directory에서 관리자 역할 할당](../users-groups-roles/directory-assign-admin-roles.md)을 참조하세요.

### <a name="disable-contact-your-administrator-emails"></a>“관리자에게 문의” 메일 사용 안 함

조직에서 암호 재설정 요청에 대해 관리자에게 알리지 않으려면 다음 구성 옵션을 사용할 수 있습니다.

* 사용자가 도움을 받을 수 있는 웹 URL 또는 mailto: 주소를 제공하는 고객 지원 센터 링크를 사용자 지정합니다. 이 옵션은 **암호 재설정** > **사용자 지정** > **사용자 지정 헬프 데스크 전자 메일 또는 URL**아래에 있습니다.
* 모든 사용자에 대해 셀프 서비스 암호 재설정을 사용하도록 설정합니다. 이 옵션은 **암호 재설정** > **속성**아래에 있습니다. 사용자가 자신의 암호를 재설정할 수 없도록 하려면 빈 그룹에 대한 액세스 범위를 지정하면 됩니다. *이 옵션은 권장하지 않습니다.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>로그인 페이지 및 액세스 패널 사용자 지정

회사 브랜딩에 맞는 이미지와 함께 표시되는 로고를 추가하는 등 로그인 페이지를 사용자 지정할 수 있습니다. 회사 브랜딩을 구성하는 방법에 대한 자세한 내용은 [Azure AD의 로그인 페이지에 회사 브랜딩 추가를](../fundamentals/customize-branding.md)참조하십시오.

이미지는 다음과 같은 경우에 표시됩니다.

* 사용자가 사용자 이름을 입력한 뒤
* 사용자가 다음과 같은 방식으로 사용자 지정된 URL에 액세스하는 경우
   * 암호 재설정 `whr` 페이지에 매개 변수를 전달하여`https://login.microsoftonline.com/?whr=contoso.com`
   * 암호 재설정 `username` 페이지에 매개 변수를 전달하여`https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>디렉터리 이름

보다 사용자 친화적인 모양으로 보이도록 포털 및 자동화된 통신에서 조직 이름을 변경할 수 있습니다. Azure 포털에서 디렉터리 이름 특성을 변경하려면 **Azure Active Directory** > **속성**으로 찾아봅니다. 이 친숙한 조직 이름 옵션은 다음 예제와 같이 자동화된 전자 메일에서 가장 많이 표시됩니다.

* 이메일의 친숙한 이름, 예를 들어 "*CONTOSO 데모를 대신하여 마이크로 소프트*"
* 이메일의 제목 줄(예:*"CONTOSO 데모 계정 이메일 확인 코드"*

## <a name="customize-the-ad-fs-sign-in-page"></a>AD FS 로그인 페이지 사용자 지정

사용자 로그인 이벤트에 AD FS(Active Directory Federation Services)를 사용하는 경우 문서의 지침을 사용하여 로그인 페이지 설명을 [추가하여 로그인 페이지에](/windows-server/identity/ad-fs/operations/add-sign-in-page-description)대한 링크를 추가할 수 있습니다.

SSPR 워크플로를 입력할 수 있도록 페이지에 대한 링크를 *https://passwordreset.microsoftonline.com*사용자에게 제공합니다. AD FS 로그인 페이지에 링크를 추가하려면 AD FS 서버에서 다음 명령을 사용합니다.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>다음 단계

사용자 환경에서 SSPR의 사용을 이해하려면 [Azure AD 암호 관리에 대한 보고 옵션을](howto-sspr-reporting.md)참조하십시오.

사용자 또는 사용자가 SSPR에 문제가 있는 경우 [셀프 서비스 암호 재설정 문제를](active-directory-passwords-troubleshoot.md) 참조하십시오.
