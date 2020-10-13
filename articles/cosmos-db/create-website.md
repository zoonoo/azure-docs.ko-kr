---
title: 템플릿을 사용하여 웹앱 배포 - Azure Cosmos DB
description: Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos 계정, Azure App Service Web Apps 및 샘플 웹 응용 프로그램을 배포 하는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: 8e6a6d1c557a765e55152685f08e80ad54bbd903
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362013"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-with-a-web-app-from-github-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 GitHub에서 웹 앱과 Azure Cosmos DB 및 Azure App Service 배포

이 자습서에서는 Azure Cosmos DB의 연결 정보를 잘라내어 `appsettings.json` Azure Portal의 Azure 앱 Services 응용 프로그램 설정으로 붙여 넣지 않고도 처음 실행 시 Azure Cosmos DB에 연결 하는 웹 응용 프로그램의 "터치 안 함" 배포를 수행 하는 방법을 보여 줍니다. 이러한 모든 작업은 단일 작업에서 Azure Resource Manager 템플릿을 사용 하 여 수행 됩니다. 예제에서는 [웹 앱 자습서](sql-api-dotnet-application.md)에서 [Azure Cosmos DB ToDo 샘플](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) 을 배포 합니다.

리소스 관리자 템플릿은 매우 유연 하며 Azure의 모든 서비스에서 복잡 한 배포를 작성할 수 있도록 합니다. 여기에는 GitHub에서 응용 프로그램을 배포 하 고 Azure Portal에서 Azure App Service의 응용 프로그램 설정에 연결 정보를 삽입 하는 등의 고급 작업이 포함 됩니다. 이 자습서에서는 단일 리소스 관리자 템플릿을 사용 하 여 다음 작업을 수행 하는 방법을 보여 줍니다.

* Azure Cosmos 계정을 배포 합니다.
* Azure App Service 호스팅 계획을 배포 합니다.
* Azure App Service를 배포 합니다.
* Azure Cosmos 계정의 끝점 및 키를 Azure Portal의 App Service 응용 프로그램 설정에 삽입 합니다.
* GitHub 리포지토리에서 App Service에 웹 응용 프로그램을 배포 합니다.

결과 배포에는 Azure Portal에서 Azure Cosmos DB의 끝점 URL 또는 인증 키를 잘라내어 붙여 넣지 않고도 Azure Cosmos DB에 연결할 수 있는 완전히 작동 하는 웹 응용 프로그램이 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

> [!TIP]
> 이 자습서에서는 Azure Resource Manager 템플릿 또는 JSON을 이전에 사용해 본 경험이 있다고 가정하지는 않지만, 참조된 템플릿 또는 배포 옵션을 수정하려면 이러한 각 영역에 대한 지식이 필요합니다.

## <a name="step-1-deploy-the-template"></a>1 단계: 템플릿 배포

먼저 아래 **Azure에 배포** 단추를 선택 하 여 Azure Portal을 열고 사용자 지정 배포를 만듭니다. Azure [빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp) 에서 Azure 리소스 관리 템플릿을 볼 수도 있습니다.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure에 배포":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-webapp%2Fazuredeploy.json)

Azure Portal에서 배포할 구독을 선택 하 고 새 리소스 그룹을 선택 하거나 만듭니다. 다음 값을 입력 합니다.

:::image type="content" source="./media/create-website/template-deployment.png" alt-text="Azure에 배포":::

* **Region** -리소스 관리자에 필요 합니다. 리소스가 있는 location 매개 변수에서 사용 하는 것과 동일한 지역을 입력 합니다.
* **응용 프로그램 이름** -이 이름은이 배포에 대 한 모든 리소스에서 사용 됩니다. 기존 Azure Cosmos DB 및 App Service 계정과 충돌 하지 않도록 고유한 이름을 선택 해야 합니다.
* **Location** -리소스가 배포 되는 지역입니다.
* **계획 계층** App Service 계획의 가격 책정 계층을 App Service 합니다.
* **App Service 계획 인스턴스** -App Service 계획에 대 한 작업자 수입니다.
* **리포지토리 URL** -GitHub의 웹 응용 프로그램에 대 한 리포지토리입니다.
* **분기** -GitHub 리포지토리에 대 한 분기입니다.
* **데이터베이스 이름** -Azure Cosmos 데이터베이스 이름입니다.
* **컨테이너 이름** -Azure Cosmos 컨테이너 이름입니다.

값을 입력 한 후 **만들기** 단추를 선택 하 여 배포를 시작 합니다. 이 단계를 완료 하려면 5 ~ 10 분이 소요 됩니다.

> [!TIP]
> 템플릿에서 입력 한 Azure App Service 이름 및 Azure Cosmos 계정 이름이 유효 하 고 사용 가능한 지 확인 하지 않습니다. 배포를 제출하기 전에 지정하려는 이름의 가용성을 확인하는 것이 좋습니다.


## <a name="step-2-explore-the-resources"></a>2 단계: 리소스 탐색

### <a name="view-the-deployed-resources"></a>배포된 리소스 검토

템플릿이 리소스를 배포한 후 리소스 그룹에서 각 리소스를 볼 수 있습니다.

:::image type="content" source="./media/create-website/resource-group.png" alt-text="Azure에 배포":::

### <a name="view-cosmos-db-endpoint-and-keys"></a>Cosmos DB 끝점 및 키 보기

그런 다음 포털에서 Azure Cosmos 계정을 엽니다. 다음 스크린샷은 Azure Cosmos 계정에 대 한 끝점 및 키를 보여 줍니다.

:::image type="content" source="./media/create-website/cosmos-keys.png" alt-text="Azure에 배포":::

### <a name="view-the-azure-cosmos-db-keys-in-application-settings"></a>응용 프로그램 설정에서 Azure Cosmos DB 키 보기

그런 다음 리소스 그룹의 Azure App Service로 이동 합니다. 구성 탭을 클릭 하 여 App Service에 대 한 응용 프로그램 설정을 확인 합니다. 응용 프로그램 설정에는 Cosmos DB에 연결 하는 데 필요한 Cosmos DB 계정 및 기본 키 값과 템플릿 배포에서 전달 된 데이터베이스 및 컨테이너 이름이 포함 됩니다.

:::image type="content" source="./media/create-website/application-settings.png" alt-text="Azure에 배포":::

### <a name="view-web-app-in-deployment-center"></a>Deployment Center에서 웹 앱 보기

그런 다음 App Service에 대 한 배포 센터로 이동 합니다. 여기에서 템플릿에 전달 된 GitHub 리포지토리에 대 한 리포지토리 지점이 표시 됩니다. 또한 아래 상태는 성공 (활성)을 나타냅니다. 즉, 응용 프로그램을 성공적으로 배포 하 고 시작 했음을 나타냅니다.

:::image type="content" source="./media/create-website/deployment-center.png" alt-text="Azure에 배포":::

### <a name="run-the-web-application"></a>웹 애플리케이션 실행

Deployment Center 위쪽에서 **찾아보기** 를 클릭 하 여 웹 응용 프로그램을 엽니다. 웹 응용 프로그램이 홈 화면까지 열립니다. **새로 만들기** 를 클릭 하 고 필드에 일부 데이터를 입력 하 고 저장을 클릭 합니다. 결과 화면에는 Cosmos DB에 저장 된 데이터가 표시 됩니다.

:::image type="content" source="./media/create-website/app-home-screen.png" alt-text="Azure에 배포":::

## <a name="step-3-how-does-it-work"></a>3 단계: 작동 방법

이 작업을 수행 하는 데 필요한 세 가지 요소가 있습니다.

### <a name="reading-app-settings-at-runtime"></a>런타임에 앱 설정 읽기

먼저 응용 프로그램은 `Startup` ASP.NET MVC 웹 응용 프로그램의 클래스에서 Cosmos DB 끝점 및 키를 요청 해야 합니다. Cosmos DB에 대 한 appsettings.js에 연결 정보를 입력할 수 있는 위치에서 [샘플을](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) 로컬로 실행할 수 있습니다. 그러나 배포 하는 경우이 파일은 앱과 함께 배포 됩니다. 이러한 줄이 appsettings.js의 설정에 액세스할 수 없는 경우 Azure App Service의 응용 프로그램 설정에서 시도 합니다.

:::image type="content" source="./media/create-website/startup.png" alt-text="Azure에 배포":::

### <a name="using-special-azure-resource-management-functions"></a>특수 Azure 리소스 관리 기능 사용

이러한 값을 배포할 때 응용 프로그램에서 사용할 수 있도록 Azure Resource Manager 템플릿에서는 ' {section: key} ' 형식의 위에 있는 응용 프로그램에서 사용 되는 것과 일치 하는 키 이름을 가진 응용 프로그램 설정 값에 Cosmos DB 계정의 값을 가져오는 [참조](../azure-resource-manager/templates/template-functions-resource.md#reference) 및 [listkeys](../azure-resource-manager/templates/template-functions-resource.md#listkeys) 를 비롯 한 특수 Azure 리소스 관리 기능을 사용 하 여 Cosmos DB 계정에서 해당 값을 요청할 수 있습니다. 예들 들어 `CosmosDb:Account`입니다.

:::image type="content" source="./media/create-website/template-keys.png" alt-text="Azure에 배포":::

### <a name="deploying-web-apps-from-github"></a>GitHub에서 웹 앱 배포

마지막으로 GitHub에서 App Service 웹 응용 프로그램을 배포 해야 합니다. 아래 JSON을 사용 하 여이 작업을 수행 합니다. 주의 해야 하는 두 가지 사항은이 리소스의 형식 및 이름입니다. `"type": "sourcecontrols"`및 `"name": "web"` 속성 값은 모두 하드 코딩 되며 변경할 수 없습니다.

:::image type="content" source="./media/create-website/deploy-from-github.png" alt-text="Azure에 배포":::

## <a name="next-steps"></a>다음 단계

축하합니다! Cosmos DB에 연결 하는 데 필요한 연결 정보를 자동으로 포함 하는 Azure Cosmos DB, Azure App Service 및 샘플 웹 응용 프로그램을 단일 작업으로 사용 하 고 중요 한 정보를 잘라내어 붙여 넣지 않아도 됩니다. 이 템플릿을 시작 점으로 사용 하 여 사용자 고유의 웹 응용 프로그램을 동일한 방식으로 배포 하도록 수정할 수 있습니다.

* 이 샘플에 대 한 Azure Resource Manager 템플릿의 경우 [Azure 빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp) 로 이동 합니다.
* 샘플 앱에 대 한 소스 코드는 [GitHub에서 Cosmos DB 앱](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)으로 이동 합니다.
