---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: e54301e01d25fb075325ef34522daa5f1b691dd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760665"
---
## <a name="conditional-access-and-claims-challenges"></a>조건부 액세스 및 클레임 챌린지

토큰을 자동으로 가져올 때 액세스하려는 API에서 [조건부 액세스 클레임 챌린지](../articles/active-directory/develop/v2-conditional-access-dev-guide.md)와 같은 MFA 정책이 필요한 경우 애플리케이션에서 오류가 발생할 수 있습니다.

이 오류를 처리하는 패턴은 MSAL을 사용하여 토큰을 대화형으로 획득하는 것입니다. 그러면 사용자에게 메시지가 표시되고 필요한 조건부 액세스 정책을 충족시킬 수 있는 기회가 제공됩니다.

조건부 액세스가 필요한 API를 호출하는 경우 클레임 챌린지를 API의 오류로 받을 수 있습니다. 예를 들어 조건부 액세스 정책에 관리 디바이스(Intune)가 있는 경우 오류는 [AADSTS53000: 이 리소스에 액세스하려면 디바이스가 관리되어야 합니다](../articles/active-directory/develop/reference-aadsts-error-codes.md)이거나 이와 비슷합니다. 이 경우 토큰 획득 호출에서 클레임을 전달하여 사용자에게 적절한 정책을 충족시키라는 메시지가 표시되도록 할 수 있습니다.
