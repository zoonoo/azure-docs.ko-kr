---
title: 네이티브 클라이언트 애플리케이션 추가 - Azure Active Directory B2C | Microsoft Docs
description: Active Directory B2C 테넌트에 네이티브 클라이언트 애플리케이션을 추가하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b4e9b95cb226aeec686816d0ed7160062e110c62
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511831"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Active Directory B2C 테넌트에 네이티브 클라이언트 애플리케이션 추가

애플리케이션이 Azure Active Directory B2C와 통신하려면 네이티브 클라이언트 리소스를 테넌트에 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **응용 프로그램**을 선택하고 **추가**를 선택합니다.
2. 애플리케이션의 이름을 입력합니다. 예를 들어 *nativeapp1*과 같습니다.
3. **웹앱/웹 API 포함**에 **아니요**를 선택합니다.
4. **원시 클라이언트 포함**에 **예**를 선택합니다.
5. **리디렉션 URI**에 사용자 지정 체계를 사용하는 유효한 리디렉션 URI를 입력합니다. 리디렉션 URI를 선택하는 경우 다음과 같은 두 가지 중요한 고려 사항이 있습니다.

    - **고유** - 리디렉션 URI의 체계는 모든 애플리케이션에 대해 고유해야 합니다. 보기 `com.onmicrosoft.contoso.appname://redirect/path`에서 `com.onmicrosoft.contoso.appname`은 구성표입니다. 이 패턴을 따라야 합니다. 두 애플리케이션이 동일한 체계를 공유하는 경우 애플리케이션을 선택하기 위한 옵션이 제공됩니다. 사용자가 잘못된 항목을 선택하면 로그인이 실패합니다.
    - **전체** - 리디렉션 URI에는 체계 및 경로가 있어야 합니다. 경로는 도메인 다음에 하나 이상의 포워드 슬래시를 포함해야 합니다. 예를 들어 `//contoso/`는 실행되고 `//contoso`는 실행되지 않습니다. 리디렉션 URI가 밑줄 등의 특수 문자를 포함하지 않는지 확인합니다.

6. **만들기**를 클릭합니다.
7. 속성 페이지에서 네이티브 클라이언트 애플리케이션을 구성할 때 사용할 애플리케이션 ID를 기록합니다.
