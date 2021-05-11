---
title: Azure AD의 MSA(Microsoft 계정) ID 공급자
description: Azure AD를 사용하여 외부 사용자(게스트)가 자신의 MSA(Microsoft 계정)를 통하여 내 Azure AD 앱에 로그인할 수 있도록 합니다.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693144"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities-preview"></a>외부 ID에 대한 MSA(Microsoft 계정) ID 공급자(미리 보기)

> [!NOTE]
> Microsoft 계정 ID 공급자는 Azure Active Directory의 퍼블릭 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

B2B 게스트 사용자는 자신들의 B2B 협업용 개인 Microsoft 계정을 추가 구성 없이 사용할 수 있습니다. 게스트 사용자는 내 B2B 협업 초대를 사용하거나 개인 Microsoft 계정을 사용하여 등록 사용자 흐름을 완료할 수 있습니다.

Microsoft 계정은 Outlook이나 OneDrive, Xbox LIVE 또는 Microsoft 365와 같은 소비자 중심적 Microsoft 제품 및 클라우드 서비스에 액세스하기 위하여 사용자가 설정합니다. 이 계정은 Microsoft에서 실행하는 Microsoft 소비자 ID 계정 시스템에서 생성되어 저장됩니다.

## <a name="guest-sign-in-using-microsoft-accounts"></a>Microsoft 계정을 사용한 게스트 로그인

Microsoft 계정은 기본적으로 외부 ID의 ID 공급자 목록에서 사용할 수 있습니다. 초대 흐름이나 셀프 서비스 등록 사용자 흐름을 사용하여 게스트 사용자가 Microsoft 계정에 로그인하기 위하여 추가로 구성할 필요가 없습니다.

![ID 공급자 목록의 Microsoft 계정](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>초대 흐름의 Microsoft 계정

B2B 협업을 위하여 [게스트 사용자를 초대](add-users-administrator.md)하는 경우, 이들의 Microsoft 계정을 이들이 로그인할 때 사용할 이메일 주소로 지정할 수 있습니다.

![Microsoft 계정을 통하여 초대하기](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>셀프 서비스 등록 사용자 흐름의 Microsoft 계정

Microsoft 계정은 셀프 서비스 등록 사용자 흐름에 대한 ID 공급자 옵션 중 하나입니다. 사용자는 자신의 Microsoft 계정을 사용하여 내 애플리케이션에 등록할 수 있습니다. 먼저 테넌트에 대하여 [셀프 서비스 등록을 사용하도록 설정](self-service-sign-up-user-flow.md)하여야 합니다. 그런 다음 해당 애플리케이션을 위한 사용자 흐름을 설정하고 Microsoft 계정을 로그인 옵션 가운데 하나로 선택합니다.

![셀프 서비스 등록 사용자 흐름의 Microsoft 계정](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory B2B 협업 사용자 추가](add-users-administrator.md)
- [앱에 셀프 서비스 등록 추가](self-service-sign-up-user-flow.md)