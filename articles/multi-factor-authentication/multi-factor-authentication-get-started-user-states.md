---
title: "Microsoft Azure Multi-Factor Authentication 사용자 상태"
description: "Azure MFA의 사용자 상태에 대해 알아보세요."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 999361daa2faebe3e88cab0b6085a938d6f40e9d
ms.openlocfilehash: 3079969783f589cb90f17eac116c5d57884db49f
ms.lasthandoff: 02/17/2017


---
# <a name="user-states-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication의 사용자 상태
Azure Multi-Factor Authentication의 사용자 계정은 다음과 같은&3;가지 상태를 갖습니다.

| 시스템 상태 | 설명 | 영향 받는 비브라우저 앱 | 
|:---:|:---:|:---:|
| 사용 안 함 |Azure MFA(Multi-Factor Authentication)에 등록되지 않은 새 사용자에 대한 기본 상태입니다. |아니요 |
| 사용 |사용자가 Azure MFA에 등록되었지만 등록하지 않았습니다. 이 경우 다음에 로그인할 때 등록하라는 메시지가 표시됩니다. |아니요.  등록 프로세스가 완료될 때까지 계속 작업합니다. |
| 적용 |사용자가 등록되었으며 Azure MFA를 위한 등록 프로세스를 완료했습니다. |예.  앱에 앱 암호가 필요합니다. |

## <a name="changing-a-user-state"></a>사용자 상태 변경
사용자의 상태는 관리자가 사용자를 Azure MFA에 등록했는지 그리고 사용자가 등록 프로세스를 완료했는지 여부를 반영합니다.

모든 사용자는 *사용 안 함*으로 시작합니다. Azure MFA에 사용자를 등록하면 상태는 *사용*으로 변경됩니다. 사용된 사용자가 로그인하고 등록 프로세스를 완료하면 상태는 변경 *적용*으로 변경됩니다.  

### <a name="view-user-states"></a>사용자 상태 보기

다음 단계를 통해 사용자 상태를 보고 관리할 수 있는 페이지에 액세스합니다.

1. 관리자 권한으로 [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 왼쪽 창에서 **Active Directory**를 선택합니다.
3. 보려는 사용자의 디렉터리를 선택합니다.
   ![디렉터리 선택 - 스크린샷](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. **사용자**를 선택합니다.
5. 페이지의 아래쪽에서 **Multi-Factor Auth 관리**를 선택합니다. 
   ![다단계 인증 관리 선택 - 스크린샷](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. 사용자 상태를 표시하는 새 탭이 열립니다.
   ![다단계 인증 사용자 상태 - 스크린샷](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-state-from-disabled-to-enabled"></a>상태를 사용 안 함에서 사용으로 변경

1. 이전 단계를 통해 다단계 인증 사용자 페이지로 이동합니다. 
2. Azure MFA에 사용하려는 사용자를 찾습니다. 위쪽에서 보기를 변경해야 할 수 있습니다. 상태가 **사용 안 함**인지 확인합니다.
   ![사용자 찾기 - 스크린샷](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. 이름 옆의 확인란을 선택합니다.
4. 오른쪽의 빠른 단계에 있는 **사용**을 클릭합니다.
   ![선택한 사용자 사용 - 스크린샷](./media/multi-factor-authentication-get-started-cloud/user1.png)
5. **다단계 인증 사용**을 클릭합니다.
   ![다단계 인증 사용 - 스크린샷](./media/multi-factor-authentication-get-started-cloud/enable2.png)
6. 사용자의 상태가 **사용 안 함**에서 **사용**으로 변경됩니다.
   ![사용자 상태가 사용인지 확인 - 스크린샷](./media/multi-factor-authentication-get-started-cloud/user.png)

사용자를 사용으로 설정한 후 전자 메일을 통해 알려야 합니다. 여기에는 다음에 로그인할 때 등록하라는 요청과 일부 비 브라우저 앱이&2;단계 인증으로 작동하지 않을 수 있다는 내용이 포함됩니다. 또한 사용자가 시작할 수 있도록 [Azure MFA 최종 사용자 가이드](./end-user/multi-factor-authentication-end-user.md)에 연결되는 링크를 포함할 수도 있습니다. 

### <a name="to-change-the-state-from-enabledenforced-to-disabled"></a>상태를 사용/적용에서 사용 안 함으로 변경하려면

1. [사용자 상태 보기](#view-user-states)의 단계를 통해 다단계 인증 사용자 페이지로 이동합니다.
6. 사용하지 않도록 설정하려는 사용자를 찾습니다. 위쪽에서 보기를 변경해야 할 수 있습니다. 상태가 **사용** 또는 **적용**인지 확인합니다.
7. 이름 옆의 확인란을 선택합니다.
8. 오른쪽의 빠른 단계에 있는 **사용 안 함**을 클릭합니다.
   ![사용자 사용 안 함 - 스크린샷](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
9. 작업을 확인하라는 메시지가 표시됩니다. **예**를 클릭합니다.
10. 사용자가 성공적으로 사용하지 않도록 설정된 경우 작업 성공 메시지를 받게 됩니다. **닫기**를 클릭합니다.


