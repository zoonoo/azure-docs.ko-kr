---
title: Azure Portal에서 Logic Apps Preview 워크플로 만들기
description: Azure Portal에서 Azure Logic Apps 미리 보기를 사용 하 여 자동화 및 통합 시나리오에 대 한 워크플로를 빌드하고 실행 합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: ff938d29d998b6fcf0b2cfae72a9a9e685a10dc5
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563964"
---
# <a name="create-stateful-and-stateless-workflows-in-the-azure-portal-with-azure-logic-apps-preview"></a>Azure Logic Apps 미리 보기를 사용 하 여 Azure Portal에서 상태 저장 및 상태 비저장 워크플로 만들기

> [!IMPORTANT]
> 이 기능은 공개 미리 보기 상태이고 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure Logic Apps 미리 보기](logic-apps-overview-preview.md)를 사용 하면 새 **논리 앱 (미리 보기)** 리소스 형식으로 시작 하 여 Azure Portal에 [ *상태 저장* 및 *상태 비저장* 워크플로](logic-apps-overview-preview.md#stateful-stateless) 를 포함 하는 논리 앱을 만들고 실행 하 여 앱, 데이터, 클라우드 서비스 및 시스템 간에 자동화 및 통합 솔루션을 빌드할 수 있습니다. 이 새 논리 앱 유형을 사용 하 여 Azure 뿐만 아니라 Docker 컨테이너를 비롯 하 여 다양 한 호스팅 환경에서 이식성, 향상 된 성능 및 유연성을 제공 하는 다시 디자인 된 Azure Logic Apps 미리 보기 런타임으로 구동 되는 여러 워크플로를 빌드할 수 있습니다. 새 논리 앱 유형에 대 한 자세한 내용은 [Azure Logic Apps 미리 보기 개요](logic-apps-overview-preview.md)를 참조 하세요.

!["논리 앱 (미리 보기)" 리소스의 workflow designer를 사용 하 여 Azure Portal를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/azure-portal-logic-apps-overview.png)

Azure Portal에서 새 **논리 앱 (미리 보기)** 리소스를 만들어 시작할 수 있습니다. [Azure Logic Apps (미리 보기) 확장을 사용 하 여 Visual Studio Code에서 프로젝트를 만들](create-stateful-stateless-workflows-visual-studio-code.md)수도 있지만 두 가지 방법 모두 동일한 종류의 호스팅 환경에서 논리 앱을 배포 하 고 실행할 수 있는 기능을 제공 합니다.

한편으로는 여전히 원래 논리 앱 유형을 만들 수 있습니다. 포털의 개발 환경은 원본 및 새 논리 앱 유형에 따라 다르지만 Azure 구독은 두 유형을 모두 포함할 수 있습니다. Azure 구독에서 배포 된 모든 논리 앱을 보고 액세스할 수 있지만 앱은 자신의 범주 및 섹션으로 구성 됩니다.

이 문서에서는 **논리 앱 (미리 보기)** 리소스 형식을 사용 하 고 이러한 고급 작업을 수행 하 여 Azure Portal에서 논리 앱 및 워크플로를 빌드하는 방법을 보여 줍니다.

* 새 논리 앱 리소스를 만들고 빈 워크플로를 추가 합니다.

* 트리거 및 작업을 추가 합니다.

* 워크플로 실행을 트리거합니다.

* 워크플로의 실행 및 트리거 기록을 봅니다.

* 배포 후 Application Insights를 사용 하거나 엽니다.

* 상태 비저장 워크플로에 대 한 실행 기록을 사용 하도록 설정 합니다.

> [!NOTE]
> 현재 알려진 문제에 대 한 자세한 내용은 [GitHub에서 공개 미리 보기의 알려진 문제 Logic Apps 페이지](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* **논리 앱 (미리 보기)** 리소스가 Azure Functions에 의해 제공 되며 [함수 앱과 유사한 저장소 요구 사항을](../azure-functions/storage-considerations.md)가지 므로 [Azure Storage 계정](../storage/common/storage-account-overview.md) 입니다. 기존 저장소 계정을 사용 하거나 논리 앱을 만드는 동안 또는 미리 저장소 계정을 만들 수 있습니다.

  > [!NOTE]
  > [상태 저장 논리 앱](logic-apps-overview-preview.md#stateful-stateless) 은 큐를 사용 하 여 테이블 및 blob에 워크플로 상태를 예약 하 고 저장 하는 등의 저장소 트랜잭션을 수행 합니다. 이러한 트랜잭션은 [Azure Storage 요금](https://azure.microsoft.com/pricing/details/storage/)을 발생 시킵니다. 상태 저장 논리 앱에서 외부 저장소에 데이터를 저장 하는 방법에 대 한 자세한 내용은 [상태 저장 및 상태 비저장](logic-apps-overview-preview.md#stateful-stateless)을 참조 하세요.

* Docker 컨테이너에 배포 하려면 기존 Docker 컨테이너 이미지가 필요 합니다. 예를 들어 [Azure Container Registry](../container-registry/container-registry-intro.md), [App Service](../app-service/overview.md)또는 [Azure Container Instance](../container-instances/container-instances-overview.md)를 통해이 이미지를 만들 수 있습니다. 

* 이 문서에서 동일한 예제 논리 앱을 빌드하려면 Microsoft 회사 또는 학교 계정을 사용 하 여 로그인 하는 Office 365 Outlook 전자 메일 계정이 필요 합니다.

  Outlook.com 또는 [Gmail](../connectors/connectors-google-data-security-privacy-policy.md)과 같은 [Azure Logic Apps에서 지 원하는 다른 전자 메일 커넥터](/connectors/)를 사용 하도록 선택 하는 경우에도 예제를 계속 진행할 수 있지만 일반적인 전체 단계는 동일 하지만 사용자 인터페이스와 옵션은 일부 면에서 다를 수 있습니다. 예를 들어 Outlook.com 커넥터를 사용 하는 경우 대신 개인 Microsoft 계정를 사용 하 여 로그인 합니다.

* 이 문서에서 만든 예제 논리 앱을 테스트 하려면 예제 논리 앱의 첫 단계인 요청 트리거에 대 한 호출을 보낼 수 있는 도구가 필요 합니다. 이러한 도구가 없는 경우 [Postman](https://www.postman.com/downloads/)을 다운로드 하 여 설치 하 고 사용할 수 있습니다.

* [Application Insights](../azure-monitor/app/app-insights-overview.md)사용을 지 원하는 설정을 사용 하 여 논리 앱을 만드는 경우 필요에 따라 논리 앱에 대 한 진단 로깅 및 추적을 사용 하도록 설정할 수 있습니다. 논리 앱을 만들 때 또는 배포 후에이 작업을 수행할 수 있습니다. Application Insights 인스턴스가 필요 하지만, 논리 앱을 만들 때 또는 배포 후에이 리소스를 [미리](../azure-monitor/app/create-workspace-resource.md)만들 수 있습니다.

## <a name="create-the-logic-app-resource"></a>논리 앱 리소스 만들기

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal 검색 상자에 `logic app preview` 를 입력 하 고 **논리 앱 (미리 보기)** 을 선택 합니다.

   !["논리 앱 미리 보기" 검색 용어와 "논리 앱 (미리 보기)" 리소스가 선택 된 Azure Portal 검색 상자를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/find-logic-app-resource-template.png)

1. **논리 앱 (미리 보기)** 페이지에서 **추가** 를 선택 합니다.

1. **논리 앱 만들기 (미리 보기)** 페이지의 **기본 사항** 탭에서 논리 앱에 대 한이 정보를 제공 합니다.

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **구독** | 예 | <*Azure-subscription-name*> | 논리 앱에 사용할 Azure 구독입니다. |
   | **리소스 그룹** | 예 | <*Azure-resource-group-name*> | 논리 앱 및 관련 리소스를 만드는 Azure 리소스 그룹입니다. 이 리소스 이름은 지역에서 고유 해야 하며 문자, 숫자, 하이픈 ( **-** ), 밑줄 (**_**), 괄호 (**()**) 및 마침표 (**.**)만 포함할 수 있습니다. <p><p>이 예제에서는 이라는 리소스 그룹을 만듭니다 `Fabrikam-Workflows-RG` . |
   | **논리 앱 이름**. | 예 | <*logic-app-name*> | 논리 앱에 사용할 이름입니다. 이 리소스 이름은 지역에서 고유 해야 하며 문자, 숫자, 하이픈 ( **-** ), 밑줄 (**_**), 괄호 (**()**) 및 마침표 (**.**)만 포함할 수 있습니다. <p><p>이 예제에서는 라는 논리 앱을 만듭니다 `Fabrikam-Workflows` . <p><p>**참고**: `.azurewebsites.net` **논리 앱 (미리 보기)** 리소스는 동일한 앱 명명 규칙을 사용 하는 Azure Functions에 의해 구동 되기 때문에 논리 앱의 이름은 접미사를 자동으로 가져옵니다. |
   | **게시** | Yes | <*배포-환경*> | 논리 앱에 대 한 배포 대상입니다. **워크플로** 또는 **Docker 컨테이너** 를 선택 하 여 Azure에 배포할 수 있습니다. <p><p>이 예제에서는 **논리 앱 (미리 보기)** 리소스를 Azure Portal에 배포 하는 **워크플로** 를 사용 합니다. <p><p>**참고**: **docker 컨테이너** 를 선택 하기 전에 docker 컨테이너 이미지 만들기를 확인 합니다. 예를 들어 [Azure Container Registry](../container-registry/container-registry-intro.md), [App Service](../app-service/overview.md)또는 [Azure Container Instance](../container-instances/container-instances-overview.md)를 통해이 이미지를 만들 수 있습니다. 이렇게 하면 **Docker 컨테이너** 를 선택한 후 [논리 앱의 설정에서 사용 하려는 컨테이너를 지정할](#set-docker-container)수 있습니다. |
   | **지역** | 예 | <*Azure-region*> | 리소스 그룹 및 리소스를 만들 때 사용할 Azure 지역입니다. <p><p>이 예제에서는 **미국 서부** 를 사용합니다. |
   |||||

   예를 들면 다음과 같습니다.

   ![Azure Portal 및 "논리 앱 만들기 (미리 보기)" 페이지를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/create-logic-app-resource-portal.png)

1. 그런 다음 **호스팅** 탭에서 논리 앱에 사용할 저장소 솔루션 및 호스팅 계획에 대 한 정보를 제공 합니다.

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **스토리지 계정** | 예 | <*Azure-storage-account-name*> | 저장소 트랜잭션에 사용할 [Azure Storage 계정](../storage/common/storage-account-overview.md) 입니다. 이 리소스 이름은 지역에서 고유 해야 하며 숫자와 소문자만 포함 된 3-24 자 여야 합니다. 기존 계정을 선택 하거나 새 계정을 만드십시오. <p><p>이 예제에서는 라는 저장소 계정을 만듭니다 `fabrikamstorageacct` . |
   | **플랜 유형** | Yes | <*Azure 호스팅 계획*> | [**함수 프리미엄**](../azure-functions/functions-premium-plan.md) 또는 [ **app service 계획** (전용)](../azure-functions/dedicated-plan.md)인 논리 앱을 배포 하는 데 사용할 [호스팅 계획](../app-service/overview-hosting-plans.md) 입니다. 이 옵션은 나중에 사용할 수 있는 기능 및 가격 책정 계층에 영향을 줍니다. <p><p>이 예제에서는 **App service 계획** 을 사용 합니다. <p><p>**참고**: Azure Functions와 마찬가지로 **논리 앱 (미리 보기)** 리소스 종류에는 호스팅 계획 및 가격 책정 계층이 필요 합니다. 소비 계획은이 리소스 종류에 대해 지원 되지 않으며 사용할 수 없습니다. 자세한 내용은 다음 항목을 검토 하십시오. <p><p>- [Azure Functions 크기 조정 및 호스팅](../azure-functions/functions-scale.md) <br>- [App Service 가격 정보](https://azure.microsoft.com/pricing/details/app-service/) <p><p>예를 들어 프리미엄 계획 함수는 논리 앱을 만들고 배포 하는 경우와 유사 Azure Functions 하 게 Azure 가상 네트워크와의 연결 및 통합과 같은 네트워킹 기능에 대 한 액세스를 제공 합니다. 자세한 내용은 다음 항목을 검토 하십시오. <p><p>- [Azure Functions 네트워킹 옵션](../azure-functions/functions-networking-options.md) <br>- [Azure Logic Apps 미리 보기를 사용 하 여 어디서 나 Azure Logic Apps 실행-네트워킹 가능성](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047) |
   | **Windows 플랜** | Yes | <*계획-이름*> | 사용할 계획 이름입니다. 기존 계획을 선택 하거나 새 계획의 이름을 입력 합니다. <p><p>이 예제에서는 `Fabrikam-Service-Plan`이름을 사용합니다. |
   | **SKU 및 크기** | Yes | <*가격 책정 계층*> | 논리 앱을 호스트 하는 데 사용할 [가격 책정 계층](../app-service/overview-hosting-plans.md) 입니다. 선택한 사항은 이전에 선택한 계획 유형에 따라 달라 집니다. 기본 계층을 변경 하려면 **크기 변경** 을 선택 합니다. 그런 다음 필요한 워크 로드에 따라 다른 가격 책정 계층을 선택할 수 있습니다. <p><p>이 예제에서는 **개발/테스트** 워크 로드에 대 한 무료 **F1 가격 책정 계층** 을 사용 합니다. 자세한 내용은 [가격 책정 세부 정보를 App Service](https://azure.microsoft.com/pricing/details/app-service/)검토 하세요. |
   |||||

1. 그런 다음 생성 및 배포 설정에서 [Application Insights](../azure-monitor/app/app-insights-overview.md)사용을 지 원하는 경우 필요에 따라 논리 앱에 대 한 진단 로깅 및 추적을 사용 하도록 설정할 수 있습니다.

   1. **모니터링** 탭의 **Application Insights** 아래에서 **사용 Application Insights** 를 **예** 로 설정 합니다 (아직 선택 하지 않은 경우).

   1. **Application Insights** 설정에서 기존 Application Insights 인스턴스를 선택 하거나 새 인스턴스를 만들려는 경우 **새로 만들기** 를 선택 하 고 사용할 이름을 제공 합니다.

1. Azure가 논리 앱의 설정에 대 한 유효성을 검사 한 후 **검토 + 만들기** 탭에서 **만들기** 를 선택 합니다.

   예를 들면 다음과 같습니다.

   ![Azure Portal 및 새 논리 앱 리소스 설정을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/check-logic-app-resource-settings.png)

   > [!TIP]
   > **만들기** 를 선택한 후 유효성 검사 오류가 발생 하면를 열고 오류 정보를 검토 합니다. 예를 들어 선택한 지역이 만들려는 리소스의 할당량에 도달 하는 경우 다른 지역을 시험해 볼 수 있습니다.

   Azure에서 배포를 완료 한 후에는 논리 앱이 자동으로 라이브 및 실행 되지만 워크플로가 없으므로 아직 작업을 수행 하지 않습니다.

1. 배포 완료 페이지에서 워크플로 빌드를 시작할 수 있도록 **리소스로 이동** 을 선택 합니다. 논리 앱을 배포 하기 위해 **Docker 컨테이너** 를 선택한 경우 [해당 docker 컨테이너에 대 한 정보를 제공 하는 단계](#set-docker-container)를 계속 진행 합니다.

   ![Azure Portal 및 완성 된 배포를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="set-docker-container"></a>

## <a name="specify-docker-container-for-deployment"></a>배포용 Docker 컨테이너 지정

이러한 단계를 시작 하기 전에 Docker 컨테이너 이미지가 필요 합니다. 예를 들어 [Azure Container Registry](../container-registry/container-registry-intro.md), [App Service](../app-service/overview.md)또는 [Azure Container Instance](../container-instances/container-instances-overview.md)를 통해이 이미지를 만들 수 있습니다. 그런 다음 논리 앱을 만든 후 Docker 컨테이너에 대 한 정보를 제공할 수 있습니다.

1. Azure Portal에서 논리 앱 리소스로 이동 합니다.

1. 논리 앱 메뉴의 **설정** 에서 **배포 센터** 를 선택 합니다.

1. **배포 센터** 창에서 Docker 컨테이너에 대 한 세부 정보를 제공 하 고 관리 하기 위한 지침을 따릅니다.

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>빈 워크플로 추가

1. Azure가 리소스를 연 후 논리 앱의 메뉴에서 **워크플로** 를 선택 합니다. **워크플로** 도구 모음에서 **추가** 를 선택 합니다.

   !["워크플로"가 선택 된 논리 앱 리소스 메뉴를 표시 하 고 도구 모음에서 h) "추가"를 선택 하는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. **새 워크플로** 창이 열리면 워크플로의 이름을 제공 하 고 [ **상태 저장** 또는 **상태 비저장**](logic-apps-overview-preview.md#stateful-stateless) 워크플로 유형 중 하나를 선택 합니다. 완료되면 **만들기** 를 선택합니다.

   이 예에서는 라는 빈 상태 저장 워크플로를 추가 `Fabrikam-Stateful-Workflow` 합니다. 기본적으로 워크플로는 사용 하도록 설정 되어 있지만 트리거 및 동작을 추가 하기 전에는 수행할 수 없습니다.

   ![새로 추가 된 빈 상태 저장 워크플로 "Fabrikam-상태 저장 워크플로"를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. 다음으로, 트리거 및 작업을 추가할 수 있도록 디자이너에서 빈 워크플로를 엽니다.

   1. 워크플로 목록에서 빈 워크플로를 선택 합니다.

   1. 워크플로 메뉴의 **개발자** 에서 **디자이너** 를 선택 합니다.

      디자이너 화면에서 **작업 선택** 프롬프트가 이미 표시 되 고 기본적으로 선택 되어 **트리거 추가** 창이 열린 상태로 표시 됩니다.

      ![디자이너 화면에서 선택한 "작업 선택"을 사용 하 여 열린 워크플로 디자이너를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>트리거 및 작업 추가

이 예제에서는 다음 단계를 수행 하는 워크플로를 작성 합니다.

* **HTTP 요청** 을 받으면 인바운드 호출 또는 요청을 수신 하 고 다른 서비스 또는 논리 앱이 호출할 수 있는 끝점을 만드는 기본 제공 [요청 트리거입니다](../connectors/connectors-native-reqres.md).

* [Office 365 Outlook 작업](../connectors/connectors-create-api-office365-outlook.md)에서 **전자 메일을 보냅니다**.

* 회신을 보내고 호출자에 게 데이터를 반환 하는 데 사용 하는 기본 제공 [응답 동작](../connectors/connectors-native-reqres.md)입니다.

### <a name="add-the-request-trigger"></a>요청 트리거 추가

빈 워크플로에 트리거를 추가 하려면 먼저 workflow designer가 열려 있고 디자이너 화면에서 **작업 선택** 프롬프트가 선택 되어 있는지 확인 합니다.

1. 디자이너 화면 옆의 **트리거 추가** 창에서 **작업 선택** 검색 상자 아래에 **기본 제공** 탭이 선택 되어 있는지 확인 합니다. 이 탭에는 기본적으로 Azure Logic Apps에서 실행 되는 트리거가 표시 됩니다.

1. **작업 선택** 검색 상자에를 입력 하 `when a http request` 고 **HTTP 요청을 받을 때** 이름이 지정 된 기본 제공 요청 트리거를 선택 합니다.

   ![디자이너를 표시 하는 스크린샷 및 "HTTP 요청을 받은 경우" 트리거가 선택 된 상태에서 * * 트리거 추가 * * 창을 표시 합니다.](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger.png)

   트리거가 디자이너에 나타나면 트리거의 세부 정보 창이 열리며 트리거의 속성, 설정 및 기타 작업을 표시 합니다.

   !["HTTP 요청을 받았을 때" 트리거를 선택 하 고 트리거 세부 정보 창이 열려 있는 디자이너를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > 세부 정보 창이 표시 되지 않으면 디자이너에서 트리거를 선택 해야 합니다.

1. 디자이너에서 항목을 삭제 해야 하는 경우 [디자이너에서 항목](#delete-from-designer)을 삭제 하려면 다음 단계를 수행 합니다.

1. 작업을 저장 하려면 디자이너 도구 모음에서 **저장** 을 선택 합니다.

   워크플로를 처음 저장할 때 워크플로를 요청 트리거로 시작 하면 Logic Apps 서비스에서 요청 트리거에 의해 생성 된 끝점에 대 한 URL이 자동으로 생성 됩니다. 나중에 워크플로를 테스트할 때 트리거를 발생 시키고 워크플로 실행을 시작 하는이 URL로 요청을 보냅니다.

### <a name="add-the-office-365-outlook-action"></a>Office 365 Outlook 작업 추가

1. 디자이너에서 추가한 트리거에서 **새 단계** 를 선택 합니다.

   **작업 선택** 프롬프트가 디자이너에 표시 되며 다음 작업을 선택할 수 있도록 **작업 추가** 창이 다시 열립니다.

   > [!NOTE]
   > **작업 추가** 창에 ' ' undefined ' 속성을 읽을 수 없습니다. ' 라는 오류 메시지가 표시 되 면 워크플로를 저장 하 고 페이지를 다시 로드 하 고 워크플로를 다시 연 후 다시 시도 하세요.

1. 작업 **추가** 창의 **작업 선택** 검색 상자 아래에서 **Azure** 를 선택 합니다. 이 탭에는 Azure에서 사용 가능 하 고 배포 된 관리 되는 커넥터가 표시 됩니다.

   > [!NOTE]
   > **작업 추가** 창에 오류 메시지가 표시 되 면 워크플로를 저장 하 고 `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'` 페이지를 다시 로드 하 고 워크플로를 다시 연 후 작업을 다시 추가 해 봅니다.

   이 예제에서는 **전자 메일 보내기 (V2)** 라는 Office 365 Outlook 작업을 사용 합니다.

   ![Office 365 Outlook "전자 메일 보내기" 작업을 선택 하 여 디자이너 및 * * 작업 추가 * * 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/find-send-email-action.png)

1. 작업의 세부 정보 창에 있는 **연결 만들기** 탭에서 **로그인** 을 선택 하 여 전자 메일 계정에 대 한 연결을 만들 수 있습니다.

   ![디자이너와 "로그인"이 선택 된 "전자 메일 보내기 (V2)" 세부 정보 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-sign-in.png)

1. 전자 메일 계정에 액세스 하는 데 동의 하는지 묻는 메시지가 표시 되 면 계정 자격 증명을 사용 하 여 로그인 합니다.

   > [!NOTE]
   > 오류 메시지가 표시 되 면 브라우저에서 `Failed with error: 'The browser is closed.'. Please sign in again` 타사 쿠키를 차단 하는지 확인 합니다. 이러한 쿠키가 차단 된 경우 `https://portal.azure.com` 쿠키를 사용할 수 있는 사이트 목록에 추가 해 보세요. Incognito 모드를 사용 하는 경우 해당 모드에서 작업 하는 동안 타사 쿠키가 차단 되지 않았는지 확인 합니다.
   > 
   > 필요한 경우 페이지를 다시 로드 하 고 워크플로를 연 다음 전자 메일 작업을 다시 추가 하 고 연결을 만듭니다.

   Azure에서 연결을 만든 후에는 **전자 메일 보내기** 작업이 디자이너에 표시 되며 기본적으로 선택 됩니다. 작업을 선택 하지 않은 경우 세부 정보 창도 열리도록 작업을 선택 합니다.

1. 작업의 세부 정보 창에 있는 **매개 변수** 탭에서 작업에 필요한 정보를 제공 합니다. 예를 들면 다음과 같습니다.

   ![디자이너와 "매개 변수" 탭이 선택 된 "전자 메일 보내기" 세부 정보 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-details.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **수행할 작업** | 예 | <*your-email-address*> | 전자 메일 받는 사람으로, 테스트 목적으로 전자 메일 주소를 사용할 수 있습니다. 이 예에서는 가상의 전자 메일를 사용 `sophiaowen@fabrikam.com` 합니다. |
   | **Subject** | 예 | `An email from your example workflow` | 이메일 제목 |
   | **본문** | 예 | `Hello from your example workflow!` | 전자 메일 본문 내용 |
   ||||

   > [!NOTE]
   > 세부 정보 창에서 **설정**, **정적 결과** 또는 **다음 이후에 실행** 을 변경 하는 경우 탭을 전환 하거나 디자이너로 포커스를 변경 하기 전에 **완료** 를 선택 하 여 이러한 변경 내용을 커밋해야 합니다. 그렇지 않으면 디자이너는 변경 내용을 유지 하지 않습니다.

1. 작업을 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

1. 환경에 엄격한 네트워크 요구 사항이 나 트래픽을 제한 하는 방화벽이 있는 경우 워크플로에 있는 트리거 또는 작업 연결에 대 한 사용 권한을 설정 해야 합니다. 정규화 된를 찾으려면 

   그렇지 않고 워크플로를 테스트 하려면 [실행을 수동으로 트리거합니다](#trigger-workflow).

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>방화벽 액세스를 위한 도메인 이름 찾기

논리 앱을 배포 하 고 Azure Portal에서 워크플로를 실행 하기 전에 환경에 엄격한 네트워크 요구 사항이 나 트래픽을 제한 하는 방화벽이 있는 경우 논리 앱에 존재 하는 워크플로의 트리거 또는 작업 연결에 대 한 네트워크 또는 방화벽 권한을 설정 해야 합니다.

이러한 연결에 대 한 Fqdn (정규화 된 도메인 이름)을 찾으려면 다음 단계를 수행 합니다.

1. 논리 앱 메뉴의 **워크플로** 아래에서 **연결** 을 선택 합니다. **API 연결** 탭에서 연결의 리소스 이름을 선택 합니다. 예를 들면 다음과 같습니다.

   !["Connections" 및 "offic365" 연결 리소스 이름을 선택 하 여 Azure Portal 및 논리 앱 메뉴를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connections.png)

1. 브라우저의 오른쪽 위 모서리에 **Json 보기가** 표시 되 면 **json 보기** 를 선택 하 여 브라우저의 너비를 충분히 확장 합니다.

   !["JSON 뷰"를 선택 하 여 Azure Portal 및 API 연결 창을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connection-view-json.png)

1. `connectionRuntimeUrl`이 정보를 사용 하 여 방화벽을 설정할 수 있도록 안전한 위치에 속성 값을 찾아서 복사 하 고 저장 합니다.

   !["ConnectionRuntimeUrl" 속성 값이 선택 된 것을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connection-runtime-url.png)

1. 각 연결에 대해 관련 단계를 반복 합니다.

<a name="trigger-workflow"></a>

## <a name="trigger-the-workflow"></a>워크플로 트리거

이 예제에서 워크플로는 요청 트리거가 인바운드 요청을 받을 때 실행 되며,이 요청은 트리거로 만든 끝점에 대 한 URL로 전송 됩니다. 처음으로 워크플로를 저장 하는 경우 Logic Apps 서비스에서 자동으로이 URL을 생성 합니다. 따라서 워크플로를 트리거하기 위해이 요청을 보내기 전에이 URL을 찾아야 합니다.

1. Workflow designer에서 **HTTP 요청을 받을 때** 이름이 지정 된 요청 트리거를 선택 합니다.

1. 세부 정보 창이 열리면 **매개 변수** 탭에서 **HTTP POST URL** 속성을 찾습니다. 생성 된 URL을 복사 하려면 **복사 url** (파일 복사 아이콘)을 선택 하 고 다른 위치에 url을 저장 합니다. URL은 다음 형식을 따릅니다.

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   !["HTTP POST URL" 속성에 요청 트리거와 끝점 URL이 있는 디자이너를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger-url.png)

   이 예제에서 URL은 다음과 같습니다.

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > **워크플로 url** 속성의 논리 앱 **개요** 창에서 끝점 url을 찾을 수도 있습니다.
   >
   > 1. 리소스 메뉴에서 **개요** 를 선택 합니다.
   > 1. **개요** 창에서 **워크플로 URL** 속성을 찾습니다.
   > 1. 끝점 URL을 복사 하려면 끝점 URL 텍스트 끝 위로 포인터를 이동한 다음 **클립보드로 복사** (파일 복사 아이콘)를 선택 합니다.

1. 요청을 전송 하 여 URL을 테스트 하려면 요청을 만들고 보내기 위한 [Postman](https://www.postman.com/downloads/) 또는 선호 하는 도구를 엽니다.

   이 예는 Postman을 사용 하 여 계속 합니다. 자세한 내용은 [Postman 시작](https://learning.postman.com/docs/getting-started/introduction/)을 참조 하세요.

   1. Postman 도구 모음에서 **새로 만들기** 를 선택 합니다.

      ![새 단추가 선택 된 Postman을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/postman-create-request.png)

   1. **새로 만들기** 창의 **구성 요소** 아래에서 **요청** 을 선택 합니다.

   1. 요청 **저장** 창의 **요청 이름** 에서 요청 이름을 제공 합니다 (예:) `Test workflow trigger` .

   1. **저장할 컬렉션 또는 폴더 선택** 에서 **컬렉션 만들기** 를 선택 합니다.

   1. **모든 컬렉션** 아래에서 요청을 구성 하기 위해 만들 컬렉션의 이름을 입력 하 고 enter 키를 누른 다음 ***컬렉션 이름* > <저장을** 선택 합니다. 이 예제에서는 `Logic Apps requests` 를 컬렉션 이름으로 사용 합니다.

      요청 트리거에 대 한 끝점 URL에 요청을 보낼 수 있도록 postman의 요청 창이 열립니다.

      ![열린 요청 창에서 Postman을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/postman-request-pane.png)

   1. 요청 창의 메서드 목록 옆에 있는 주소 상자에서 현재 기본 요청 메서드로 **가져오기를** 표시 하 고 이전에 복사한 URL을 붙여넣은 다음 **보내기** 를 선택 합니다.

      ![보내기 단추가 선택 된 주소 상자에 Postman 및 끝점 URL을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/postman-test-endpoint-url.png)

      트리거가 발생 하면 예제 워크플로가 실행 되 고 다음 예제와 비슷한 전자 메일을 보냅니다.

      ![예제에 설명 된 대로 Outlook 전자 메일을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/workflow-app-result-email.png)

<a name="view-run-history"></a>

## <a name="review-run-history"></a>실행 기록 검토

상태 저장 워크플로의 경우 각 워크플로를 실행 한 후에 전체 실행에 대 한 상태, 트리거에 대 한 상태 및 각 작업에 대 한 입력 및 출력과 함께 실행 기록을 볼 수 있습니다. Azure Portal에서 실행 기록 및 트리거 기록은 논리 앱 수준이 아닌 워크플로 수준에서 표시 됩니다. 실행 기록 컨텍스트 외부에서 트리거 기록을 검토 하려면 [트리거 기록 검토](#view-trigger-histories)를 참조 하세요.

1. Azure Portal 워크플로의 메뉴에서 **모니터** 를 선택 합니다.

   **모니터** 창에 해당 워크플로의 실행 기록이 표시 됩니다.

   ![워크플로의 "모니터" 창 및 실행 기록을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > 가장 최근 실행 상태가 표시 되지 않으면 **모니터** 창 도구 모음에서 **새로 고침** 을 선택 합니다. 충족 되지 않은 조건으로 인해 건너뛴 트리거와 데이터를 찾을 수 없는 경우에는 실행 되지 않습니다.

   | 실행 상태 | 설명 |
   |------------|-------------|
   | **중단됨** | 시스템이 중단 되거나 Azure 구독이 중단 된 등의 외부 문제로 인해 실행이 중지 되었거나 완료 되지 않았습니다. |
   | **취소** | 실행이 트리거되고 시작 되었지만 취소 요청이 수신 되었습니다. |
   | **실패** | 실행에 하나 이상의 작업이 실패 했습니다. 워크플로의 후속 작업이 실패를 처리 하도록 설정 되지 않았습니다. |
   | **실행 중** | 실행이 트리거 되었지만 진행 중이지만, [작업 제한](logic-apps-limits-and-config.md) 또는 [현재 요금제](https://azure.microsoft.com/pricing/details/logic-apps/)로 인해 제한 된 실행에 대해서도이 상태가 표시 될 수 있습니다. <p><p>**팁**: [진단 로깅을](monitor-logic-apps-log-analytics.md)설정 하는 경우 발생 하는 스로틀 이벤트에 대 한 정보를 얻을 수 있습니다. |
   | **성공함** | 실행이 성공 했습니다. 작업이 실패 하면 워크플로의 후속 작업에서 해당 실패를 처리 합니다. |
   | **시간 초과됨** | 현재 기간이 실행 [ **기록 보존 기간 (일)** 설정](logic-apps-limits-and-config.md#run-duration-retention-limits)에 의해 제어 되는 실행 기간 제한을 초과 하 여 실행 시간이 초과 되었습니다. 실행의 기간은 시작 시간에서 실행의 시작 시간 및 실행 기간 제한을 사용 하 여 계산 됩니다. <p><p>**참고**: 실행 기간이 현재 *실행 기록 보존* 한도를 초과 하는 경우에도 실행 기록 보존 기간 ( [ **일)** 설정](logic-apps-limits-and-config.md#run-duration-retention-limits)에 의해 제어 되는 경우 매일 정리 작업을 통해 실행 기록에서 실행이 지워집니다. 실행 제한 시간이 초과 되거나 완료 되는지 여부는 항상 실행의 시작 시간 및 *현재* 보존 제한을 사용 하 여 계산 됩니다. 따라서 진행 중인 실행에 대 한 기간 제한을 줄이면 실행 제한 시간이 초과 됩니다. 그러나 실행 기간이 보존 한도를 초과 했는지 여부에 따라 실행 기록에서 실행이 유지 되거나 지워집니다. |
   | **대기 중** | 예를 들어 아직 실행 중인 이전 워크플로 인스턴스로 인해 실행이 시작 되지 않았거나 일시 중지 되었습니다. |
   |||

1. 실행의 각 단계에 대 한 상태를 검토 하려면 검토 하려는 실행을 선택 합니다.

   실행 세부 정보 뷰가 열리고 실행의 각 단계에 대 한 상태가 표시 됩니다.

   ![워크플로의 각 단계에 대 한 상태와 함께 실행 세부 정보 보기를 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/review-run-details.png)

   워크플로의 각 단계에 사용할 수 있는 상태는 다음과 같습니다.

   | 작업 상태 | 아이콘 | 설명 |
   |---------------|------|-------------|
   | **중단됨** | !["중단 됨" 작업 상태 아이콘][aborted-icon] | 시스템이 중단 되었거나 Azure 구독이 중단 된 등의 외부 문제로 인해 작업이 중지 되었거나 완료 되지 않았습니다. |
   | **취소** | !["취소 됨" 작업 상태 아이콘][cancelled-icon] | 작업이 실행 중이지만 취소 요청이 수신 되었습니다. |
   | **실패** | !["실패" 작업 상태 아이콘][failed-icon] | 작업이 실패 했습니다. |
   | **실행 중** | !["실행 중" 작업 상태 아이콘][running-icon] | 작업이 현재 실행 되 고 있습니다. |
   | **생략** | !["건너뜀" 작업 상태 아이콘][skipped-icon] | 바로 이전 작업이 실패 했으므로 작업을 건너뛰었습니다. 작업에는 `runAfter` 이전 작업이 성공적으로 완료 되어야 현재 작업을 실행할 수 있도록 하는 조건이 있습니다. |
   | **성공함** | !["성공" 작업 상태 아이콘][succeeded-icon] | 작업이 성공 했습니다. |
   | **다시 시도 성공** | !["재시도 성공" 작업 상태 아이콘][succeeded-with-retries-icon] | 하나 이상의 재시도 후에만 작업이 성공 했습니다. 다시 시도 기록을 검토 하려면 실행 기록 세부 정보 보기에서 해당 작업을 선택 하 여 입력 및 출력을 볼 수 있습니다. |
   | **시간 초과됨** | !["시간이 초과 되었습니다." 작업 상태 아이콘][timed-out-icon] | 해당 작업의 설정에 지정 된 제한 시간 제한으로 인해 작업이 중지 되었습니다. |
   | **대기 중** | !["대기 중" 작업 상태 아이콘][waiting-icon] | 호출자의 인바운드 요청을 기다리는 webhook 작업에 적용 됩니다. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-azure-portal/waiting.png

1. 특정 단계에 대 한 입력 및 출력을 검토 하려면 해당 단계를 선택 합니다.

   ![선택한 "전자 메일 보내기" 작업의 입력 및 출력을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/review-step-inputs-outputs.png)

1. 해당 단계에 대 한 원시 입력 및 출력을 추가로 검토 하려면 **원시 입력 표시** 또는 **원시 출력 표시** 를 선택 합니다.

<a name="view-trigger-histories"></a>

## <a name="review-trigger-histories"></a>트리거 기록 검토

상태 저장 워크플로의 경우 [실행 기록 컨텍스트와](#view-run-history)별도로 입력 및 출력과 함께 트리거 상태를 포함 하 여 각 실행에 대 한 트리거 기록을 검토할 수 있습니다. Azure Portal에서 트리거 기록 및 실행 기록은 논리 앱 수준이 아닌 워크플로 수준에 나타납니다. 이 기록 데이터를 찾으려면 다음 단계를 수행 합니다.

1. Azure Portal 워크플로의 메뉴에 있는 **개발자** 에서 **트리거 기록** 을 선택 합니다.

   **트리거 기록** 창에는 워크플로의 실행에 대 한 트리거 기록이 표시 됩니다.

1. 특정 트리거 기록을 검토 하려면 해당 실행에 대 한 ID를 선택 합니다.

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>배포 후 Application Insights 사용 또는 열기

워크플로를 실행 하는 동안 논리 앱은 다른 이벤트와 함께 원격 분석을 내보냅니다. 이 원격 분석을 사용 하 여 워크플로가 얼마나 잘 실행 되는지와 Logic Apps 런타임이 다양 한 방식으로 작동 하는 방식을 보다 잘 파악할 수 있습니다. 거의 실시간으로 원격 분석 (라이브 메트릭)을 제공 하는 [Application Insights](../azure-monitor/app/app-insights-overview.md)을 사용 하 여 워크플로를 모니터링할 수 있습니다. 이 기능을 통해이 데이터를 사용 하 여 문제를 진단 하 고, 경고를 설정 하 고, 차트를 작성 하는 경우 오류 및 성능 문제를 보다 쉽게 조사할 수 있습니다.

논리 앱의 만들기 및 배포 설정에서 [Application Insights](../azure-monitor/app/app-insights-overview.md)사용을 지 원하는 경우 필요에 따라 논리 앱에 대 한 진단 로깅 및 추적을 사용 하도록 설정할 수 있습니다. Azure Portal 또는 배포 후에 논리 앱을 만들 때 이러한 작업을 수행할 수 있습니다. Application Insights 인스턴스가 필요 하지만, 논리 앱을 만들 때 또는 배포 후에이 리소스를 [미리](../azure-monitor/app/create-workspace-resource.md)만들 수 있습니다.

배포 된 논리 앱에 대 한 Application Insights를 사용 하도록 설정 하거나 이미 사용 하도록 설정 된 경우 Application Insights 대시보드를 열려면 다음 단계를 수행 합니다.

1. Azure Portal에서 배포 된 논리 앱을 찾습니다.

1. 논리 앱 메뉴의 **설정** 에서 **Application Insights** 를 선택 합니다.

1. Application Insights 사용 하도록 설정 되지 않은 경우 **Application Insights** 창에서 **Application Insights 켜기** 를 선택 합니다. 창 업데이트 후 아래쪽에서 **적용** 을 선택 합니다.

   Application Insights 사용 하도록 설정 된 경우 **Application Insights** 창에서 **Application Insights 데이터 보기** 를 선택 합니다.

Application Insights 열리면 논리 앱에 대 한 다양 한 메트릭을 검토할 수 있습니다. 자세한 내용은 다음 항목을 검토 하십시오.

* [거의 모든 위치에서 실행 하는 Azure Logic Apps-Application Insights를 사용 하 여 모니터링-1 부](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [어디에서 나 실행 Azure Logic Apps Application Insights를 사용 하 여 모니터링-2 부](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>상태 비저장 워크플로에 대 한 실행 기록 사용

상태 비저장 워크플로를 더 쉽게 디버깅 하려면 해당 워크플로에 대 한 실행 기록을 사용 하도록 설정한 다음 완료 되 면 실행 기록을 사용 하지 않도록 설정 하면 됩니다. Azure Portal에 대해 다음 단계를 수행 하거나 Visual Studio Code에서 작업 하는 경우 [Visual Studio Code에서 상태 저장 및 상태 비저장 워크플로 만들기](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)를 참조 하세요.

1. [Azure Portal](https://portal.azure.com)에서 **논리 앱 (미리 보기)** 리소스를 찾아 엽니다.

1. 논리 앱 메뉴의 **설정** 에서 **구성** 을 선택 합니다.

1. **응용 프로그램 설정** 탭에서 **새 응용 프로그램 설정** 을 선택 합니다.

1. **응용 프로그램 설정 추가/편집** 창의 **이름** 상자에 다음 작업 옵션 이름을 입력 합니다. 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. **값** 상자에 다음 값을 입력 합니다.`WithStatelessRunHistory`

   예를 들면 다음과 같습니다.

   !["구성" > "새 응용 프로그램 설정" < "새 응용 프로그램 설정", "응용 프로그램 설정 추가/편집" 창이 열리고 "워크플로를 사용 하 여 Azure Portal 및 논리 앱 (미리 보기) 리소스를 보여 주는 스크린샷 {{Workflowname}. OperationOptions "옵션을" WithStatelessRunHistory "로 설정 합니다.](./media/create-stateful-stateless-workflows-azure-portal/stateless-operation-options-run-history.png)

1. 이 작업을 완료 하려면 **확인** 을 선택 합니다. **구성** 창 도구 모음에서 **저장** 을 선택 합니다.

1. 완료 되 면 실행 기록을 비활성화 하려면 `Workflows.{yourWorkflowName}.OperationOptions` 속성을로 설정 `None` 하거나 속성 및 해당 값을 삭제 합니다.

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>디자이너에서 항목 삭제

디자이너에서 워크플로의 항목을 삭제 하려면 다음 단계 중 하나를 수행 합니다.

* 항목을 선택 하 고, 항목의 바로 가기 메뉴 (Shift + F10)를 열고, **삭제** 를 선택 합니다. 확인하려면 **확인** 을 선택합니다.

* 항목을 선택 하 고 delete 키를 누릅니다. 확인하려면 **확인** 을 선택합니다.

* 항목을 선택 하면 해당 항목에 대 한 세부 정보 창이 열립니다. 창의 오른쪽 위 모서리에서 줄임표 (**...**) 메뉴를 열고 **삭제** 를 선택 합니다. 확인하려면 **확인** 을 선택합니다.

  ![디자이너에서 열려 있는 세부 정보 창에 선택한 줄임표 단추와 "Delete" 명령이 있는 선택한 항목을 보여 주는 스크린샷](./media/create-stateful-stateless-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > 줄임표 메뉴가 표시 되지 않는 경우 세부 정보 창의 오른쪽 위 모퉁이에 줄임표 (**...**) 단추가 표시 되도록 브라우저 창을 충분히 넓게 확장 합니다.

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>문제 및 오류 해결

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>이전에 만든 워크플로의 디자이너 선택기에서 새 트리거 및 작업이 누락 됨

Azure Logic Apps 미리 보기는 Azure 함수 작업, 액체 작업 및 xml 작업 (예: xml **유효성 검사** 및 **변환 xml**)에 대 한 기본 제공 작업을 지원 합니다. 그러나 이전에 만든 논리 앱의 경우 논리 앱에서 확장 번들의 오래 된 버전을 사용 하는지 여부를 선택할 수 있도록 이러한 작업이 디자이너에 표시 되지 않을 수 있습니다 `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

이 문제를 해결 하려면 확장 번들을 최신 버전으로 자동으로 업데이트할 수 있도록 다음 단계에 따라 오래 된 버전을 삭제 합니다.

> [!NOTE]
> 이 특정 솔루션은 Visual Studio Code 및 Azure Logic Apps (미리 보기) 확장을 사용 하 여 만들고 배포 하는 논리 앱이 아니라 Azure Portal를 사용 하 여 만든 **논리 앱 (미리 보기)** 리소스에만 적용 됩니다. [Visual Studio Code의 디자이너에서 지원 되는 트리거 및 작업이 누락](create-stateful-stateless-workflows-visual-studio-code.md#missing-triggers-actions)되었습니다 .를 참조 하세요.

1. Azure Portal에서 논리 앱을 중지 합니다.

   1. 논리 앱 메뉴에서 **개요** 를 선택합니다.

   1. **개요** 창의 도구 모음에서 **중지** 를 선택 합니다.

1. 논리 앱 메뉴의 **개발 도구** 에서 **고급 도구** 를 선택 합니다.

1. **고급 도구** 창에서 **이동** 을 선택 하 여 논리 앱에 대 한 Kudu 환경을 엽니다.

1. Kudu 도구 모음에서 **디버그 콘솔** 메뉴를 열고 **CMD** 를 선택 합니다. 

   명령 프롬프트를 사용 하 여 번들 폴더로 이동할 수 있도록 콘솔 창이 열립니다. 또는 콘솔 창에 표시 되는 디렉터리 구조를 찾아볼 수 있습니다.

1. 기존 번들의 버전이 있는 폴더를 포함 하는 다음 폴더로 이동 합니다.

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. 기존 번들에 대 한 버전 폴더를 삭제 합니다. 콘솔 창에서 다음 명령을 실행 하 여 `{bundle-version}` 기존 버전으로 바꿀 수 있습니다.

   `rm -rf {bundle-version}`

   예: `rm -rf 1.1.3`

   > [!TIP]
   > "사용 권한이 거부 되었습니다." 또는 "사용 중인 파일"과 같은 오류가 발생 하는 경우 브라우저에서 페이지를 새로 고치고 폴더를 삭제할 때까지 이전 단계를 다시 시도 합니다.

1. Azure Portal에서 논리 앱의 **개요** 페이지로 돌아가서 **다시 시작** 을 선택 합니다.

   포털에서 자동으로 최신 번들을 가져오고 사용 합니다.

## <a name="next-steps"></a>다음 단계

이 공개 미리 보기를 사용 하 여 사용자의 경험에 대 한 의견을 듣고 싶습니다.

* 버그 또는 문제의 경우 [GitHub에서 문제를 만듭니다](https://github.com/Azure/logicapps/issues).
* 질문, 요청, 의견 및 기타 피드백은 [이 사용자 의견 양식을 사용](https://aka.ms/lafeedback)합니다.
