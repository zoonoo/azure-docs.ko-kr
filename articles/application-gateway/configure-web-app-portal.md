---
title: 포털을 사용하여 다중 테넌트 앱에 대한 트래픽 관리
titleSuffix: Azure Application Gateway
description: 이 문서에서는 기존 또는 새 애플리케이션 게이트웨이에 Azure App Service 웹앱을 백 엔드 풀의 멤버로 구성하는 방법을 안내합니다.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/02/2021
ms.author: victorh
ms.openlocfilehash: aadd4904ff218613c0dd24daff784ad5b8b90fbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97854913"
---
# <a name="configure-app-service-with-application-gateway"></a>Application Gateway를 사용하여 App Service 구성

App Service는 전용 배포가 아닌 다중 테넌트 서비스이므로 들어오는 요청의 호스트 헤더를 사용하여 올바른 App Service 엔드포인트에 대한 요청을 확인합니다. 일반적으로 App Service 앞단의 애플리케이션 게이트웨이와 연결된 DNS 이름인 애플리케이션의 DNS 이름은 백 엔드 App Service의 도메인 이름과 다릅니다. 따라서 애플리케이션 게이트웨이에서 수신한 원래 요청의 호스트 헤더는 백 엔드 서비스의 호스트 이름과 동일하지 않습니다. 이 때문에 애플리케이션 게이트웨이에서 백 엔드로의 요청에 있는 호스트 헤더가 백 엔드 서비스의 호스트 이름으로 변경되지 않는 한 다중 테넌트 백 엔드는 올바른 엔드포인트에 대한 요청을 확인할 수 없습니다.

Application Gateway는 `Pick host name from backend target`라는 스위치를 제공하며, 이는 요청이 Application Gateway에서 백 엔드로 라우팅될 때 요청의 호스트 헤더를 백 엔드의 호스트 이름으로 재정의합니다. 이 기능을 사용하면 Azure App Service, API 관리와 같은 다중 테넌트 백 엔드를 지원할 수 있습니다. 

이 문서에서는 다음 방법을 설명합니다.

- 백 엔드 풀을 편집하여 App Service 추가
- ‘호스트 이름 선택’ 스위치를 사용하여 HTTP 설정 편집

## <a name="prerequisites"></a>사전 요구 사항

- 애플리케이션 게이트웨이: 백 엔드 풀 대상을 사용하지 않고 애플리케이션 게이트웨이를 만듭니다. 자세한 내용은 [빠른 시작: Azure Application Gateway를 통해 웹 트래픽 보내기 - Azure Portal](quick-create-portal.md)을 참조하세요.

- App Service: 기존 App Service가 없는 경우 [App Service 설명서](../app-service/index.yml)를 참조하세요.

## <a name="add-app-service-as-backend-pool"></a>백 엔드 풀로 App Service 추가

1. Azure Portal에서 애플리케이션 게이트웨이를 선택합니다.

2. **백 엔드 풀** 에서 백 엔드 풀을 선택합니다.

4. **대상 유형** 에서 **App Services** 를 선택합니다.

5. **대상** 에서 App Service를 선택합니다.

   :::image type="content" source="./media/configure-web-app-portal/backend-pool.png" alt-text="App Service 백 엔드":::
   
   > [!NOTE]
   > 드롭다운에는 Application Gateway와 동일한 구독에 있는 App Service만 있습니다. Application Gateway가 속해 있는 구독이 아닌 다른 구독에 있는 App Service를 사용하려는 경우 **대상** 드롭다운에서 **App Services** 를 선택하는 대신 **IP 주소 또는 호스트 이름** 옵션을 선택하고 App Service의 호스트 이름(예: azurewebsites.net)을 입력합니다.
1. **저장** 을 선택합니다.

## <a name="edit-http-settings-for-app-service"></a>App Service에 대한 HTTP 설정 편집

1. **HTTP 설정** 에서 기존 HTTP 설정을 선택합니다.

2. **새 호스트 이름으로 재정의** 에서 **예** 를 선택합니다.
3. **호스트 이름 재정의** 에서 **백 엔드 대상에서 호스트 이름 선택** 을 선택합니다.
4. **저장** 을 선택합니다.

   :::image type="content" source="./media/configure-web-app-portal/http-settings.png" alt-text="백 엔드 http 설정에서 호스트 이름 선택":::

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>App Service의 상대 경로로 리디렉션하는 경우의 추가 구성

App Service는 상대 경로로 리디렉션하기 위해 클라이언트에 리디렉션 응답을 보낼 때(예: `contoso.azurewebsites.net/path1`에서 `contoso.azurewebsites.net/path2`로 리디렉션) 애플리케이션 게이트웨이에서 받은 요청의 호스트 이름과 동일한 호스트 이름을 응답의 위치 헤더에 사용합니다. 따라서 클라이언트는 애플리케이션 게이트웨이(`contoso.com/path2`)를 통과하지 않고 `contoso.azurewebsites.net/path2`에 직접 요청을 보냅니다. 애플리케이션 게이트웨이를 우회하는 것은 바람직하지 않습니다.

사용 사례에서 App Service가 클라이언트에 리디렉션 응답을 보내야 하는 시나리오가 있는 경우 [추가 단계를 수행하여 위치 헤더를 다시 작성](./troubleshoot-app-service-redirection-app-service-url.md#sample-configuration)합니다.

## <a name="restrict-access"></a>액세스 제한

이 예제에서 배포된 웹앱은 인터넷에서 직접 액세스할 수 있는 공용 IP 주소를 사용합니다. 따라서 새로운 기능을 배우고 새로운 것을 시도할 때 문제를 해결하는 데 도움이 됩니다. 하지만 프로덕션 환경에 기능을 배포하려는 경우 더 많은 제한을 추가해야 합니다.

웹앱에 대한 액세스를 제한할 수 있는 한 가지 방법은 [Azure App Service 고정 IP 제한](../app-service/app-service-ip-restrictions.md)을 사용하는 것입니다. 웹앱이 애플리케이션 게이트웨이의 트래픽만 수신하도록 웹앱을 제한할 수 있습니다. 앱 서비스 IP 제한 기능을 사용하여 액세스할 수 있는 유일한 주소로 애플리케이션 게이트웨이 VIP를 나열할 수 있습니다.

## <a name="next-steps"></a>다음 단계

애플리케이션 게이트웨이를 사용한 App Service 및 기타 다중 테넌트 지원에 대해 자세히 알아보려면 [Application Gateway를 사용하여 다중 테넌트 서비스 지원](./application-gateway-web-app-overview.md)을 참조하세요.
