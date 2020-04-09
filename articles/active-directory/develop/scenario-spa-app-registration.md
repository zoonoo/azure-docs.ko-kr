---
title: 단일 페이지 앱 등록 - Microsoft ID 플랫폼 | Azure
description: 단일 페이지 응용 프로그램(앱 등록)을 빌드하는 방법 알아보기
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 6f690a8b3436a45d434ccad2bbaa7d2a1b0b76aa
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882151"
---
# <a name="single-page-application-app-registration"></a>단일 페이지 응용 프로그램: 앱 등록

이 페이지에서는 단일 페이지 응용 프로그램(SPA)에 대한 앱 등록 세부 사항을 설명합니다.

Microsoft ID [플랫폼에 새 응용 프로그램을 등록하고](quickstart-register-app.md)응용 프로그램에 대해 지원되는 계정을 선택하는 단계를 따릅니다. SPA 시나리오는 조직의 계정 또는 모든 조직 및 개인 Microsoft 계정으로 인증을 지원할 수 있습니다.

그런 다음 단일 페이지 응용 프로그램에 적용되는 응용 프로그램 등록의 특정 측면을 알아봅니다.

## <a name="register-a-redirect-uri"></a>리디렉션 URI 등록

암시적 흐름은 웹 브라우저에서 실행되는 단일 페이지 응용 프로그램으로 리디렉션된 토큰을 보냅니다. 따라서 응용 프로그램에서 토큰을 받을 수 있는 리디렉션 URI를 등록하는 것이 중요합니다. 리디렉션 URI가 응용 프로그램의 URI와 정확히 일치하는지 확인합니다.

Azure [포털에서](https://go.microsoft.com/fwlink/?linkid=2083908)등록된 응용 프로그램으로 이동합니다. 응용 프로그램의 **인증** 페이지에서 **웹** 플랫폼을 선택합니다. **리디렉션** URI 필드에 응용 프로그램에 대한 리디렉션 URI 값을 입력합니다.

## <a name="enable-the-implicit-flow"></a>암시적 흐름 사용

동일한 **인증** 페이지에서 **고급 설정에서** **암시적 부여도**사용하도록 설정해야 합니다. 응용 프로그램이 사용자에 로그인하고 ID 토큰만 가져오는 경우 ID 토큰 확인란을 선택하는 것으로 **충분합니다.**

응용 프로그램에서 API를 호출하기 위해 액세스 토큰을 받아야 하는 경우 **액세스 토큰** 확인란도 선택해야 합니다. 자세한 내용은 [ID 토큰](./id-tokens.md) 및 액세스 [토큰 을](./access-tokens.md)참조하십시오.

## <a name="api-permissions"></a>API 사용 권한

단일 페이지 응용 프로그램은 로그인한 사용자를 대신하여 API를 호출할 수 있습니다. 위임된 권한을 요청해야 합니다. 자세한 내용은 [웹 API에 액세스할 수 있는 권한 추가를](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)참조하십시오.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱의 코드 구성](scenario-spa-app-configuration.md)
