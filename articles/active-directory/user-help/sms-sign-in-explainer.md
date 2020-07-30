---
title: 전화 번호를 사용하는 SMS 로그인 사용자 환경(미리 보기) - Azure AD
description: 새로운 또는 기존 전화 번호를 사용하는 SMS 로그인 사용자 환경에 대해 알아봅니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 864225238c10ab2fda96e95448790201cc8a16ae
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423000"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>전화 번호를 사용자 이름으로 사용(미리 보기)

전화를 등록하면 전화에서 조직의 서비스에 액세스할 수 있으며 조직에서는 전화에 액세스할 수 없습니다. 관리자는 [SMS 기반 인증용 사용자 구성 및 사용](../authentication/howto-authentication-sms-signin.md)에서 자세한 내용을 확인할 수 있습니다.

조직에서 SMS 로그인을 사용하도록 설정하지 않은 경우, 계정을 사용하여 전화를 등록할 때 이 옵션이 표시되지 않습니다.  

## <a name="when-you-have-a-new-phone-number"></a>새 전화 번호가 있는 경우

새 전화 또는 새 번호를 SMS 로그인을 사용할 수 있는 조직에 등록하면 다음과 같은 일반적인 전화 등록 프로세스를 거치게 됩니다.

1. **방법 추가**를 선택합니다.
1. **전화**를 선택합니다.
1. 전화 번호를 입력하고 **코드를 문자로 받기**를 선택합니다.
1. 코드를 입력한 후 **다음**을 선택합니다.
1. 다음과 같은 메시지가 표시됩니다. “SMS가 확인되었습니다. 휴대폰이 등록되었습니다.”

> [!Important]
> 미리 보기의 알려진 문제로 인해 전화 번호를 추가해도 짧은 시간 동안은 해당 번호가 SMS 로그인용으로 등록되지 않습니다. 추가한 번호를 사용하여 로그인한 다음 메시지에 따라 SMS 로그인용으로 번호를 등록해야 합니다.

### <a name="when-the-phone-number-is-in-use"></a>전화 번호가 사용 중인 경우

조직의 다른 사용자가 사용 중인 전화 번호를 사용하려고 시도할 경우 다음 메시지가 표시됩니다.

![전화 번호가 이미 사용 중인 경우 표시되는 오류 메시지](media/sms-sign-in-explainer/sms-sign-in-error.png)

관리자에게 문의하여 문제를 해결하세요.

## <a name="when-you-have-an-existing-number"></a>기존 번호가 있는 경우

조직에서 사용 중인 전화 번호가 있으며 전화 번호를 사용자 이름으로 사용할 수 있게 될 경우, 다음 단계에 따라 로그인할 수 있습니다.

1. SMS 로그인을 사용할 수 있게 되면 전화 번호를 SMS 로그인용으로 사용할 것인지 묻는 배너가 표시됩니다.

    [![전화 번호에 대해 SMS 로그인을 사용 하도록 설정 하는 배너입니다.](media/sms-sign-in-explainer/sms-sign-in-banner.png)](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. 또한, 전화 방법 타일에서 캐럿을 선택하면 **사용** 단추가 표시됩니다.

    [![전화 번호에 대해 SMS 로그인을 사용 하도록 설정 하는 배너입니다.](media/sms-sign-in-explainer/sms-sign-in-phone-method.png)](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. 이 방법을 사용하도록 설정하려면 **사용**을 선택합니다. 작업을 확인하라는 메시지가 표시됩니다.

    ![전화 번호를 SMS 로그인용으로 사용하도록 설정하는 확인 대화 상자](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. **사용**을 선택합니다.

## <a name="when-you-remove-your-phone-number"></a>전화 번호를 제거하는 경우

1. 전화 번호를 삭제하려면 SMS 로그인 전화 방법 타일에서 삭제 단추를 선택합니다.

    [![전화 번호에 대 한 SMS 로그인을 삭제 하는 배너입니다.](media/sms-sign-in-explainer/sms-sign-in-delete-method.png)](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. 작업을 확인하라는 메시지가 표시되면 **확인**을 선택합니다.

기본 로그인 방법으로 사용 중인 전화 번호는 제거할 수 없습니다. 이 번호를 제거하려면 기본 로그인 방법을 변경한 다음 다시 전화 번호를 제거해야 합니다.
