---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184358"
---
Azure AD B2C 테넌트에 애플리케이션을 등록하기 위해 현재 **애플리케이션** 환경 또는 새로운 통합 **앱 등록(미리 보기)** 환경을 사용할 수 있습니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[애플리케이션](#tab/applications/)

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure Active Directory**를 검색하고 선택합니다.
1. **관리에서** **앱 등록(레거시)을 선택합니다.**
1. **새 애플리케이션 등록**을 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들어, *관리app1*.
1. **응용 프로그램 유형의**경우 **웹 앱 / API를**선택합니다.
1. 로그인 URL에 유효한 **URL을**입력합니다. `https://localhost`)을 입력합니다. 끝점에 연결할 필요는 없지만 유효한 URL이어야 합니다.
1. **만들기**를 선택합니다.
1. **등록된 앱** 개요 페이지에 표시되는 응용 **프로그램 ID를** 기록합니다. 이후 단계에서 이 값을 사용합니다.

#### <a name="app-registrations-preview"></a>[앱 등록(미리 보기)](#tab/app-reg-preview/)

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure 포털에서 **Azure AD B2C를**검색하고 선택합니다.
1. **앱 등록(미리 보기)** 을 선택한 다음, **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예를 들어, *관리app1*.
1. **이 조직 디렉터리에서만 계정을**선택합니다.
1. **사용 권한에서** *권한 부여 관리자동의를 지우고 권한 offline_access 확인란을 엽니다.*
1. **등록을**선택합니다.
1. 응용 프로그램 개요 페이지에 나타나는 **응용 프로그램(클라이언트) ID를** 기록합니다. 이후 단계에서 이 값을 사용합니다.