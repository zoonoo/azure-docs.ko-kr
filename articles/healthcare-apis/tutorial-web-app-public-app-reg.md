---
title: 웹앱 자습서 - 클라이언트 애플리케이션 설정
description: 이 자습서에서는 웹 애플리케이션을 배포할 준비를 위해 퍼블릭 애플리케이션을 등록하는 단계를 안내합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 58e21e46edfe1d03d42bf2202bcf1f22282631a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "84870456"
---
# <a name="client-application-registration"></a>클라이언트 애플리케이션 등록
이전 자습서에서는 Azure API for FHIR을 배포하고 설정했습니다. 이제 Azure API for FHIR을 설정했으므로 퍼블릭 클라이언트 애플리케이션을 등록합니다. 자세한 내용 또는 문제 해결에 대한 전체 [퍼블릭 클라이언트 앱 등록](register-public-azure-ad-client-app.md) 방법 가이드를 참조할 수 있지만 아래 이 자습서에 대한 주요 단계를 소개했습니다.

1. Azure Active Directory로 이동
1. **앱 등록** --> **새 등록** 선택
1. 애플리케이션의 이름 설정 및 리디렉션 URI를 https://www.getpostman.com/oauth2/callback 으로 설정


![클라이언트 애플리케이션 등록](media/tutorial-web-app/reg-public-app.png)

## <a name="client-application-settings"></a>클라이언트 애플리케이션 설정
클라이언트 애플리케이션이 등록되면 개요 페이지에서 애플리케이션(클라이언트) ID를 복사합니다. 나중에 클라이언트에 액세스할 때 이 값이 필요합니다.

![앱 ID 복사](media/tutorial-web-app/app-id.png)

다음으로 올바른 인증 옵션을 설정합니다. 왼쪽에서 **인증**을 선택합니다. **액세스 토큰** 및 **ID 토큰** 상자를 선택합니다. 이 자습서의 네 번째 부분에서 웹 애플리케이션을 만들 때를 준비하는 과정에서 리디렉션 URI를 설정할 수도 있습니다. 이렇게 하려면 https://\<WEB-APP-NAME>.azurewebsites.net을 리디렉션 URI 목록에 추가합니다. [웹앱을 작성](tutorial-web-app-write-web-app.md)하는 단계에서 다른 이름을 선택하는 경우 돌아가서 이를 업데이트해야 합니다.

![앱 인증 설정](media/tutorial-web-app/app-authentication.png)

이제 올바른 인증을 설정했으므로 API 사용 권한을 설정합니다. 
1. **API 사용 권한** 선택 및 **사용 권한 추가** 클릭
1. **내 조직에서 사용하는 API**에서 Azure Healthcare API 검색
1. **user_impersonation** 선택 및 **사용 권한 추가** 클릭

## <a name="next-steps"></a>다음 단계
이제 퍼블릭 클라이언트 애플리케이션이 있습니다. 다음 자습서에서는 Postman을 통해 이 애플리케이션을 테스트하고 액세스 권한을 얻는 방법을 살펴봅니다.

>[!div class="nextstepaction"]
>[Postman에서 클라이언트 애플리케이션 테스트](tutorial-web-app-test-postman.md)
