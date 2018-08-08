---
title: 보안 정보를 사용하여 앱 암호 설정 - Azure Active Directory | Microsoft Docs
description: 보안 정보를 사용하여 일반 암호와는 별도로 비 브라우저 앱에 사용하도록 자동 생성된 암호(앱 암호)를 설정합니다.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 56d207dc47b9e055782568f1c4a4cedc4d19a288
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39348210"
---
# <a name="manage-app-passwords-using-security-info-preview"></a>보안 정보를 사용하여 앱 암호 관리(미리 보기)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Outlook 2010과 같은 특정 비 브라우저 앱은 2단계 인증을 지원하지 않습니다. 따라서 2단계 인증을 사용하는 경우 앱이 작동하지 않습니다. 이 문제를 해결하려면 사용자의 일반 암호와는 별도로 자동으로 생성된 암호를 만들어 각 비 브라우저 앱과 함께 사용할 수 있습니다.

앱 암호를 사용하는 경우 다음 사항을 고려해야 합니다.

- 앱 암호는 자동으로 생성되며 앱당 한 번만 입력됩니다.

- 사용자당 40개의 암호로 제한되어 있습니다. 해당 제한을 초과한 후 암호를 만들려고 하면 새 암호를 만들기 전에 기존 암호를 삭제할 것인지를 묻는 메시지가 표시됩니다.

- 앱마다가 아닌 장치마다 하나의 앱 암호를 사용합니다. 예를 들어 랩톱의 모든 앱에 대해 단일 암호를 만든 다음, 데스크톱의 모든 앱에 대해 다른 단일 암호를 만듭니다.

    >[!Note]
    >Outlook을 포함한 Office 2013 클라이언트는 새로운 인증 프로토콜을 지원하며 2단계 인증과 함께 사용할 수 있습니다. 이 지원으로 2단계 인증을 켜고 나면 Office 2013 클라이언트에는 더 이상 앱 암호가 필요하지 않습니다. 자세한 내용은 [Office 2013 및 Office 2016 클라이언트 앱에 대한 최신 인증 작동 방식](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) 문서를 참조하세요.

## <a name="create-and-delete-app-passwords-using-security-info"></a>보안 정보를 사용하여 앱 암호 만들기 및 삭제

회사 또는 학교 계정에 2단계 인증을 사용하고 관리자가 보안 정보 환경을 설정한 경우 My Apps 포털을 사용하여 앱 암호를 만들고 삭제할 수 있습니다.

관리자가 보안 정보 환경을 설정하지 않은 경우 [2단계 인증을 위한 앱 암호 관리](multi-factor-authentication-end-user-app-passwords.md) 섹션의 지침과 정보를 따라야 합니다.

### <a name="to-create-app-passwords-using-the-my-apps-portal"></a>My Apps 포털을 사용하여 앱 암호를 만들려면

1. 회사 또는 학교 계정에 로그인합니다.

2. myapps.microsoft.com으로 이동하고 페이지 오른쪽 위 모서리에서 이름을 선택한 다음, **프로필**을 선택합니다.

3. **계정 관리** 영역에서 **보안 정보 편집**을 선택합니다.

    ![보안 정보 편집 링크가 강조 표시된 프로필 화면](media/security-info/security-info-profile.png)

4. **계정 보안 유지** 화면에서 **보안 정보 추가**를 선택합니다.

    ![편집 가능한 기존 정보가 있는 보안 정보 화면](media/security-info/security-info-edit-add-info.png)

5. **보안 정보 추가** 화면에서 **앱 암호**를 선택합니다.

6. **앱 암호 만들기** 화면에서 앱 암호의 이름을 입력하고 **다음**을 선택합니다.

    ![앱 암호 이름을 지정하는 화면](media/security-info/security-info-name-app-password.png)

7. **복사**를 선택하여 클립보드로 암호를 복사하고, **다음**을 선택합니다.

    ![복사할 앱 암호가 있는 화면](media/security-info/security-info-create-app-password.png)
    
8. **계정 보안 유지** 화면에 앱 암호가 표시되는지 확인합니다.

    ![앱 암호가 표시된 보안 유지 화면](media/security-info/security-info-keep-secure-app-password.png)

### <a name="to-delete-app-passwords-using-the-my-apps-portal"></a>My Apps 포털을 사용하여 앱 암호를 삭제하려면

1. **계정 보안 유지** 화면에서 삭제할 앱 암호 옆에 있는 **X**를 선택합니다.

    ![앱 암호가 삭제된 보안 유지 화면](media/security-info/security-info-keep-secure-delete-app-password.png)

2. **응용 프로그램 암호 삭제** 화면에서 **삭제**를 선택합니다.

    ![앱 암호 삭제 화면](media/security-info/security-info-keep-secure-delete-app-password2.png)

## <a name="next-steps"></a>다음 단계

- 보안 정보를 업데이트해야 하는 경우 [보안 정보 관리](security-info-manage-settings.md) 문서의 지침을 따릅니다.

- 보안 정보 및 수행 가능한 작업에 대한 일반적인 내용은 [보안 정보 개요](user-help-security-info-overview.md)를 참조하세요. 