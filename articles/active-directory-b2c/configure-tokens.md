---
title: Azure Active Directory B2C 토큰-구성 | Microsoft Docs
description: Azure Active Directory B2C에서 토큰 수명 및 호환성 설정을 구성 하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e1163c88a100ebb7500607475ab5740557904137
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511326"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 토큰을 구성 합니다.

이 문서에서는 구성 하는 방법을 알아봅니다 합니다 [수명 및 토큰의 호환성](active-directory-b2c-reference-tokens.md) Azure Active Directory (Azure AD) B2C에서.

## <a name="prerequisites"></a>필수 조건

사용자가 애플리케이션에 가입하고 로그인할 수 있는 [사용자 흐름을 만듭니다](tutorial-create-user-flows.md).

## <a name="configure-token-lifetime"></a>토큰 수명 구성

사용자 흐름 토큰 수명을 구성할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD B2C 테 넌 트를 포함 하는 디렉터리 사용 했는지 확인 합니다. 선택 된 **디렉터리 및 구독 필터** 최상위 메뉴에서 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 선택 합니다.
3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. 선택 **사용자 흐름 (정책)** 합니다.
5. 이전에 만든 사용자 흐름을 엽니다. 
6. **속성**을 선택합니다.
7. 아래 **토큰 수명**, 응용 프로그램의 요구에 맞게 다음 속성을 조정 합니다.

    ![토큰 수명 구성](./media/configure-tokens/token-lifetime.png)

8. **저장**을 클릭합니다.

## <a name="configure-token-compatibility"></a>토큰 호환성을 구성

1. 선택 **사용자 흐름 (정책)** 합니다.
2. 이전에 만든 사용자 흐름을 엽니다. 
3. **속성**을 선택합니다.
4. 아래 **호환성 설정 토큰**, 응용 프로그램의 요구에 맞게 다음 속성을 조정 합니다.

    ![토큰 호환성을 구성](./media/configure-tokens/token-compatibility.png)

5. **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

[액세스 토큰을 사용하는 방법](active-directory-b2c-access-tokens.md)에 대해 자세히 알아보세요.



