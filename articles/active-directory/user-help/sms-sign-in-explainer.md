---
title: 전화 번호에 대한 SMS 로그인 사용자 환경(미리 보기) - Azure AD
description: 신규 또는 기존 전화 번호에 대한 SMS 로그인 사용자 경험에 대해 자세히 알아보기
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e5369247fc1e31bafb158e83a54bf8badf721d0d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378835"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>전화번호를 사용자 이름으로 사용(미리 보기)

장치를 등록하면 휴대폰에서 조직의 서비스에 액세스할 수 있으며 조직에서 휴대폰에 액세스할 수 없습니다. 관리자인 경우 구성에서 자세한 정보를 찾고 [사용자가 SMS 기반 인증을 사용하도록 설정할](../authentication/howto-authentication-sms-signin.md)수 있습니다.

조직에서 SMS 로그인을 사용할 수 없게 된 경우 계정에 휴대폰을 등록할 때 이에 대한 옵션이 표시되지 않습니다.  

## <a name="when-you-have-a-new-phone-number"></a>새 전화번호가 있는 경우

새 전화 또는 새 번호를 받고 SMS 로그인을 사용할 수 있는 조직에 등록하는 경우 일반적인 전화 등록 프로세스가 발생합니다.

1. **메서드 추가를**선택합니다.
1. **전화**를 선택합니다.
1. 전화 번호를 입력하고 **나에게 코드를 텍스트**를 선택합니다.
1. 코드를 입력한 후 **다음**을 선택합니다.
1. "SMS가 확인되었습니다. 휴대 전화가 성공적으로 등록되었습니다."

> [!Important]
> 미리 보기에서 알려진 문제로 인해 짧은 시간 동안 전화 번호를 추가하면 SMS 로그인 번호가 등록되지 않습니다. 추가된 번호로 로그인한 다음 프롬프트에 따라 SMS 로그인 번호를 등록해야 합니다.

### <a name="when-the-phone-number-is-in-use"></a>전화 번호가 사용 중일 때

조직의 다른 사용자가 사용하는 전화 번호를 사용하려고 하면 다음 메시지가 표시됩니다.

![전화 번호가 이미 사용 중일 때 오류 메시지](media/sms-sign-in-explainer/sms-sign-in-error.png)

관리자에게 문의하여 문제를 해결합니다.

## <a name="when-you-have-an-existing-number"></a>기존 번호가 있는 경우

이미 기관에서 전화 번호를 사용하고 있고 전화 번호를 사용자 이름으로 사용하는 경우 다음 단계를 통해 로그인하는 데 도움이 될 수 있습니다.

1. SMS 로그인을 사용할 수 있는 경우 SMS 로그인에 대 한 전화 번호를 사용 하려는 경우 묻는 배너가 표시 됩니다.

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. 또한 전화 메서드 타일에서 캐런을 선택하면 **활성화** 버튼이 나타납니다.

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. 메서드를 사용하려면 **을 선택합니다.** 작업을 확인하라는 메시지가 표시됩니다.

    ![전화 번호에 대한 SMS 로그인을 활성화하는 확인 대화 상자](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. **사용**을 선택합니다.

## <a name="when-you-remove-your-phone-number"></a>전화번호를 삭제하는 경우

1. 전화 번호를 삭제하려면 SMS 로그인 전화 메서드 타일에서 삭제 단추를 선택합니다.

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. 작업을 확인하라는 메시지가 표시되면 **확인을**선택합니다.

기본 로그인 방법으로 사용 중이면 전화 번호를 제거할 수 없습니다. 번호를 제거하려면 기본 로그인 방법을 변경한 다음 전화 번호를 다시 제거해야 합니다.
