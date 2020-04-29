---
title: 전화 번호 (미리 보기)에 대 한 SMS 로그인 사용자 환경-Azure AD
description: 새 전화 번호 또는 기존 전화 번호에 대 한 SMS 로그인 사용자 환경에 대해 자세히 알아보세요.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378835"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>사용자 이름으로 전화 번호 사용 (미리 보기)

장치를 등록 하면 조직의 서비스에 액세스할 수 있으며 조직에서 휴대폰에 액세스할 수 없습니다. 관리자 인 경우 [SMS 기반 인증에 대 한 사용자 구성 및 사용](../authentication/howto-authentication-sms-signin.md)에서 추가 정보를 확인할 수 있습니다.

조직에서 사용 가능한 SMS 로그인을 하지 않은 경우 사용자 계정에 전화를 등록할 때 해당 로그인에 대 한 옵션이 표시 되지 않습니다.  

## <a name="when-you-have-a-new-phone-number"></a>새 전화 번호

새 전화 번호 또는 새 번호를 받고 SMS 로그인을 사용할 수 있는 조직에 등록 하는 경우 일반 전화 등록 프로세스를 경험할 수 있습니다.

1. **메서드 추가**를 선택 합니다.
1. **휴대폰**을 선택 합니다.
1. 전화 번호를 입력 하 고 **문자 코드를**선택 합니다.
1. 코드를 입력 한 후 **다음**을 선택 합니다.
1. "SMS가 확인 되었습니다. 라는 메시지가 표시 됩니다. 전화를 등록 했습니다. "

> [!Important]
> 미리 보기의 알려진 문제로 인해 짧은 시간 동안 전화 번호를 추가 하면 SMS 로그인에 대 한 번호가 등록 되지 않습니다. 추가 된 번호를 사용 하 여 로그인 한 다음 프롬프트에 따라 SMS 로그인에 대 한 번호를 등록 해야 합니다.

### <a name="when-the-phone-number-is-in-use"></a>전화 번호를 사용 하는 경우

조직의 다른 사용자가 사용 하는 전화 번호를 사용 하려고 하면 다음과 같은 메시지가 표시 됩니다.

![전화 번호가 이미 사용 되는 경우의 오류 메시지](media/sms-sign-in-explainer/sms-sign-in-error.png)

관리자에 게 연락 하 여 문제를 해결 하세요.

## <a name="when-you-have-an-existing-number"></a>기존 숫자가 있는 경우

이미 조직에 전화 번호를 사용 하 고 있고 사용자 이름으로 전화 번호를 사용 하는 경우 다음 단계를 사용 하 여 로그인 할 수 있습니다.

1. SMS 로그인을 사용할 수 있는 경우 SMS 로그인에 전화 번호를 사용 하도록 설정 하 시겠습니까? 라는 배너가 표시 됩니다.

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. 또한 휴대폰 메서드 타일에서 캐럿을 선택 하는 경우 **사용** 단추가 나타납니다.

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. 메서드를 사용 하도록 설정 하려면 **사용**을 선택 합니다. 작업을 확인 하 라는 메시지가 표시 됩니다.

    ![전화 번호에 대해 SMS 로그인을 사용 하도록 설정 하는 확인 대화 상자](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. **사용**을 선택합니다.

## <a name="when-you-remove-your-phone-number"></a>전화 번호를 제거 하는 경우

1. 전화 번호를 삭제 하려면 SMS 로그인 전화 방법 타일에서 삭제 단추를 선택 합니다.

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. 작업을 확인 하 라는 메시지가 표시 되 면 **확인**을 선택 합니다.

기본 로그인 방법으로 사용 중인 전화 번호를 제거할 수 없습니다. 이 번호를 제거 하려면 기본 로그인 방법을 변경 하 고 전화 번호를 다시 제거 해야 합니다.
