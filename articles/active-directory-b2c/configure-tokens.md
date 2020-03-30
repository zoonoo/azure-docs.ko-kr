---
title: 토큰 구성 - Azure Active Directory B2C | 마이크로 소프트 문서
description: Azure Active Directory B2C에서 토큰 수명 및 호환성 설정을 구성하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 160898f8535d3dad4811af016ebca779b7ef43a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189620"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 토큰 구성

이 문서에서는 Azure Active Directory B2C(Azure AD B2C)에서 [토큰의 수명 및 호환성을](tokens-overview.md) 구성하는 방법을 배웁니다.

## <a name="prerequisites"></a>사전 요구 사항

사용자가 애플리케이션에 가입하고 로그인할 수 있는 [사용자 흐름을 만듭니다](tutorial-create-user-flows.md).

## <a name="configure-token-lifetime"></a>토큰 수명 구성

모든 사용자 흐름에서 토큰 수명을 구성할 수 있습니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 테넌트를 포함하는 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **사용자 흐름(정책)을 선택합니다.**
5. 이전에 만든 사용자 흐름을 엽니다.
6. **속성**을 선택합니다.
7. **토큰 수명**에서 응용 프로그램의 요구에 맞게 다음 속성을 조정합니다.

    ![Azure 포털의 토큰 수명 속성 설정](./media/configure-tokens/token-lifetime.png)

8. **저장**을 클릭합니다.

## <a name="configure-token-compatibility"></a>토큰 호환성 구성

1. **사용자 흐름(정책)을 선택합니다.**
2. 이전에 만든 사용자 흐름을 엽니다.
3. **속성**을 선택합니다.
4. **토큰 호환성 설정에서**응용 프로그램의 요구에 맞게 다음 속성을 조정합니다.

    ![Azure 포털의 토큰 호환성 속성 설정](./media/configure-tokens/token-compatibility.png)

5. **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

[액세스 토큰을 사용하는 방법](access-tokens.md)에 대해 자세히 알아보세요.



