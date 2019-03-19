---
title: Microsoft Authenticator 앱에 개인 Microsoft 계정 추가 - Azure Active Directory | Microsoft Docs
description: 2단계 인증용으로 Microsoft Authenticator 앱에 Outlook.com 또는 Xbox LIVE와 같은 개인 Microsoft 계정을 추가하는 방법을 설명합니다.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcb49048fa9200b8970f37a4f187ca995d208cc2
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961587"
---
# <a name="add-your-personal-microsoft-accounts"></a>개인 Microsoft 계정 추가
표준 2단계 인증 프로세스 및 암호 없는 휴대폰 로그인 방법에 모두 사용하도록 Microsoft Authenticator 앱에 Outlook.com 또는 Xbox LIVE와 같은 개인 Microsoft 계정을 추가합니다.

- **표준 2단계 인증 방법.** 로그인하는 데 사용하는 디바이스에 사용자 이름 및 암호를 입력한 다음, Microsoft Authenticator 앱에서 알림을 보낼지, 아니면 Microsoft Authenticator 앱의 **계정** 화면에서 연결된 확인 코드를 복사할지 여부를 선택합니다.

- **암호 없는 로그인 방법.** 개인 Microsoft 계정에 로그인하는 데 사용하는 디바이스에 사용자 이름을 입력한 다음, 모바일 디바이스에서 지문, 얼굴 또는 PIN을 사용하여 본인이라는 것을 확인합니다. 이 방법은 암호를 입력할 필요가 없습니다.

>[!Important]
>계정을 추가하려면 Microsoft Authenticator 앱을 다운로드하여 설치해야 합니다. [앱 다운로드 및 설치](user-help-auth-app-download-install.md) 문서의 단계를 아직 진행하지 않은 경우 지금 진행하세요.

## <a name="add-your-personal-microsoft-account"></a>개인 Microsoft 계정을 추가합니다.
먼저 2단계 인증을 설정한 다음, 앱에 계정을 추가하여 개인 Microsoft 계정을 추가할 수 있습니다.

>[!Note]
>개인 Microsoft 계정에 암호 없는 휴대폰 로그인만 사용하려면 2단계 인증을 켤 필요가 없습니다. 그러나 계정 보안을 강화하기 위해 2단계 인증을 켜는 것이 좋습니다.

### <a name="turn-on-two-factor-verification"></a>2단계 인증 설정

1. 컴퓨터에서 [보안 기본 사항](https://account.microsoft.com/security) 페이지로 이동한 다음, 개인 Microsoft 계정을 사용하여 로그인합니다. 예: alain@outlook.com

2. **보안 기본 사항** 페이지 아래쪽에서 **추가 보안 옵션** 링크를 선택합니다.

    ![“추가 보안 옵션” 링크가 강조 표시된 보안 기본 사항 페이지](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. **2단계 인증** 섹션으로 이동하고 기능을 **켜기**로 변경합니다. 기능을 더 이상 개인 계정으로 사용하지 않으려면 여기서 기능을 끌 수도 있습니다.

### <a name="add-your-microsoft-account-to-the-app"></a>앱에 Microsoft 계정 추가

1. 모바일 디바이스에서 Microsoft Authenticator 앱을 엽니다.

2. 오른쪽 위에 있는 **사용자 지정 및 제어** 아이콘에서 **계정 추가**를 선택합니다.

    ![계정 및 제어 아이콘이 강조 표시된 계정 페이지](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. **계정 추가** 페이지에서 **개인 계정**을 선택합니다.

4. 적절한 메일 주소(예: alain@outlook.com)를 사용하여 개인 계정에 로그인한 후 **다음**을 선택합니다.

    >[!Note]
    >개인 Microsoft 계정이 없는 경우 여기서 계정을 만들 수 있습니다.

5. 암호를 입력한 다음, **로그인**을 선택합니다.

    개인 계정이 Microsoft Authenticator 앱에 추가됩니다.

## <a name="next-steps"></a>다음 단계

- 앱에 계정을 추가한 후에는 디바이스에서 Authenticator 앱을 사용하여 로그인할 수 있습니다. 자세한 내용은 [앱을 사용하여 로그인](user-help-auth-app-sign-in.md)을 참조하세요.

- 개인 Microsoft 계정에 대 한 확인 코드를 시작 하는 데 문제가 있는 경우 참조를 **확인 코드 문제 해결** 섹션을 [Microsoft 계정 보안 정보 및 확인 코드](https://support.microsoft.com/en-us/help/12428/microsoft-account-security-info-verification-codes) 문서.

- iOS를 실행하는 디바이스의 경우에는 계정 자격 증명 및 관련 앱 설정(예: 계정 순서)을 클라우드에 백업할 수도 있습니다. 자세한 내용은 [Microsoft Authenticator 앱을 통한 백업 및 복구](user-help-auth-app-backup-recovery.md)를 참조하세요.
