<properties
   pageTitle="Azure 리소스 그룹 배포 프로젝트 만들기 및 배포 | Microsoft Azure"
   description="Azure 리소스 그룹 배포 프로젝트 만들기 및 배포"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tlee" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/02/2015"
   ms.author="kempb" />

# Azure 리소스 그룹 배포 프로젝트 만들기 및 배포

Azure SDK 2.6이 설치될 때 **Azure 리소스 그룹** 배포 프로젝트 템플릿은 Visual Studio에서 제공됩니다. Azure 리소스 그룹 프로젝트를 사용하여 관련된 여러 Azure 리소스를 단일 배포 작업으로 함께 그룹화하고 게시합니다. Azure 리소스 그룹 프로젝트는 **Azure 리소스 관리자**라는 기술을 사용하여 자신의 작업을 수행합니다. **Azure 리소스 관리자**는 Azure 리소스 그룹을 정의할 수 있는 REST API 서비스로, 일반적으로 함께 사용되며 수명 주기가 유사한 여러 Azure 리소스를 포함합니다. 리소스 그룹을 사용하여 각 개별 리소스에 대한 다른 함수를 호출하지 않고 단일 함수 호출로 그룹의 모든 리소스에 대해 작업할 수 있습니다. Azure 리소스 그룹에 대한 자세한 내용은 [Azure Preview 포털을 사용하여 Azure 리소스 관리](resource-group-portal.md)를 참조하세요. 자세한 종단 간 Azure 리소스 그룹 배포 시나리오는 [Visual Studio용 Azure 리소스 그룹](https://azure.microsoft.com/blog/azure-resource-manager-2-5-for-visual-studio/)을 참조하세요.

Azure 리소스 그룹 프로젝트는 리소스 그룹에 배포되는 요소를 정의하는 Azure 리소스 관리자 JSON 템플릿을 포함합니다. 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.

Azure 리소스 관리자에는 Ubuntu Server 및 Windows Server 2012 R2와 같은 리소스를 배포하는 데 사용될 수 있는 여러 다른 리소스는 공급자가 있습니다. 이 항목에서는 빈 기본 웹 사이트를 Azure에 배포하는 **웹앱**을 사용합니다.

## Azure 리소스 그룹 프로젝트 만들기

이 절차에서 **웹앱** 템플릿으로 Azure 리소스 그룹 프로젝트를 만드는 방법에 대해 배웁니다.

### Azure 리소스 그룹 프로젝트를 만들려면

1. Visual Studio에서 **파일**, **새 프로젝트**를 선택하고 **C#** 또는 **Visual Basic**을 선택합니다. 그런 다음 **클라우드**, **Azure 리소스 그룹** 프로젝트를 차례로 선택합니다.

    ![Cloud Deployment 프로젝트](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796668.png)

1. Azure 리소스 관리자에 배포하려는 템플릿을 선택합니다. 이 예제의 경우 **웹앱** 템플릿을 선택합니다.

    ![템플릿 선택](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796669.png)

    나중에 리소스 그룹에 더 많은 리소스를 추가할 수도 있습니다.

    >[AZURE.NOTE]사용 가능한 템플릿 목록은 온라인으로 검색되고 변경될 수 있습니다.

    Visual Studio는 웹앱에 대한 Azure 리소스 그룹 배포 프로젝트를 만듭니다.

1. 배포 프로젝트에서 노드를 확장하여 만들어진 내용을 볼 수 있습니다.

    이 예제에 대한 웹앱 템플릿을 선택한 후 다음 파일을 참조합니다.

    |파일 이름|설명|
    |---|---|
    |Deploy-AzureResourceGroup.ps1|Azure 리소스 관리자에 배포하는 PowerShell 명령을 호출하는 PowerShell 스크립트입니다.

    **참고** 이 PowerShell 스크립트는 Visual Studio에서 사용되어 템플릿을 배포합니다. 이 스크립트의 변경 내용은 Visual Studio의 배포에도 영향을 주므로 주의합니다. | !WebSite.json|Azure 리소스 관리자에 배포하려는 모든 세부 정보를 지정하는 구성 파일입니다. | | WebSite.param.dev.json| 구성 파일에 필요한 특정 값을 포함하는 매개 변수 파일입니다. | |AzCopy.exe|PowerShell 스크립트에서 사용 되는 도구로, 로컬 저장소 드롭 경로에서 저장소 계정 컨테이너로 파일을 복사합니다. 이 도구는 템플릿과 함께 코드를 배포하는 배포 프로젝트를 구성하는 경우에만 사용됩니다.|

    모든 Azure 리소스 그룹 배포 프로젝트는 이러한 4개의 기본 파일을 포함합니다. 다른 프로젝트는 다른 기능을 지원하는 추가 파일을 포함할 수 있습니다.

## Azure 리소스 그룹 프로젝트 사용자 지정

배포하려는 Azure 리소스를 설명하는 JSON 템플릿 파일을 수정하여 배포 프로젝트를 사용자 지정할 수 있습니다. JSON은 JavaScript Object Notation의 약어로, 쉽게 작업할 수 있는 직렬화된 데이터 형식입니다.

Azure 리소스 그룹 프로젝트는 수정할 수 있는 솔루션 탐색기의 **템플릿** 노드에 Azure 리소스 관리자 템플릿 파일 및 매개 변수 파일의 두 개의 템플릿 파일을 포함합니다.

- **Azure 리소스 관리자 템플릿 파일**(확장명이 .json인)은 사이트 이름 및 위치와 같은 배포 프로젝트에서 필요한 매개 변수 및 원하는 리소스를 포함하는 파일을 지정합니다. 또한 트리거에 대한 이름, 태그 및 규칙과 같이 Azure 리소스 그룹 및 해당 속성에서 구성 요소의 종속성도 지정합니다. 사용자 고유의 기능을 추가하도록 이 파일을 수정할 수 있습니다. 예를 들어, 템플릿에 데이터베이스를 추가할 수 있습니다. 제공해야 하는 매개 변수를 알아보려면 각 리소스 공급자에 대한 설명서를 참조하세요. 자세한 내용은 [리소스 공급자](https://msdn.microsoft.com/library/azure/dn790572.aspx)를 참조하세요.

- **매개 변수 파일**(확장명이 `.param.*.json`인)은 각 리소스 공급자에 필요한 구성 파일에 지정된 매개 변수의 값을 포함합니다. 이 예제에서는 웹앱(WebSite.json)에 대한 구성 파일이 *siteName* 및 *siteLocation*에 대한 매개 변수를 정의합니다. 배포하는 동안 템플릿 파일의 매개 변수에 대한 값을 제공하라는 메시지가 표시되며 이 값은 매개 변수 파일에 저장됩니다. 또한 매개 변수 파일을 직접 편집할 수 있습니다.

JSON 파일은 Visual Studio 편집기에서 편집할 수 있습니다. [Visual Studio용 PowerShell 도구](https://visualstudiogallery.msdn.microsoft.com/c9eb3ba8-0c59-4944-9a62-6eee37294597)를 설치하는 경우, 구문 강조 표시, 중괄호 일치 및 IntelliSense을 사용하여 PowerShell 스크립트를 쉽게 읽거나 편집할 수 있습니다. 설치되어있지 않은 경우 PowerShell 도구를 설치하는 링크는 편집기의 위쪽에 나타납니다.

![PowerShell 도구 설치](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796671.png)

JSON 파일은 각 파일의 위쪽에서 참조하는 스키마를 사용합니다. 스키마를 더 잘 이해하도록 다운로드하여 분석할 수 있습니다. 스키마는 허용된 요소, 필드의 형식 및 포맷, 열거형 값의 가능한 값 등을 정의합니다.

서로 다른 구성으로 배포하거나 자주 설정을 변경하려는 경우*param* 파일의 다른 복사본을 만들 수 있습니다. 모든 환경에 동일한 템플릿 파일을 사용합니다.

## Azure 리소스 그룹 프로젝트를 Azure 리소스 그룹에 배포

Azure 리소스 그룹 프로젝트를 배포할 때 Azure 리소스 그룹에 배포하며, 웹앱, 데이터베이스 등과 같은 Azure에서의 논리적 리소스 그룹입니다.

1. 배포 프로젝트 노드의 바로 가기 메뉴에서 **배포**, **새 배포**를 선택합니다.

    ![배포, 새 배포 메뉴 항목](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796672.png)

    **리소스 그룹에 배포** 대화 상자가 나타납니다.

    ![리소스 그룹에 배포 대화 상자](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796673.png)

1. **리소스 그룹** 드롭다운 상자에서 기존 리소스 그룹을 선택하거나 새로 만듭니다. 리소스 그룹을 만들려면 **리소스 그룹** 드롭다운 상자를 선택하고 **<Create New...>**를 선택합니다.

    **리소스 그룹 만들기** 대화 상자가 나타납니다.

    ![리소스 그룹 만들기 대화 상자](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796674.png)

    >[AZURE.NOTE]일반적으로 새 배포 프로젝트를 시작하는 경우 배포할 새 리소스 그룹을 만들 수 있습니다.

1. 리소스 그룹의 이름과 위치를 입력한 다음 **만들기** 단추를 선택합니다.

    리소스 그룹의 위치는 그룹의 리소스가 지역에 걸쳐 있을 수 있으므로 리소스의 위치와 동일할 필요가 없습니다.

1. 이 배포에 사용하려는 템플릿 구성 및 매개 변수 파일을 선택하거나 기본값을 적용합니다.

    **매개 변수 편집** 단추를 선택하여 리소스에 대한 속성을 편집할 수 있습니다. 배포 시 필수 매개 변수가 누락된 경우 입력할 수 있도록 **매개 변수 편집** 대화 상자가 나타납니다. 값이 누락된 매개 변수에는 옆의 **값** 상자에 표시되는 **<null>**이 있습니다. 이 예제의 경우(웹앱 리소스), 필수 매개 변수는 사이트 이름, 호스팅 계획 및 사이트 위치를 포함합니다. (재호출하는 경우 이 매개 변수 값은 매개 변수 파일에서 기본적으로 null로 설정됩니다.) 다른 매개 변수는 기본값이 있습니다.

    ![매개 변수 편집 대화 상자](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796675.png)

1. **매개 변수 편집** 대화 상자에 사이트 이름, 사이트 위치, 호스팅 계획 이름을 입력하고 다른 속성의 값을 확인합니다. 완료되면 **저장** 단추를 선택합니다.

    - *siteName* 매개 변수는 웹 페이지 URL의 첫 번째 부분입니다. 예를 들어, URL mywebsitename.azurewebsites.net의 경우 사이트 이름은 **mywebsitename**입니다.

    - *hostingPlanName* 매개 변수는 호스팅 계획을 지정합니다. 이 예제에서는 '자유'를 사용할 수 있습니다. 호스팅 계획에 대한 자세한 내용은 [Azure 앱 서비스 계획의 포괄 개요](http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/)를 참조하세요.

    - *siteLocation* 매개 변수는 "미국 서부"와 같은 호스팅될 사이트인 Azure 지역을 참조합니다. 사용 가능한 영역 목록은 [Azure 지역](http://azure.microsoft.com/regions/)을 참조하세요.

1. **배포** 단추를 선택하여 Azure에 프로젝트를 배포합니다.

    **출력** 창에서 배포의 진행률을 볼 수 있습니다. 구성에 따라 배포를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

    >[AZURE.NOTE]Microsoft Azure PowerShell cmdlet을 설치하라는 메시지가 표시될 수 있습니다. 이 cmdlet은 Azure 리소스 그룹을 배포하는 데 필요하기 때문에 이를 설치해야 합니다.

1. 브라우저에서 [Azure Preview 포털](https://portal.azure.com/)을 엽니다. 새 변경 사항이므로 **알림** 탭에서 사용 가능한 새 알림 메시지여야 합니다. 새 Azure 리소스 그룹에 대한 세부 정보를 보려면 선택합니다. 사용 가능한 모든 리소스 그룹 목록을 보려면, **찾아보기** 탭을 선택한 후 **리소스 그룹**를 선택합니다.

    ![프로비전된 Azure 리소스 그룹](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796676.png)

1. 프로젝트를 변경하고 다시 배포할 경우, Azure 리소스 그룹 프로젝트의 바로 가기 메뉴에서 직접 기존 리소스 그룹을 선택할 수 있습니다. 바로 가기 메뉴에서 **배포**를 선택한 다음 방금 배포한 리소스 그룹을 선택합니다.

    ![배포된 Azure 리소스 그룹](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796677.png)

    Azure 리소스 그룹을 배포하면 해당 프로젝트만 배포합니다. 솔루션에 코드 프로젝트 또는 다른 모든 프로젝트가 있는 경우 별도로 배포해야 합니다.

## Azure SDK 2.6과 함께 Azure SDK 2.5 Cloud Deployment 프로젝트 사용

Azure SDK 2.5로 만든 Cloud Deployment 프로젝트를 사용하는 경우, Azure 리소스 템플릿 편집 및 배포를 위해 새로운 기능을 사용할 수 있도록 Azure SDK 2.6 이상으로 업그레이드합니다. Azure SDK 2.5를 사용하여 만든 템플릿을 다시 사용하는 가장 쉬운 방법은 프로젝트의 Azure SDK 2.6 이상 버전을 만들고 해당 프로젝트에 템플릿을 이동하고 일부 수정 작업을 수행하는 것입니다.

### Azure SDK 2.6 이상과 함께 Azure SDK 2.5 Cloud Deployment 프로젝트를 사용하려면

1. 새 Azure SDK 2.6 이상 Azure 리소스 그룹 프로젝트를 솔루션에 추가합니다.

    1. Azure SDK 2.5 Cloud Deployment 프로젝트를 포함하는 솔루션을 엽니다.

    1. **파일** 메뉴에서 **새로 만들기**, **프로젝트**를 선택합니다.

    1. **새 프로젝트** 대화 상자에서 **Azure 리소스 그룹 **프로젝트(**Visual C#**/**클라우드** 또는 **Visual Basic**/**클라우드**)를 찾습니다.

         프로젝트 템플릿 이름이 **클라우드 배포**에서 Azure 리소스 그룹으로 변경되었습니다.

    1. 프로젝트에 이름을 지정합니다.

    1. 솔루션 드롭다운 상자를 **솔루션에 추가**로 변경합니다.

    1. 다음으로 서식 파일을 선택하라는 메시지가 표시됩니다. Azure SDK 2.5 Cloud Deployment 프로젝트에서 기존 템플릿을 이동하므로, 모든 템플릿을 선택할 수 있으므로 목록의 맨 아래에서 빈 템플릿을 선택할 수 있습니다.

    1. **확인** 단추를 선택합니다.

        새 프로젝트가 솔루션에 추가됩니다.

1. 템플릿 및 매개 변수 파일을 Azure SDK 2.5 Cloud Deployment 프로젝트에서 Azure SDK 2.6 이상 리소스 그룹 프로젝트로 복사합니다.

    1. 솔루션 탐색기에서 Azure SDK 2.5 배포 프로젝트에서 복사할 템플릿 및 매개변수 파일을 찾고 선택하여 복사합니다.

    2. 새 Azure SDK 2.6 이상 리소스 그룹 프로젝트의 **템플릿** 폴더로 파일을 붙여넣습니다.

1. 솔루션 탐색기에서 Azure SDK 2.5 배포 프로젝트에서 복사할 템플릿 및 매개변수 파일을 찾고 선택하여 복사합니다.

1. 새 Azure SDK 2.6 이상 리소스 그룹 프로젝트의 템플릿 폴더로 파일을 붙여넣습니다.

1. 사용자 템플릿을 사용하여 웹 응용 프로그램도 배포하는 경우, 새 Azure SDK 2.6 이상 리소스 그룹 프로젝트에서 웹 응용 프로그램으로 참조를 만들어야 합니다.

    1. 솔루션 탐색기에서 새 Azure SDK 2.6 이상 리소스 그룹 프로젝트의 **참조** 노드의 상황에 맞는 메뉴에서 **참조 추가**를 선택합니다.

    1. 프로젝트 목록의 웹 응용 프로그램 옆의 상자를 선택한 다음 **확인** 단추를 선택합니다.

1. 솔루션 탐색기에서 새 Azure SDK 2.6 이상 리소스 그룹 프로젝트의 참조 노드의 상황에 맞는 메뉴에서 참조 추가를 선택합니다.

1. 프로젝트 목록의 웹 응용 프로그램 옆의 상자를 선택한 다음 확인 단추를 선택합니다.

1. 매개 변수 *dropLocation* 및 *dropLocationSasToken*의 모든 일치 항목의 이름을 *\_artifactsLocation* 및 *\_artifactsLocationSasToken*으로 변경합니다.

1. 사용하지 않으려는 경우, 만들 때 Azure SDK 2.6 이상 프로젝트에 자동으로 추가된 빈 템플릿 및 매개 변수 파일을 삭제할 수 있습니다.

    1. DeployTemplate.json 파일을 삭제합니다.

    1. DeploymentTemplate.param.dev.json 파일을 삭제합니다.

1. Azure SDK 2.5 프로젝트의 Publish-AzureResourceGroup.ps1 스크립트를 변경한 경우, Azure SDK 2.6 이상 프로젝트에서 Deploy-AzureResourceGroup.ps1 스크립트로 이 변경 내용을 이동해야 합니다.

    이제 Azure SDK 2.6 이상 Azure 리소스 그룹 프로젝트의 배포 명령을 사용하여 템플릿을 배포하고 Azure SDK 2.6에서 템플릿을 편집하기 위한 새 기능을 이용할 수 있습니다. 만족할 정도로 2.6 이상 프로젝트를 작업한 경우 솔루션에서 Azure SDK 2.5 프로젝트를 제거할 수 있습니다.

## 프로젝트에 업데이트가 필요한 이유

템플릿에 적용된 몇 가지 변경 내용이 Azure SDK 2.5 배포 스크립트 및 템플릿을 호환 가능하지 않게 하는 Azure SDK 2.6에 배포됩니다. 첫 번째 가장 큰 변경 내용이 배포 시작됩니다. Azure SDK 2.5에는 Azure REST API를 사용하여 템플릿을 업로드하고 배포를 시작하는 컴파일된 코드가 있습니다. Visual Studio가 프로젝트에 포함된 PowerShell 스크립트를 시작했으면 하는 개발자의 의견이 있습니다. 따라서 Azure SDK 2.6에서 배포 명령은 프로젝트에 포함된 PowerShell 스크립트를 시작하여 템플릿을 배포합니다. Azure PowerShell을 사용하여 명령줄에서 배포하거나 배포 명령을 사용한 Visual Studio를 통해 배포하거나 간에 배포를 사용자 지정하고 이 사용자 지정을 항상 실행할 수 있습니다. Visual Studio에서 배포하려면 Azure SDK 2.6 이상이 설치되어 있는 경우 Azure SDK 2.6(이상) 배포 PowerShell 스크립트를 사용해야 합니다.

TFS 자동화된 빌드 및 Microsoft 내 다른 프로젝트에서 명명 규칙에 더 잘 맞도록 일부 변수 이름과 빌드 작업도 조정했습니다. PowerShell 스크립트를 시작하는 데 필요한 변수 및 값을 수집하는 Visual Studio의 코드는 이 새 이름을 검색합니다.

## 다음 단계

Visual Studio에서 Azure 리소스 그룹에 리소스를 추가하는 방법을 알아보려면 [Azure 리소스 그룹에 리소스 추가](vs-azure-tools-resource-group-adding-resources.md)를 참조하세요.

<!---HONumber=AcomDC_1203_2015-->