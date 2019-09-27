---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/27/2019
ms.author: marsma
ms.openlocfilehash: b83a6b9185eb4ef127da1dd3b55aba4e8b2945f9
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326296"
---
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테 넌 트가 포함 된 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택 합니다. 또는 **모든 서비스** 를 선택 하 고 **Azure AD B2C**을 검색 하 고 선택 합니다.
1. **애플리케이션**을 선택하고 **추가**를 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들어 *nativeapp1*과 같습니다.
1. **Native client**의 경우 **예**를 선택 합니다.
1. 고유한 스키마를 사용 하 여 **사용자 지정 리디렉션 URI** 를 입력 합니다. 예를 들어, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`을 입력합니다. 리디렉션 URI를 선택하는 경우 다음과 같은 두 가지 중요한 고려 사항이 있습니다.
    1. **고유**: 리디렉션 URI의 체계는 모든 응용 프로그램에 대해 고유 해야 합니다. 보기 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`에서 `com.onmicrosoft.contosob2c.exampleapp`은 구성표입니다. 이 패턴을 따라야 합니다. 두 애플리케이션이 동일한 체계를 공유하는 경우 애플리케이션을 선택하기 위한 옵션이 제공됩니다. 사용자가 잘못 선택 하는 경우 로그인이 실패 합니다.
    1. **전체**: 리디렉션 URI에는 체계와 경로가 둘 다 있어야 합니다. 경로는 도메인 다음에 하나 이상의 포워드 슬래시를 포함해야 합니다. 예를 들어 `//oauth/`은 `//oauth`이 실패 합니다. URI에 특수 문자 (예: 밑줄)를 포함 하지 않습니다.
1. **만들기**를 선택합니다.
