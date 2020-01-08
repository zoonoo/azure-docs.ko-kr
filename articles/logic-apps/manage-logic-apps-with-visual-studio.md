---
title: 클라우드 탐색기와 함께 Visual Studio를 사용 하 여 논리 앱 편집 및 관리
description: Visual Studio를 사용 하 여 편집, 업데이트, 관리, 소스 제어에 추가 및 논리 앱 배포 클라우드 탐색기
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 10/29/2019
ms.openlocfilehash: ed48082c52a5b4f79fd2030303dbe2bb7bedafe6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456591"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Visual Studio로 논리 앱 관리

[Azure Portal](https://portal.azure.com)에서 논리 앱을 만들고, 편집 하 고, 관리 하 고, 배포할 수 있지만, 소스 제어에 논리 앱을 추가 하 고, 서로 다른 버전을 게시 하 고, 다양 한 배포 환경에 대 한 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 템플릿을 만들 때 Visual Studio를 사용할 수도 있습니다. Visual Studio 클라우드 탐색기로 다른 Azure 리소스와 함께 논리 앱을 찾고 관리할 수 있습니다. 예를 들어 Azure Portal에서 이미 배포된 논리 앱을 열고, 다운로드하고, 편집하고, 실행하고, 실행 기록을 보고, 해제하고, 설정할 수 있습니다. Visual Studio에서 Azure Logic Apps를 작업하는 데 익숙하지 않다면 [Visual Studio로 논리 앱 만들기](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)를 참조하세요.

> [!IMPORTANT]
> Visual Studio에서 논리 앱을 배포하거나 게시하면 Azure Portal에서 해당 앱의 버전을 덮어씁니다. 따라서 Azure Portal에서 변경한 내용을 계속 유지하려면 다음에 Visual Studio에서 배포 또는 게시하기 전에 Azure Portal에서 [Visual Studio를 사용하여 논리 앱을 새로 고침](#refresh)해야 합니다.

<a name="requirements"></a>

## <a name="prerequisites"></a>필수 조건

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 다음 도구가 없으면 다운로드하여 설치합니다.

  * [Visual Studio 2019, 2017 또는 2015 - Community Edition 이상](https://aka.ms/download-visual-studio). 이 빠른 시작에서는 무료로 제공되는 Visual Studio Community 2017을 사용합니다.

    > [!IMPORTANT]
    > Visual Studio 2019 또는 2017을 설치할 때는 **Azure 개발** 워크로드를 선택해야 합니다.
    > 자세한 내용은 [Visual Studio에서 Azure 계정과 연결 된 리소스 관리 클라우드 탐색기](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view)를 참조 하세요.

    Visual Studio 2015에 대 한 클라우드 탐색기를 설치 하려면 [Visual Studio Marketplace에서 클라우드 탐색기를 다운로드](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)합니다. 자세한 내용은 [Visual Studio에서 Azure 계정과 연결 된 리소스 관리 클라우드 탐색기 (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015)를 참조 하세요.

  * [Azure SDK (2.9.1 이상)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * 원하는 버전에 대한 Visual Studio 확장용 최신 Azure Logic Apps 도구는 다음과 같습니다.

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Visual Studio Marketplace에서 직접 Azure Logic Apps 도구를 다운로드해 설치하거나 [Visual Studio 내에서 이 확장을 설치하는 방법](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)을 알아볼 수 있습니다. 설치를 완료하면 Visual Studio를 다시 시작하도록 합니다.

* 포함된 Logic Apps 디자이너를 사용하는 동안 웹에 액세스

  디자이너가 Azure에서 리소스를 만들고 논리 앱의 커넥터에서 속성 및 데이터를 읽기 위해서는 인터넷 연결이 필요합니다. 예를 들어, Dynamics CRM Online 커넥터를 사용하는 경우 디자이너는 사용 가능한 사용자 지정 및 기본 속성에 대한 CRM 인스턴스를 확인합니다.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>논리 앱 찾기

Visual Studio에서 클라우드 탐색기를 사용하여 Azure 구독에 연결되고 Azure Portal에서 배포된 모든 논리 앱을 찾을 수 있습니다.

1. Visual Studio를 엽니다. **보기** 메뉴에서 **클라우드 탐색기**를 선택합니다.

1. 클라우드 탐색기에서 **계정 관리**를 선택 합니다. 논리 앱과 연결 된 Azure 구독을 선택 하 고 **적용**을 선택 합니다. 예:

   !["계정 관리"를 선택 합니다.](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. **리소스 그룹**으로 검색하는지 아니면 **리소스 종류**로 검색하는지에 따라 다음 단계를 수행합니다.

   * **리소스 그룹**: Azure 구독 아래에서 클라우드 탐색기가 해당 구독과 연결된 모든 리소스 그룹을 표시합니다. 논리 앱이 포함 된 리소스 그룹을 확장 한 다음 논리 앱을 선택 합니다.

   * **리소스 종류**: Azure 구독에서 **Logic Apps**를 확장합니다. 클라우드 탐색기가 구독과 연결된 모든 배포된 논리 앱을 표시하면 논리 앱을 선택합니다.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Visual Studio에서 열기

Visual Studio에서는 Visual Studio를 사용 하 여 Azure Portal 또는 Azure 리소스 그룹 프로젝트로 직접 만들고 배포한 논리 앱을 열 수 있습니다.

1. 클라우드 탐색기를 열고 논리 앱을 찾습니다.

1. 논리 앱의 바로 가기 메뉴에서 **논리 앱 편집기로 열기**를 선택합니다.

   > [!TIP]
   > Visual Studio 2019에 이 명령이 없으면 Visual Studio에 대한 최신 업데이트가 있는지 확인합니다.

   이 예제는 리소스 종류별로 논리 앱을 표시하므로 논리 앱이 **Logic Apps** 섹션 아래에 표시됩니다.

   ![Azure Portal에서 배포된 논리 앱 열기](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   논리 앱이 Logic Apps Designer에서 열리면 디자이너의 맨 아래에서 **코드 보기** 를 선택 하 여 기본 논리 앱 정의 구조를 검토할 수 있습니다. 논리 앱에 대한 배포 템플릿을 만들려면 해당 논리 앱에 대한 [Azure Resource Manager 템플릿을 다운로드하는 방법](#download-logic-app)을 살펴보세요. [Resource Manager 템플릿](../azure-resource-manager/template-deployment-overview.md)에 대해 자세히 알아보세요.

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Azure에서 다운로드

[Azure Portal](https://portal.azure.com)에서 논리 앱을 다운로드하여 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 템플릿으로 저장할 수 있습니다. 그런 다음, Visual Studio를 사용하여 로컬로 템플릿을 편집하고 다른 배포 환경에 적합하도록 사용자 지정할 수 있습니다.  논리 앱을 자동으로 다운로드하면 해당 정의가 [Resource Manager 템플릿](../azure-resource-manager/template-deployment-overview.md) 내에서 *매개 변수화*되며, 템플릿도 JSON(JavaScript Object Notation)을 사용합니다.

1. Visual Studio에서 클라우드 탐색기를 엽니다. Azure에서 다운로드 하려는 논리 앱을 찾아 선택 합니다.

1. 해당 앱의 바로 가기 메뉴에서 **논리 앱 편집기로 열기**를 선택합니다.

   > [!TIP]
   > Visual Studio 2019에 이 명령이 없으면 Visual Studio에 대한 최신 업데이트가 있는지 확인합니다.

   논리 앱 디자이너가 열리고 논리 앱이 표시됩니다. 논리 앱의 기본 정의 및 구조를 검토 하려면 디자이너의 맨 아래에서 **코드 보기**를 선택 합니다.

1. 디자이너 도구 모음에서 **다운로드**를 선택 합니다.

   ![Azure Portal에서 논리 앱 다운로드](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. 위치를 묻는 메시지가 표시되면 해당 위치로 이동하여 논리 앱 정의에 대한 Resource Manager 템플릿을 JSON(.json) 파일 형식으로 저장합니다.

   논리 앱 정의는 Resource Manager 템플릿 내부의 `resources` 하위 섹션에 표시됩니다. 이제 Visual Studio를 사용하여 논리 앱 정의 및 Resource Manager 템플릿을 편집할 수 있습니다. [Azure 리소스 그룹 프로젝트로](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) 템플릿을 Visual Studio 솔루션에 추가할 수도 있습니다. [Visual Studio에서 논리 앱에 대 한 Azure 리소스 그룹 프로젝트](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)에 대해 알아봅니다.

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>통합 계정에 연결

B2B (기업 간) 엔터프라이즈 통합 시나리오에 대 한 논리 앱을 구축 하기 위해 논리 앱과 동일한 지역에 있는 이전에 만든 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 에 논리 앱을 연결할 수 있습니다. 통합 계정은 거래 업체, 규약, 스키마 및 맵과 같은 B2B 아티팩트를 포함 하 고 있으며, 논리 앱은 XML 유효성 검사 및 플랫 파일 인코딩 또는 디코딩에 B2B 커넥터를 사용할 수 있습니다. [Azure Portal를 사용 하 여이 링크를 만들](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)수 있지만, [필수 구성 요소](#requirements)를 충족 한 후에도 Visual Studio를 사용할 수 있으며, 논리 앱은 [Azure 리소스 그룹 프로젝트](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)내에 json (json) 파일로 존재 합니다. [Visual Studio에서 논리 앱에 대 한 Azure 리소스 그룹 프로젝트](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project)에 대해 알아봅니다.

1. Visual Studio에서 논리 앱을 포함 하는 Azure 리소스 그룹 프로젝트를 엽니다.

1. 솔루션 탐색기에서 **< 논리-앱-이름 > json** 파일의 바로 가기 메뉴를 열고 **논리 앱 디자이너를 사용 하 여 열기**를 선택 합니다. (키보드: Ctrl + L)

   ![논리 앱 디자이너를 사용 하 여 논리 앱의 json 파일 열기](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Visual Studio 2019에이 명령이 없으면 Visual Studio 및 Azure Logic Apps Tools 확장에 대 한 최신 업데이트가 있는지 확인 합니다.

1. 속성 창에서 논리 앱에 대 한 **통합 계정** 속성을 표시 하도록 디자이너의 탭 또는 화면을 선택 하 여 논리 앱 디자이너에 포커스가 있는지 확인 합니다.

   ![속성 창-"통합 계정" 속성](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > 속성 창 아직 열려 있지 않으면 **보기** 메뉴에서 **속성 창**을 선택 합니다. (키보드: F4 키 누르기)

1. **통합 계정** 속성 목록을 열고 논리 앱에 연결 하려는 통합 계정을 선택 합니다. 예를 들면 다음과 같습니다.

   !["통합 계정" 속성 목록 열기](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. 완료 되 면 Visual Studio 솔루션을 저장 해야 합니다.

Visual Studio에서 **통합 계정** 속성을 설정 하 고 논리 앱을 Azure Resource Manager 템플릿으로 저장 하는 경우 해당 템플릿에는 선택한 통합 계정에 대 한 매개 변수 선언도 포함 됩니다. 템플릿 매개 변수 및 논리 앱에 대 한 자세한 내용은 [개요: 논리 앱 배포 자동화](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)를 참조 하세요.

<a name="change-location"></a>

## <a name="change-deployment-location"></a>배포 위치 변경

Visual Studio에서 논리 앱이 배포를 자동화 하는 데 사용 하는 [Azure 리소스 그룹 프로젝트](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) 내에 json (json) 파일로 존재 하는 경우 해당 논리 앱은 위치 유형 및 특정 위치로 설정 됩니다. 이 위치는 Azure 지역 또는 기존 [ISE (통합 서비스 환경)](connect-virtual-network-vnet-isolated-environment.md)중 하나입니다.

논리 앱의 위치 유형 또는 위치를 변경 하려면 논리 앱 디자이너를 사용 하 여 솔루션 탐색기에서 논리 앱의 워크플로 정의 (json) 파일을 열어야 합니다. 클라우드 탐색기를 사용 하 여 이러한 속성을 변경할 수 없습니다.

> [!IMPORTANT]
> 위치 유형을 **지역** 에서 [**통합 서비스 환경**](connect-virtual-network-vnet-isolated-environment-overview.md) 로 변경 하면 청구, [제한](logic-apps-limits-and-config.md#integration-account-limits), [통합 계정 지원](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)등에 사용 되는 논리 앱의 [가격 책정 모델](logic-apps-pricing.md#fixed-pricing) 에 영향을 줍니다. 다른 위치 유형을 선택 하기 전에 논리 앱에 미치는 영향을 이해 해야 합니다.

1. Visual Studio에서 논리 앱을 포함 하는 Azure 리소스 그룹 프로젝트를 엽니다.

1. 솔루션 탐색기에서 `<logic-app-name>.json` 파일의 바로 가기 메뉴를 열고 **논리 앱 디자이너를 사용 하 여 열기**를 선택 합니다. (키보드: Ctrl + L)

   ![논리 앱 디자이너를 사용 하 여 논리 앱의 json 파일 열기](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Visual Studio 2019에이 명령이 없으면 Visual Studio 및 Azure Logic Apps Tools 확장에 대 한 최신 업데이트가 있는지 확인 합니다.

1. 속성 창에서 논리 앱에 대 한 **위치 유형** 및 **위치 속성** 선택이 표시 되도록 디자이너의 탭 또는 화면을 선택 하 여 논리 앱 디자이너에 포커스가 있는지 확인 합니다. 프로젝트의 위치 형식은 **Region** 또는 **통합 서비스 환경**로 설정 됩니다.

   ![속성 창-"위치 유형 선택" & "위치" 속성](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > 속성 창 아직 열려 있지 않으면 **보기** 메뉴에서 **속성 창**을 선택 합니다. (키보드: F4 키 누르기)

1. 위치 유형을 변경 하려면 **위치 유형 선택** 속성 목록을 열고 원하는 위치 유형을 선택 합니다.

   예를 들어 위치 형식이 **통합 서비스 환경**인 경우 **영역**을 선택할 수 있습니다.

   !["위치 유형 선택" 속성-위치 유형 변경](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. 특정 위치를 변경 하려면 **위치** 속성 목록을 엽니다. 위치 형식에 따라 원하는 위치를 선택 합니다. 예를 들면 다음과 같습니다.

   * 다른 Azure 지역 선택:

     !["위치" 속성 목록을 열고 다른 Azure 지역을 선택 합니다.](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * 다른 ISE를 선택 합니다.

     !["위치" 속성 목록을 열고 다른 ISE를 선택 합니다.](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. 완료 되 면 Visual Studio 솔루션을 저장 해야 합니다.

Visual Studio에서 위치 유형 또는 위치를 변경 하 고 논리 앱을 Azure Resource Manager 템플릿으로 저장 하는 경우 해당 위치에는 해당 위치 유형 및 위치에 대 한 매개 변수 선언도 포함 됩니다. 템플릿 매개 변수 및 논리 앱에 대 한 자세한 내용은 [개요: 논리 앱 배포 자동화](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)를 참조 하세요.

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Azure에서 새로 고침

Azure Portal에서 논리 앱을 편집하고 변경 내용을 유지하려면 Visual Studio에서 해당 변경 내용으로 앱 버전을 새로 고침해야 합니다.

* Visual Studio의 논리 앱 디자이너 도구 모음에서 **새로 고침**을 선택 합니다.

  또는

* Visual Studio 클라우드 탐색기에서 논리 앱의 바로 가기 메뉴를 열고 **새로 고침**을 선택합니다.

![업데이트로 논리 앱 새로 고침](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>논리 앱 업데이트 게시

Visual Studio에서 Azure로 논리 앱 업데이트를 배포할 준비가 되 면 논리 앱 디자이너 도구 모음에서 **게시**를 선택 합니다.

![Azure Portal에 업데이트 된 논리 앱 게시](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>수동으로 논리 앱 실행

Azure에 배포된 논리 앱을 Visual Studio에서 수동으로 트리거할 수 있습니다. 논리 앱 디자이너 도구 모음에서 **트리거 실행**을 선택 합니다.

![논리 앱에 대 한 수동으로 트리거 실행](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>실행 기록 검토

논리 앱 실행 상태를 확인하고 문제를 진단하려면 Visual Studio에서 해당 실행에 대한 입력 및 출력 같은 세부 정보를 검토하면 됩니다.

1. 클라우드 탐색기에서 논리 앱의 바로 가기 메뉴를 열고 **실행 기록 열기**를 선택합니다.

   ![논리 앱에 대 한 실행 기록 열기](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. 특정 실행에 대한 세부 정보를 보려면 실행을 두 번 클릭합니다. 예:

   ![특정 실행에 대 한 정보 보기](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > 속성을 기준으로 테이블을 정렬 하려면 해당 속성에 대 한 열 머리글을 선택 합니다.

1. 검토 하려는 입력 및 출력의 단계를 확장 합니다. 예를 들면 다음과 같습니다.

   ![각 단계에 대한 입력 및 출력 보기](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>논리 앱 사용 또는 사용 안 함

논리 앱을 삭제하지 않고도 다음 번에 트리거 조건이 충족되더라도 트리거가 발생하지 않게 할 수 있습니다. 논리 앱을 사용하지 않도록 설정하면 Logic Apps 엔진이 논리 앱에 대한 이후 워크플로 인스턴스를 만들고 실행하지 못하게 차단됩니다. 클라우드 탐색기에서 논리 앱의 바로 가기 메뉴를 열고 **사용 안 함**을 선택합니다.

![클라우드 탐색기에서 논리 앱을 사용 하지 않도록 설정](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> 논리 앱을 사용하지 않도록 설정하면 새 실행이 인스턴스화되지 않습니다. 진행 중 및 보류 중인 모든 실행이 완료될 때까지 계속되며, 완료에 시간이 소요될 수 있습니다.

논리 앱을 다시 활성화 하려면 클라우드 탐색기에서 논리 앱의 바로 가기 메뉴를 열고 **사용**을 선택 합니다.

![클라우드 탐색기에서 논리 앱 사용](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>논리 앱 삭제

Azure Portal에서 논리 앱을 삭제하려면 클라우드 탐색기에서 논리 앱의 바로 가기 메뉴를 열고 **삭제**를 선택합니다.

![Azure Portal에서 논리 앱을 삭제 합니다.](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> 논리 앱을 삭제하면 새 실행이 인스턴스화되지 않습니다. 모든 진행 중 및 보류 중인 실행이 취소됩니다. 수천 개의 실행이 있다면 취소를 완료하는 데 상당한 시간이 소요될 수 있습니다. 

## <a name="troubleshooting"></a>문제 해결

Logic Apps 디자이너에서 논리 앱 프로젝트를 열 때 Azure 구독을 선택하는 옵션이 제공되지 않을 수 있습니다. 그 대신, 사용하려는 Azure 구독이 아닌 다른 구독으로 논리 앱이 열립니다. 사용자가 논리 앱의 .json 파일을 연 후 Visual Studio가 처음에 선택된 구독을 나중에 사용하기 위해 캐시하기 때문에 이 동작이 발생합니다. 이 문제를 해결하려면 다음 단계 중 하나를 수행합니다.

* 논리 앱의 .json 파일 이름을 바꿉니다. 구독 캐시는 파일 이름에 따라 달라집니다.

* 솔루션의 *모든* 논리 앱에 대해 이전에 선택한 구독을 제거 하려면 솔루션 디렉터리에서 숨겨진 Visual Studio 설정 폴더 (vs)를 삭제 합니다. 이 위치에 구독 정보가 저장됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Visual Studio를 사용하여 배포된 논리 앱을 관리하는 방법을 배웠습니다. 다음으로 배포에 대한 논리 앱 정의 사용자 지정에 대해 알아보겠습니다.

> [!div class="nextstepaction"]
> [JSON에서 논리 앱 정의를 작성](../logic-apps/logic-apps-author-definitions.md)
