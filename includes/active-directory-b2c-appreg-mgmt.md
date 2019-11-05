---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: baf39c1fe72703d8ebc18b03bae1c963536d7ced
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475208"
---
Azure AD B2C 테 넌 트에 응용 프로그램을 등록 하려면 현재 **응용 프로그램** 환경 또는 새로운 통합 **앱 등록 (미리 보기)** 환경을 사용할 수 있습니다. [미리 보기 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[애플리케이션](#tab/applications/)

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테 넌 트가 포함 된 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure Active Directory** (Azure AD B2C*아님* )를 선택 합니다. 또는 **모든 서비스** 를 선택한 다음 **Azure Active Directory**을 검색 하 고 선택 합니다.
1. **관리**에서 **앱 등록 (레거시)** 를 선택 합니다.
1. **새 애플리케이션 등록**을 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들면 *managementapp1*입니다.
1. **응용 프로그램 유형**으로 **웹 앱/** a p i를 선택 합니다.
1. **로그온 url**에 유효한 url을 입력 합니다. 예: `https://localhost` 끝점에 연결할 필요는 없지만 유효한 URL 이어야 합니다.
1. **만들기**를 선택합니다.
1. **등록 된 앱** 개요 페이지에 표시 되는 **응용 프로그램 ID** 를 기록 합니다. 이후 단계에서이 값을 사용 합니다.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[앱 등록 (미리 보기)](#tab/app-reg-preview/)

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테 넌 트가 포함 된 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택 합니다. 또는 **모든 서비스** 를 선택 하 고 **Azure AD B2C**을 검색 하 고 선택 합니다.
1. **앱 등록 (미리 보기)** 를 선택 하 고 **새 등록**을 선택 합니다.
1. 응용 프로그램의 **이름을** 입력 합니다. 예를 들면 *managementapp1*입니다.
1. **이 조직 디렉터리 에서만 계정을**선택 하십시오.
1. **사용 권한**아래에서 *openid connect and offline_access Permissions에 관리자 동의 부여* 확인란의 선택을 취소 합니다.
1. **등록**을 선택합니다.
1. 응용 프로그램 개요 페이지에 표시 되는 **응용 프로그램 (클라이언트) ID** 를 기록 합니다. 이후 단계에서이 값을 사용 합니다.