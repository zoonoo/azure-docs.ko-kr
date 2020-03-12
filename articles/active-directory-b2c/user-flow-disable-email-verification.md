---
title: 고객 등록 중에 전자 메일 확인 사용 안 함
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 고객 등록 중에 전자 메일 확인을 사용 하지 않도록 설정 하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 369b4e13baa344a71a51b358ef810d1a66b4b6ae
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126732"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 고객 등록 중에 전자 메일 확인 사용 안 함

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

전자 메일 확인을 사용 하지 않도록 설정 하려면 다음 단계를 따르세요.

1. [Azure 포털](https://portal.azure.com)
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 사용 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **사용자 흐름**을 선택합니다.
1. 전자 메일 확인을 사용 하지 않도록 설정할 사용자 흐름을 선택 합니다. 예를 들어 *B2C_1_signinsignup*합니다.
1. **페이지 레이아웃**을 선택 합니다.
1. **로컬 계정 등록 페이지**를 선택 합니다.
1. **사용자 특성**에서 **전자 메일 주소**를 선택 합니다.
1. **확인 필요** 드롭다운에서 **아니요**를 선택 합니다.
1. **저장**을 선택합니다. 이제이 사용자 흐름에 대해 메일 확인을 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory B2C에서 사용자 인터페이스를 사용자 지정](customize-ui-overview.md) 하는 방법을 알아봅니다.

