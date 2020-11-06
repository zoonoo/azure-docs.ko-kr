---
title: 포털을 사용 하 여 다중 테 넌 트 앱에 대 한 트래픽 관리
titleSuffix: Azure Application Gateway
description: 이 문서에서는 기존 또는 새 응용 프로그램 게이트웨이에서 백 엔드 풀의 멤버로 Azure 앱 서비스 웹 앱을 구성 하는 방법에 대 한 지침을 제공 합니다.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/23/2020
ms.author: victorh
ms.openlocfilehash: a72f0106088d26eb2ff53456840c598c3d9619a7
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397555"
---
# <a name="configure-app-service-with-application-gateway"></a>Application Gateway를 사용하여 App Service 구성

App service는 전용 배포가 아닌 다중 테 넌 트 서비스 이므로 들어오는 요청의 호스트 헤더를 사용 하 여 올바른 app service 끝점에 대 한 요청을 확인 합니다. 일반적으로 응용 프로그램의 DNS 이름은 app service 앞단에 application gateway와 연결 된 DNS 이름으로, 백엔드 app service의 도메인 이름과 다릅니다. 따라서 application gateway에서 받은 원래 요청의 호스트 헤더는 백 엔드 서비스의 호스트 이름과 동일 하지 않습니다. 이 때문에 응용 프로그램 게이트웨이에서 백 엔드에 대 한 요청에 있는 호스트 헤더를 백 엔드 서비스의 호스트 이름으로 변경 하지 않는 한 다중 테 넌 트 백 엔드는 해당 요청을 올바른 끝점으로 확인할 수 없습니다.

Application Gateway는 요청이 `Pick host name from backend target` Application Gateway에서 백 엔드로 라우팅될 때 백 엔드의 호스트 이름을 사용 하 여 요청의 호스트 헤더를 재정의 하는 스위치를 제공 합니다. 이 기능을 사용 하면 Azure app service 및 API management와 같은 다중 테 넌 트 백 엔드를 지원할 수 있습니다. 

이 문서에서는 다음 방법을 설명합니다.

- 백 엔드 풀을 편집 하 고 여기에 App Service 추가
- ' 호스트 선택 ' 스위치를 사용 하 여 HTTP 설정 편집

## <a name="prerequisites"></a>필수 구성 요소

- Application gateway: 백 엔드 풀 대상을 사용 하지 않고 응용 프로그램 게이트웨이를 만듭니다. 자세한 내용은 [빠른 시작: Azure 애플리케이션 게이트웨이를 사용 하 여 웹 트래픽 직접 사용-Azure Portal](quick-create-portal.md) 을 참조 하세요.

- App service: 기존 App service가 없는 경우 [app service 설명서](../app-service/index.yml)를 참조 하세요.

## <a name="add-app-service-as-backend-pool"></a>백 엔드 풀로 App service 추가

1. Azure Portal에서 응용 프로그램 게이트웨이를 선택 합니다.

2. **백 엔드 풀** 에서 백 엔드 풀을 선택 합니다.

4. **대상 유형** 에서 **App Services** 를 선택 합니다.

5. **대상** 아래에서 App Service를 선택 합니다.

   :::image type="content" source="./media/configure-web-app-portal/backend-pool.png" alt-text="App service 백 엔드":::
   
   > [!NOTE]
   > 드롭다운은 Application Gateway와 동일한 구독에 있는 앱 서비스만 채웁니다. Application Gateway 아닌 다른 구독에 있는 앱 서비스를 사용 하려는 경우 **대상** 드롭다운에서 **App Services** 를 선택 하는 대신 **IP 주소 또는 호스트 이름** 옵션을 선택 하 고 호스트 이름 (예:)을 입력 합니다. azurewebsites.net)를 다운로드 합니다.
1. **저장** 을 선택합니다.

## <a name="edit-http-settings-for-app-service"></a>App Service에 대 한 HTTP 설정 편집

1. **Http 설정** 에서 기존 http 설정을 선택 합니다.

2. **새 호스트 이름으로 재정의** 아래에서 **예** 를 선택 합니다.
3. **호스트 이름 재정의** 에서 **백 엔드 대상에서 호스트 이름** 선택을 선택 합니다.
4. **저장** 을 선택합니다.

   :::image type="content" source="./media/configure-web-app-portal/http-settings.png" alt-text="백 엔드 http 설정에서 호스트 이름 선택":::

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>App service의 상대 경로로 리디렉션하는 경우 추가 구성

App service가 상대 경로 (예:에서로의 리디렉션)로 리디렉션되는 클라이언트에 리디렉션 응답을 보낼 때 `contoso.azurewebsites.net/path1` `contoso.azurewebsites.net/path2` 응용 프로그램 게이트웨이에서 받은 요청의 위치 헤더에 있는 것과 동일한 호스트 이름을 사용 합니다. 따라서 클라이언트는 `contoso.azurewebsites.net/path2` 응용 프로그램 게이트웨이 ()를 통하지 않고 직접 요청을 만듭니다 `contoso.com/path2` . Application gateway를 무시 하는 것은 바람직하지 않습니다.

사용 사례에서 응용 프로그램 서비스가 클라이언트에 리디렉션 응답을 보내야 하는 경우에는 추가 단계를 수행 하 여 [위치 헤더를 다시 작성](./troubleshoot-app-service-redirection-app-service-url.md#sample-configuration)해야 합니다.

## <a name="restrict-access"></a>액세스 제한

이 예제에서 배포된 웹앱은 인터넷에서 직접 액세스할 수 있는 공용 IP 주소를 사용합니다. 따라서 새로운 기능을 배우고 새로운 것을 시도할 때 문제를 해결하는 데 도움이 됩니다. 하지만 프로덕션 환경에 기능을 배포하려는 경우 더 많은 제한을 추가해야 합니다.

웹앱에 대한 액세스를 제한할 수 있는 한 가지 방법은 [Azure App Service 고정 IP 제한](../app-service/app-service-ip-restrictions.md)을 사용하는 것입니다. 웹앱이 애플리케이션 게이트웨이의 트래픽만 수신하도록 웹앱을 제한할 수 있습니다. 앱 서비스 IP 제한 기능을 사용하여 액세스할 수 있는 유일한 주소로 애플리케이션 게이트웨이 VIP를 나열할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Application gateway를 사용 하 여 App service 및 기타 다중 테 넌 트 지원에 대 한 자세한 내용은 [application gateway를 사용 하 여 다중 테 넌 트 서비스 지원](./application-gateway-web-app-overview.md)을 참조 하세요.