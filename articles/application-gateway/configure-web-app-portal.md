---
title: 포털을 사용하여 다중 테넌트 앱으로 트래픽 관리
titleSuffix: Azure Application Gateway
description: 이 문서에서는 기존 또는 새 응용 프로그램 게이트웨이의 백 엔드 풀에서 멤버로 Azure App 서비스 웹 앱을 구성하는 방법에 대한 지침을 제공합니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075174"
---
# <a name="configure-app-service-with-application-gateway"></a>Application Gateway를 사용하여 App Service 구성

앱 서비스는 전용 배포 대신 다중 테넌트 서비스이므로 들어오는 요청에서 호스트 헤더를 사용하여 요청을 올바른 앱 서비스 끝점으로 확인합니다. 일반적으로 응용 프로그램의 DNS 이름은 응용 프로그램 서비스를 전면에 있는 응용 프로그램 게이트웨이와 연결된 DNS 이름으로, 백 엔드 앱 서비스의 도메인 이름과 다릅니다. 따라서 응용 프로그램 게이트웨이에서 받은 원래 요청의 호스트 헤더는 백 엔드 서비스의 호스트 이름과 다다. 따라서 응용 프로그램 게이트웨이에서 백 엔드에 대한 요청의 호스트 헤더가 백 엔드 서비스의 호스트 이름으로 변경되지 않으면 다중 테넌트 백엔드가 요청을 올바른 끝점으로 확인할 수 없습니다.

응용 프로그램 게이트웨이는 `Pick host name from backend address` 요청이 응용 프로그램 게이트웨이에서 백 엔드로 라우팅될 때 백 엔드의 호스트 이름으로 요청의 호스트 헤더를 재정의하는 스위치를 제공합니다. 이 기능을 사용하면 Azure 앱 서비스 및 API 관리와 같은 다중 테넌트 백 엔드를 지원할 수 있습니다. 

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> - 백 엔드 풀을 만들고 앱 서비스를 추가합니다.
> - "호스트 이름 선택" 스위치를 사용하도록 설정한 HTTP 설정 및 사용자 지정 프로브 만들기

## <a name="prerequisites"></a>사전 요구 사항

- 응용 프로그램 게이트웨이: 기존 응용 프로그램 게이트웨이가 없는 경우 [응용 프로그램 게이트웨이를 만드는](https://docs.microsoft.com/azure/application-gateway/quick-create-portal) 방법을 참조하세요.
- 앱 서비스: 기존 앱 서비스가 없는 경우 [앱 서비스 설명서를](https://docs.microsoft.com/azure/app-service/)참조하십시오.

## <a name="add-app-service-as-backend-pool"></a>앱 서비스를 백 엔드 풀로 추가

1. Azure 포털에서 응용 프로그램 게이트웨이의 구성 보기를 엽니다.

2. **백 엔드 풀에서** **추가를** 클릭하여 새 백 엔드 풀을 만듭니다.

3. 백 엔드 풀에 적합한 이름을 제공합니다. 

4. **대상**아래에서 드롭다운을 클릭하고 **앱 서비스를** 옵션으로 선택합니다.

5. 앱 서비스 목록이 포함된 **목표** 드롭다운 바로 아래에 드롭다운이 표시됩니다. 이 드롭다운에서 백 엔드 풀 멤버로 추가할 앱 서비스를 선택하고 추가를 클릭합니다.

   ![앱 서비스 백엔드](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > 드롭다운은 응용 프로그램 게이트웨이와 동일한 구독에 있는 해당 앱 서비스만 채웁니다. 응용 프로그램 게이트웨이가 있는 구독과 다른 구독에 있는 앱 서비스를 사용하려는 경우 **대상** 드롭다운에서 **앱 서비스를** 선택하는 대신 IP 주소 또는 **호스트 이름** 옵션을 선택하고 호스트 이름(예:)을 입력합니다. 앱 서비스의 azurewebsites.net)를 참조하십시오.

## <a name="create-http-settings-for-app-service"></a>앱 서비스에 대한 HTTP 설정 만들기

1. **HTTP 설정에서** **추가를** 클릭하여 새 HTTP 설정을 만듭니다.

2. HTTP 설정에 대한 이름을 입력하면 요구 사항에 따라 쿠키 기반 선호도를 활성화하거나 비활성화할 수 있습니다.

3. 사용 사례에 따라 프로토콜을 HTTP 또는 HTTPS로 선택합니다. 

   > [!NOTE]
   > HTTPS를 선택하는 경우 앱 서비스가 신뢰할 수 있는 Azure 서비스이므로 앱 서비스 백엔드를 화이트리스트에 추가하기 위해 인증 인증서 또는 신뢰할 수 있는 루트 인증서를 업로드할 필요가 없습니다.

4. 앱 서비스에 **사용할** 수 있는 확인란을 선택합니다. 스위치가 `Create a probe with pick host name from backend address` 자동으로 `Pick host name from backend address` 활성화됩니다.`Pick host name from backend address` 요청이 응용 프로그램 게이트웨이에서 백 엔드로 라우팅될 때 백 엔드의 호스트 이름으로 요청의 호스트 헤더를 재정의합니다.  

   `Create a probe with pick host name from backend address`자동으로 상태 프로브를 만들고 이 HTTP 설정에 연결합니다. 이 HTTP 설정에 대 한 다른 상태 프로브를 만들 필요가 없습니다. 이름이 <HTTP Setting name> <Unique GUID> 있는 새 프로브가 상태 프로브 목록에 추가되었으며 이미 스위치가 `Pick host name from backend http settings enabled`있는지 확인할 수 있습니다.

   앱 서비스에 사용되고 있는 HTTP 설정이 이미 하나 이상 있고 해당 HTTP 설정이 사용자가 만드는 설정에서 사용하는 것과 동일한 프로토콜을 사용하는 경우 `Create a probe with pick host name from backend address` 스위치 대신 사용자 지정 프로브 중 하나를 선택하는 드롭다운을 받게 됩니다. 이는 이미 앱 서비스가 있는 HTTP 설정이 있기 때문에 스위치가 `Pick host name from backend http settings enabled` 있는 상태 프로브도 존재하기 때문입니다. 드롭다운에서 해당 사용자 지정 프로브를 선택합니다.

5. **확인을** 클릭하여 HTTP 설정을 만듭니다.

   ![HTTP 설정1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP 설정2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>규칙 만들기하여 수신기, 백엔드 풀 및 HTTP 설정을 연결합니다.

1. **규칙에서** **기본을** 클릭하여 새 기본 규칙을 만듭니다.

2. 적절한 이름을 제공하고 앱 서비스에 대한 수신 요청을 수락할 리스너를 선택합니다.

3. 백 **엔드 풀** 드롭다운에서 위에서 만든 백 엔드 풀을 선택합니다.

4. HTTP **설정** 드롭다운에서 위에서 만든 HTTP 설정을 선택합니다.

5. **확인을** 클릭하여 이 규칙을 저장합니다.

   ![규칙](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>앱 서비스의 상대 경로로 리디렉션하는 경우 추가 구성

앱 서비스가 클라이언트에 리디렉션 응답을 보내 상대 경로로 리디렉션할 때(예: contoso.azurewebsites.net/path1 contoso.azurewebsites.net/path2 리디렉션) 응용 프로그램 게이트웨이에서 받은 요청의 요청과 응답의 위치 헤더에서 동일한 호스트 이름을 사용합니다. 따라서 클라이언트는 응용 프로그램 게이트웨이(contoso.com/path2)를 거치지 않고 contoso.azurewebsites.net/path2 직접 요청을 합니다. 응용 프로그램 게이트웨이를 우회하는 것은 바람직하지 않습니다.

사용 사례에서 앱 서비스가 클라이언트에 리디렉션 응답을 보내야 하는 시나리오가 있는 경우 [위치 헤더를 다시 작성하는 추가 단계를 수행합니다.](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration)

## <a name="restrict-access"></a>액세스 제한

이 예제에서 배포된 웹앱은 인터넷에서 직접 액세스할 수 있는 공용 IP 주소를 사용합니다. 따라서 새로운 기능을 배우고 새로운 것을 시도할 때 문제를 해결하는 데 도움이 됩니다. 하지만 프로덕션 환경에 기능을 배포하려는 경우 더 많은 제한을 추가해야 합니다.

웹앱에 대한 액세스를 제한할 수 있는 한 가지 방법은 [Azure App Service 고정 IP 제한](../app-service/app-service-ip-restrictions.md)을 사용하는 것입니다. 웹앱이 애플리케이션 게이트웨이의 트래픽만 수신하도록 웹앱을 제한할 수 있습니다. 앱 서비스 IP 제한 기능을 사용하여 액세스할 수 있는 유일한 주소로 애플리케이션 게이트웨이 VIP를 나열할 수 있습니다.

## <a name="next-steps"></a>다음 단계

응용 프로그램 게이트웨이를 사용하여 App 서비스 및 기타 다중 테넌트 지원에 대해 자세히 알아보려면 [응용 프로그램 게이트웨이를 사용하여 다중 테넌트 서비스 지원을](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)참조하십시오.
