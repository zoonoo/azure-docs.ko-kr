---
title: 고객 가입 시 이메일 확인 을 사용하지 않도록 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 고객 등록 중에 전자 메일 확인을 사용하지 않도록 설정하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126732"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 고객 등록 중 전자 메일 확인을 사용하지 않도록 설정

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

다음 단계에 따라 이메일 확인을 사용하지 않도록 설정합니다.

1. [Azure 포털에](https://portal.azure.com) 로그인
1. 최상위 메뉴의 **디렉터리 + 구독** 필터를 사용하여 Azure AD B2C 테넌트를 포함하는 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **사용자 흐름**을 선택합니다.
1. 이메일 확인을 사용하지 않도록 설정할 사용자 흐름을 선택합니다. 예를 들어, *B2C_1_signinsignup*.
1. **페이지 레이아웃을 선택합니다.**
1. **로컬 계정 등록 페이지를**선택합니다.
1. 사용자 속성에서 **전자 메일 주소를**선택합니다. **User attributes**
1. 확인 **필요** 드롭다운에서 **아니요를**선택합니다.
1. **저장**을 선택합니다. 이제 이 사용자 흐름에 대해 전자 메일 확인이 비활성화되었습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory B2C에서 사용자 인터페이스를 사용자 지정하는](customize-ui-overview.md) 방법에 대해 알아봅니다.

