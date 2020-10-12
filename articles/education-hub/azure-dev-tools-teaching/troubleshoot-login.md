---
title: 교육을 위해 Azure Dev Tools에 대 한 로그인 오류 해결
description: 이는 교육을 위해 Azure Dev Tools에 로그인 할 때 학생 들이 오류 메시지를 수신 하는 경우 수행 해야 하는 작업을 설명 합니다.
author: rymend
ms.author: rymend
ms.topic: troubleshooting
ms.service: azure-education
ms.subservice: education-hub
ms.date: 06/30/2020
ms.openlocfilehash: 276ab2cc1d79235c00ce1bb50ff9b648bb1de6ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87097679"
---
# <a name="troubleshooting-student-login-issues"></a>학생 로그인 문제 해결
Azure 개발자 도구에 액세스 하려면 사용자에 게 MSA (Microsoft 계정)가 있어야 합니다. 해당 계정이 아직 msa가 아니거나 MSA에 연결 되지 않은 경우 학생 들이 자동으로 MSA를 만들도록 지시 됩니다. 도메인이 Active Directory와 연결 된 경우 해당 도메인의 모든 계정은 이미 MSA로 간주 됩니다.

학생이 로그인을 시도 하 고 다음 오류 메시지가 수신 되는 경우 아래에 설명 된 솔루션 중 하나를 사용 합니다.

:::image type="content" source="media/troubleshoot-login/error-login-message.png" alt-text="로그인 오류 메시지" border="false":::

새 Microsoft 계정을 만들거나 기존 Microsoft 계정를 사용 하는 두 가지 솔루션이 있습니다.

## <a name="create-a-new-microsoft-account"></a>새 Microsoft 계정 만들기
### <a name="use-a-university-email-address"></a>대학 메일 주소 사용
대학 메일 주소 (예:)를 사용 하 여 로그인 하는 경우 `John.Smith@university.edu` 해당 전자 메일 주소를 사용 하 여 Microsoft 계정를 만들어야 합니다. 계정을 만드는 것은 무료 이며 몇 분 밖에 걸리지 않습니다. Microsoft 계정 있으면 단일 사용자 이름 및 암호를 사용 하 여 모든 Microsoft 제품에 자동으로 로그인 할 수 있습니다.

### <a name="use-a-personal-email-address"></a>개인 전자 메일 주소 사용
개인 전자 메일 주소 (예:)를 사용 하 여 로그인 하 고 `John.Smith@email.com` 대학 메일 주소도 포함 하는 경우 대학 전자 메일 주소를 사용해 보세요. 이전에 개인 메일 주소를 사용 하 여 교육 기관 웹 스토어에 로그인 했거나 대학 메일 주소가 없는 경우 개인 전자 메일 주소를 사용 하 여 Microsoft 계정를 만들거나 연결 해야 합니다.

## <a name="use-an-existing-microsoft-account"></a>기존 Microsoft 계정 사용
학생에 게 기존 Microsoft 계정 (예: Xbox)가 있는 경우 해당 계정을 Azure Dev Tools 계정에 연결할 수 있습니다.

1. https://account.microsoft.com로 이동합니다.
1. Microsoft 계정 자격 증명으로 로그인 합니다.
1. 위쪽 리본 메뉴에서 **정보를** 선택 합니다.

1. **Microsoft에 로그인 하는 방법 관리**를 클릭 합니다. 본인 여부를 확인 하는 메시지가 표시 됩니다. 보안 코드에 전자 메일을 보낼 예정입니다.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in.png" alt-text="로그인 오류 메시지" border="false":::

1. 전자 메일로 전송 되는 보안 코드를 입력 합니다.

    :::image type="content" source="media/troubleshoot-accounts/sign-in-enter-code.png" alt-text="로그인 오류 메시지" border="false":::

1. 계정에 **전자 메일 추가** 를 클릭 하 고 대학 전자 메일 주소를 입력 합니다.
다음에 로그인 할 때 대학 메일 주소를 사용 하 여 Azure 개발자 도구에 액세스할 수 있습니다.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in-add-email.png" alt-text="로그인 오류 메시지" border="false":::

## <a name="next-steps"></a>다음 단계
- [FAQ](program-faq.md)

- [지원 옵션](program-support.md)
