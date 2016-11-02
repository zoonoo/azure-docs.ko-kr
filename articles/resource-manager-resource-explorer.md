<properties
   pageTitle="Azure 리소스 탐색기 | Microsoft Azure"
   description="Azure 리소스 탐색기에 대해 설명하고 이를 사용하여 Azure Resource Manager를 통해 배포를 보고 업데이트할 수 있는 방법을 알아봅니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="stuartleeks"
   manager="ankodu"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="stuartle;tomfitz"/>

# Azure 리소스 탐색기를 사용하여 리소스 보기 및 수정
[Azure 리소스 탐색기](https://resources.azure.com)는 구독에서 이미 만든 리소스를 확인하는 데 유용한 도구입니다. 이 도구를 사용하면 리소스 구조를 이해하고 각 리소스에 할당된 속성을 확인할 수 있습니다. 리소스 유형에 사용할 수 있는 REST API 작업 및 PowerShell cmdlet에 대해 알아보고 인터페이스를 통해 명령을 실행할 수 있습니다. 리소스 탐색기는 기존 리소스에 대한 속성을 볼 수 있으므로 Resource Manager 템플릿을 만들 때 특히 유용할 수 있습니다.

리소스 탐색기 도구의 원본은 [github](https://github.com/projectkudu/ARMExplorer)에서 사용할 수 있습니다. 여기에서는 사용자 고유의 응용 프로그램에서 유사한 동작을 구현해야 하는 경우 유용한 참조를 제공합니다.

## 리소스 보기
[https://resources.azure.com](https://resources.azure.com)으로 이동하여 [Azure 포털](https://portal.azure.com)에서 사용하는 것과 동일한 자격 증명으로 로그인합니다.

로드되면 왼쪽의 트리 보기를 사용하여 구독 및 리소스 그룹으로 드릴다운할 수 있습니다.

![트리 보기](./media/resource-manager-resource-explorer/are-01-treeview.png)

리소스 그룹으로 드릴다운한 경우 해당 그룹에 있는 리소스의 공급자가 표시됩니다.

![providers](./media/resource-manager-resource-explorer/are-02-treeview-providers.png)

여기에서 리소스 인스턴스로 드릴다운할 수 있습니다. 아래의 스크린샷에는 트리 보기의 `sltest` SQL Server 인스턴스가 나와 있습니다. 오른쪽에서는 해당 리소스에 사용할 수 있는 REST API 요청에 대한 정보를 볼 수 있습니다. 리소스의 노드로 이동하면 리소스 탐색기에서 해당 리소스에 대한 정보를 검색하는 GET 요청을 자동으로 실행합니다. URL 아래의 큰 텍스트 영역에 API의 응답이 표시됩니다.

Resource Manager 템플릿에 익숙해지면 본문 내용에도 익숙해지기 시작합니다. 응답의 **properties** 섹션은 템플릿의 **properties** 섹션에서 제공할 수 있는 값과 일치합니다.

![SQL Server](./media/resource-manager-resource-explorer/are-03-sqlserver-with-response.png)

리소스 탐색기에서 계속 드릴다운하여 자식 리소스를 탐색할 수 있습니다. SQL 데이터베이스 서버의 경우 데이터베이스 및 방화벽 규칙과 같은 항목에 대한 자식 리소스가 있습니다.

데이터베이스를 탐색하면 해당 데이터베이스에 대한 속성이 표시됩니다. 아래 스크린샷에서는 데이터베이스 `edition`이 `Standard`이고 `serviceLevelObjective`(또는 데이터베이스 계층)가 `S1`인 것을 볼 수 있습니다.

![SQL 데이터베이스](./media/resource-manager-resource-explorer/are-04-database-get.png)

## 리소스 변경

리소스를 이동한 경우 편집 단추를 선택하여 JSON 콘텐츠를 편집할 수 있도록 설정할 수 있습니다. 그런 다음 리소스 탐색기를 사용하여 JSON을 편집하고 PUT 요청을 보내 리소스를 변경할 수 있습니다. 예를 들어 아래 그림에는 `S0`으로 변경된 데이터베이스 계층이 표시되어 있습니다.

![데이터베이스 - PUT 요청](./media/resource-manager-resource-explorer/are-05-database-put.png)

**PUT**을 선택하여 요청을 제출합니다.

요청이 제출되면 리소스 탐색기에서 GET 요청을 실행하여 상태를 새로 고칩니다. 이 예제에서는 `requestedServiceObjectiveId`가 업데이트되어 `currentServiceObjectiveId`와 다른 것을 볼 수 있습니다. 이는 크기 조정 작업이 진행 중임을 나타냅니다. 가져오기 단추를 클릭하여 상태를 수동으로 새로 고칠 수 있습니다.

![데이터베이스 - GET 요청 2](./media/resource-manager-resource-explorer/are-06-database-get2.png)

## 리소스에 대한 작업 수행

**작업** 탭을 사용하여 추가 REST 작업을 보고 수행할 수 있습니다. 예를 들어 웹 사이트 리소스를 선택한 경우 작업 탭에 사용 가능한 작업의 긴 목록이 제공됩니다. 그 일부가 아래에 표시되어 있습니다.

![웹 - POST 요청](./media/resource-manager-resource-explorer/are-web-post.png)

## PowerShell 통해 API 호출
리소스 탐색기의 PowerShell 탭은 현재 탐색 중인 리소스를 조작하는 데 사용할 cmdlet을 표시합니다. 선택한 리소스 종류에 따라 표시되는 PowerShell 스크립트가 간단한 cmdlet(예: `Get-AzureRmResource` 및 `Set-AzureRmResource`)에서 보다 복잡한 cmdlet(예: 웹 사이트의 슬롯 교환)까지 다양할 수 있습니다.

![PowerShell](./media/resource-manager-resource-explorer/are-07-powershell.png)

Azure PowerShell cmdlet에 대한 자세한 내용은 [Azure Resource Manager에서 Azure PowerShell 사용](powershell-azure-resource-manager.md)을 참조하세요.

## 요약
리소스 탐색기는 Resource Manager로 작업할 때 매우 유용한 도구가 될 수 있습니다. PowerShell을 사용하여 쿼리하고 변경할 방법을 찾는 것이 좋습니다. REST API로 작업하는 경우 코드 작성을 시작하기 전에 API 호출을 시작하고 신속하게 테스트하는 것이 좋습니다. 또한 템플릿을 작성할 경우 리소스 계층을 이해하고 구성을 배치할 위치를 찾는 데 유용할 수 있습니다. 포털에서 변경한 다음 리소스 탐색기에서 해당 항목을 찾을 수 있습니다.

자세한 내용은 [Scott Hanselman 및 David Ebbo의 Channel 9 비디오](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)를 참조하세요.

<!---HONumber=AcomDC_0803_2016-->