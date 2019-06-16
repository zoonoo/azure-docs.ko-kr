---
title: Azure 리소스에 대한 관리 ID - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs에서 Azure 리소스에 대한 관리 ID를 사용하는 방법을 설명합니다.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 05/20/2019
ms.author: shvija
ms.openlocfilehash: 4e6f16a15547583baab63f452504d36eb2e43b85
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65978437"
---
# <a name="managed-identities-for-azure-resources-with-event-hubs"></a>Event Hubs를 통해 Azure 리소스에 관리 ID 사용

[Azure 리소스용 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)는 애플리케이션 코드가 실행되는 배포와 관련된 보안 ID를 만들 수 있도록 하는 Azure 간 기능입니다. 그런 다음 애플리케이션에 필요한 특정 Azure 리소스에 액세스하기 위한 사용자 지정 권한을 부여하는 액세스 제어 역할에 해당 ID를 연결할 수 있습니다. 

관리 ID를 사용하는 경우 Azure 플랫폼에서 이 런타임 ID를 관리합니다. ID 자체 또는 액세스해야 하는 리소스에 대한 액세스 키를 애플리케이션 코드나 구성에 저장하고 보호할 필요가 없습니다. Azure App Service 애플리케이션 내에서 실행되거나 Azure 리소스용 관리 ID를 지원하도록 설정된 가상 머신에서 실행되는 Event Hubs 클라이언트 앱은 SAS 규칙과 키 또는 기타 액세스 토큰을 처리할 필요가 없습니다. 클라이언트 앱은 Event Hubs 네임스페이스의 엔드포인트 주소만 있으면 됩니다. 앱이 연결되면 Event Hubs는 이 문서 뒷부분의 예제에 나와 있는 작업에서 관리 ID의 컨텍스트를 클라이언트에 바인딩합니다.

관리 ID와 연결된 Event Hubs 클라이언트는 권한이 부여된 모든 작업을 수행할 수 있습니다. 관리 ID를 Event Hubs 역할에 연결하는 방식을 통해 권한이 부여됩니다. 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs 역할 및 사용 권한
관리 되는 id를 추가할 수 있습니다 합니다 **Event Hubs 데이터 소유자** Event Hubs 네임 스페이스의 역할입니다. 이 역할에는 네임 스페이스에 있는 모든 엔터티 (대 한 관리 및 데이터 작업) 전체 컨트롤 id를 부여합니다.

>[!IMPORTANT]
> 에서는 이전에 지원 된 관리 되는 id를 추가 합니다 **소유자** 또는 **참가자** 역할입니다. 그러나 데이터 액세스에 대 한 권한을 **소유자** 하 고 **참가자** 역할은 더 이상 적용 합니다. 사용 중인 경우는 **소유자** 또는 **참가자** 역할을 사용 하도록 전환 합니다 **Event Hubs 데이터 소유자** 역할입니다.

새 기본 제공 역할을 사용 하려면 다음이 단계를 수행 합니다. 

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. Event Hubs 네임 스페이스를 이동 합니다.
3. 에 **Event Hubs Namespace** 페이지에서 **액세스 제어 (iam)** 왼쪽된 메뉴에서.
4. 에 **액세스 제어 (IAM)** 페이지에서 **추가** 에 **역할 할당을 추가** 섹션입니다. 

    ![역할 할당 단추 추가](./media/event-hubs-managed-service-identity/add-role-assignment-button.png)
5. 에 **역할 할당 추가** 페이지에서 다음 단계를 수행 합니다. 
    1. 에 대 한 **역할**를 선택 **Azure Event Hubs 데이터 소유자**합니다. 
    2. 선택 된 **identity** 역할을 추가할 수 있습니다.
    3. **저장**을 선택합니다. 

        ![이벤트 허브 데이터 소유자 역할](./media/event-hubs-managed-service-identity/add-role-assignment-dialog.png)
6. 로 전환 합니다 **역할 할당** 페이지 및 사용자 추가 되어 있는지 확인 합니다 **Azure Event Hubs 데이터 소유자** 역할. 

    ![사용자가 역할에 추가 확인](./media/event-hubs-managed-service-identity/role-assignments.png)
 
## <a name="use-event-hubs-with-managed-identities-for-azure-resources"></a>Event Hubs에서 Azure 리소스용 관리 ID 사용

다음 섹션에서는 아래 단계에 대해 자세히 설명합니다.

1. 관리 ID로 실행되는 샘플 애플리케이션을 만들고 배포합니다.
2. 해당 ID에 Event Hubs 네임스페이스 액세스 권한을 부여합니다.
3. 애플리케이션이 해당 ID를 사용하여 Event Hubs와 상호 작용하는 방법을 지정합니다.

이 소개에서는 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 호스트되는 웹 애플리케이션에 대해 설명합니다. VM 호스트 애플리케이션에 필요한 단계는 비슷합니다.

### <a name="create-an-app-service-web-application"></a>App Service 웹 애플리케이션 만들기

첫 번째 단계는 App Service ASP.NET 애플리케이션을 만드는 것입니다. Azure에서 이 작업을 수행하는 방법을 잘 모를 경우 [이 방법 가이드](../app-service/app-service-web-get-started-dotnet-framework.md)를 따릅니다. 단, 자습서에 나와 있는 MVC 애플리케이션 대신, Web Forms 애플리케이션을 만듭니다.

### <a name="set-up-the-managed-identity"></a>관리 ID 설정

애플리케이션을 만든 후 Azure Portal에서 새로 만든 웹앱으로 이동한 후(방법에 제공된 설명 참조) **관리 서비스 ID** 페이지로 이동하여 해당 기능을 사용하도록 설정합니다. 

![관리형 서비스 ID 페이지](./media/event-hubs-managed-service-identity/msi1.png)
 
기능을 사용하도록 설정하면 새 서비스 ID가 Azure Active Directory에서 생성되고 App Service 호스트로 구성됩니다.

### <a name="create-a-new-event-hubs-namespace"></a>새 Event Hubs 네임스페이스 만들기

그런 다음, [Event Hubs 네임 스페이스 만들기](event-hubs-create.md)합니다. 

포털의 네임스페이스 **액세스 제어(IAM)** 페이지로 이동한 후 **역할 할당 추가**를 클릭하여 관리형 ID를 **소유자** 역할에 추가합니다. 이렇게 하려면 **권한 추가** 패널의 **선택** 필드에서 웹 애플리케이션의 이름을 검색하고 해당 항목을 클릭합니다. 그런 다음 **Save**를 클릭합니다. 이제 웹 애플리케이션의 관리 ID는 Event Hubs 네임스페이스 및 이전에 만든 이벤트 허브에 액세스할 수 있습니다. 

### <a name="run-the-app"></a>앱 실행

이제 만든 ASP.NET 애플리케이션의 기본 페이지를 수정합니다. [이 GitHub 리포지토리](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp)에서 웹 애플리케이션 코드를 사용할 수도 있습니다. 

앱을 시작한 후 브라우저로 EventHubsMSIDemo.aspx를 가리킵니다. 이 페이지를 시작 페이지로 설정할 수도 있습니다. 이 코드는 EventHubsMSIDemo.aspx.cs 파일에서 찾을 수 있습니다. 그 결과 몇 가지 입력 필드와 이벤트를 보내거나 받기 위해 Event Hubs에 연결되는 **전송** 및 **수신** 단추가 있는 최소 웹 애플리케이션이 생성됩니다. 

[MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 개체가 어떻게 초기화되는지에 유의합니다. 이 코드는 SAS(공유 액세스 토큰) 토큰 공급자를 사용하는 대신 `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` 호출을 사용하여 관리 ID용 토큰 공급자를 만듭니다. 그러므로 비밀을 저장하여 사용할 필요가 없습니다. Event Hubs 및 권한 부여 핸드셰이크에 대한 관리 ID 컨텍스트 흐름은 토큰 공급자가 자동으로 처리합니다. 이것은 SAS를 사용하는 것보다 단순한 모델입니다.

이와 같이 변경한 후에는 애플리케이션을 게시하고 실행합니다. Visual Studio에서 게시 프로필을 다운로드한 다음 가져오면 올바른 게시 데이터를 가져올 수 있습니다.

![게시 프로필 가져오기](./media/event-hubs-managed-service-identity/msi3.png)
 
메시지를 보내거나 받으려면 만든 엔터티의 이름과 네임스페이스의 이름을 입력한 후 **전송** 또는 **수신**을 클릭합니다. 
 
관리 ID는 Azure 환경 및 관리 ID를 구성한 App Service 배포에서만 작동하며 App Service 배포 슬롯에서 현재 작동하지 않습니다.

## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 방문하세요.

* [Event Hubs 자습서](event-hubs-dotnet-standard-getstarted-send.md) 시작
* [Event Hubs FAQ](event-hubs-faq.md)
* [Event Hubs 가격 정보](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Event Hubs를 사용하는 샘플 애플리케이션](https://github.com/Azure/azure-event-hubs/tree/master/samples)
