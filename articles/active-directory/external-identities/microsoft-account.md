---
title: Azure AD의 Microsoft 계정 (MSA) id 공급자
description: Azure AD를 사용 하 여 외부 사용자 (게스트)가 Microsoft 계정 (MSA)를 사용 하 여 Azure AD 앱에 로그인 할 수 있도록 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d3380277d0e653fd5cb03069b7d91cb363dd95
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693144"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities-preview"></a>외부 Id에 대 한 MSA (Microsoft 계정) id 공급자 (미리 보기)

> [!NOTE]
> Microsoft 계정 id 공급자는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

B2B 게스트 사용자는 추가 구성 없이 B2B 공동 작업에 자신의 개인 Microsoft 계정을 사용할 수 있습니다. 게스트 사용자는 B2B 공동 작업 초대를 교환 하거나 자신의 개인 Microsoft 계정를 사용 하 여 등록 사용자 흐름을 완료할 수 있습니다.

Microsoft 계정은 사용자가 Outlook, OneDrive, Xbox LIVE 또는 Microsoft 365와 같은 소비자 지향 Microsoft 제품 및 클라우드 서비스에 대 한 액세스 권한을 얻기 위해 설정 됩니다. 계정이 만들어지고 Microsoft에서 실행 되는 Microsoft consumer identity 계정 시스템에 저장 됩니다.

## <a name="guest-sign-in-using-microsoft-accounts"></a>Microsoft 계정을 사용 하 여 게스트 로그인

Microsoft 계정은 기본적으로 외부 Id id 공급자 목록에서 사용할 수 있습니다. 게스트 사용자가 초대 흐름이 나 셀프 서비스 등록 사용자 흐름 중 하나를 사용 하 여 Microsoft 계정에 로그인 할 수 있도록 하는 추가 구성은 필요 하지 않습니다.

![Id 공급자 목록의 Microsoft 계정](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>초대 흐름의 Microsoft 계정

B2B 공동 작업에 [게스트 사용자를 초대](add-users-administrator.md) 하는 경우 로그인 하는 데 사용할 전자 메일 주소로 Microsoft 계정를 지정할 수 있습니다.

![Microsoft 계정를 사용 하 여 초대](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>셀프 서비스 등록 사용자 흐름의 Microsoft 계정

Microsoft 계정은 셀프 서비스 등록 사용자 흐름에 대 한 id 공급자 옵션입니다. 사용자는 자신의 Microsoft 계정을 사용 하 여 응용 프로그램에 등록할 수 있습니다. 먼저 테 넌 트에 대해 [셀프 서비스 등록을 사용 하도록 설정](self-service-sign-up-user-flow.md) 해야 합니다. 그런 다음 응용 프로그램에 대 한 사용자 흐름을 설정 하 고 로그인 옵션 중 하나로 Microsoft 계정을 선택할 수 있습니다.

![셀프 서비스 등록 사용자 흐름의 Microsoft 계정](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory B2B 공동 작업 사용자 추가](add-users-administrator.md)
- [앱에 셀프 서비스 등록 추가](self-service-sign-up-user-flow.md)