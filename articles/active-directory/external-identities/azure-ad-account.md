---
title: Azure AD 계정 ID 공급자
description: Azure Active Directory를 사용하여 외부 사용자(게스트)가 해당 Azure AD 회사 계정으로 Azure AD 앱에 로그인할 수 있도록 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d9d7a8e74767982d5089a1308ca36108eb5f106
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111972549"
---
# <a name="azure-active-directory-azure-ad-identity-provider-for-external-identities"></a>외부 ID에 대한 Azure AD(Azure Active Directory) ID 공급자

Azure Active Directory는 기본적으로 B2B 협업을 위한 ID 공급자 옵션으로 사용할 수 있습니다. 외부 게스트 사용자가 직장 또는 학교를 통해 Azure AD 계정을 가지고 있는 경우 B2B 협업 초대를 사용하거나 Azure AD 계정을 사용하여 등록 사용자 흐름을 완료할 수 있습니다.

## <a name="guest-sign-in-using-azure-active-directory-accounts"></a>Azure Active Directory 계정을 사용하여 게스트 로그인

Azure Active Directory는 기본적으로 외부 ID의 ID 공급자 목록에서 사용할 수 있습니다. 초대 흐름이나 셀프 서비스 등록 사용자 흐름을 사용하여 게스트 사용자가 Azure AD 계정에 로그인하기 위하여 추가로 구성할 필요가 없습니다.

![ID 공급자 목록의 Azure AD 계정](media/azure-ad-account/azure-ad-account-identity-provider.png)

### <a name="azure-ad-account-in-the-invitation-flow"></a>초대 흐름의 Azure AD 계정

B2B 협업에 [게스트 사용자를 초대](add-users-administrator.md)할 때 로그인에 사용할 이메일 주소로 해당 Azure AD 계정을 지정할 수 있습니다.

![Azure AD 계정을 사용하여 초대](media/azure-ad-account/azure-ad-account-invite.png)

### <a name="azure-ad-account-in-self-service-sign-up-user-flows"></a>셀프 서비스 등록 사용자 흐름의 Azure AD 계정

Azure AD 계정은 셀프 서비스 등록 사용자 흐름에 대한 ID 공급자 옵션 중 하나입니다. 사용자는 자신의 Azure AD 계정을 사용하여 내 애플리케이션에 등록할 수 있습니다. 먼저 테넌트에 대하여 [셀프 서비스 등록을 사용하도록 설정](self-service-sign-up-user-flow.md)하여야 합니다. 그런 다음 해당 애플리케이션을 위한 사용자 흐름을 설정하고 Azure Active Directory를 로그인 옵션 중 하나로 선택합니다.

![셀프 서비스 등록 사용자 흐름의 Azure AD 계정](media/azure-ad-account/azure-ad-account-user-flow.png)

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory B2B 협업 사용자 추가](add-users-administrator.md)
- [앱에 셀프 서비스 등록 추가](self-service-sign-up-user-flow.md)