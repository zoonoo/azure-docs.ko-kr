<properties 	
	pageTitle="Azure 데이터 팩터리 - 샘플" 
	description="Azure Data Factory 서비스와 함께 제공 되는 샘플에 대 한 세부 정보를 제공 합니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/07/2016" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리 - 샘플

## GitHub의 샘플
[GitHub Azure-DataFactory 리포지토리](https://github.com/azure/azure-datafactory)에는 신속하게 Azure Data Factory 서비스의 규모를 확장하거나 스크립트를 수정하고 자체 응용 프로그램에서 사용하는 데 도움이 되는 여러 샘플이 포함되어 있습니다. Samples\\JSON 폴더에는 일반적인 시나리오에 대한 JSON 조각이 포함되어 있습니다.

## Azure 포털의 샘플
데이터 팩터리의 홈 페이지에 있는 **샘플 파이프라인**을 사용하여 데이터 팩터리에 샘플 파이프라인 및 관련된 엔터티(데이터 집합 및 연결된 서비스)를 배포합니다.

1. 새 데이터 팩터리를 만들거나 기존 데이터 팩터리를 엽니다. 데이터 팩터리를 만드는 단계는 [Azure Data Factory 시작](data-factory-get-started.md#CreateDataFactory)을 참조하세요.
2. 데이터 팩터리의 **데이터 팩터리** 블레이드에서 **샘플 파이프라인** 타일을 클릭합니다.

	![샘플 파이프라인 타일](./media/data-factory-samples/SamplePipelinesTile.png)

2. **샘플 파이프라인** 블레이드에서 배포할 **샘플**을 클릭합니다.
	
	![샘플 파이프라인 블레이드](./media/data-factory-samples/SampleTile.png)

3. 샘플에 대한 구성 설정을 지정합니다. 예를 들어 Azure 저장소 계정 이름 및 계정 키, Azure SQL Server 이름, 데이터베이스, 사용자 ID, 암호 등입니다.

	![샘플 블레이드](./media/data-factory-samples/SampleBlade.png)

4. 구성 설정 지정을 마쳤으면 **만들기**를 클릭하여 샘플 파이프라인 및 파이프라인에서 사용되는 연결된 서비스/테이블을 만듭니다/배포합니다.
5. 이전에 **샘플 파이프라인** 블레이드에서 클릭한 샘플 타일에 배포 상태가 표시됩니다.

	![배포 상태](./media/data-factory-samples/DeploymentStatus.png)

6. 샘플 타일에 **배포 성공** 메시지가 표시되면 **샘플 파이프라인** 블레이드를 닫습니다.
5. **데이터 팩터리** 블레이드에서 연결된 서비스, 데이터 집합 및 파이프라인이 데이터 팩터리에 추가된 것을 확인할 수 있습니다.  

	![데이터 팩터리 블레이드](./media/data-factory-samples/DataFactoryBladeAfter.png)
   
## Visual Studio의 샘플

### 필수 조건

다음 항목이 컴퓨터에 설치되어 있어야 합니다.

- Visual Studio 2013 또는 Visual Studio 2015
- Visual Studio 2013 또는 Visual Studio 2015용 Azure SDK를 다운로드합니다. [Azure 다운로드 페이지](https://azure.microsoft.com/downloads/)로 이동하고 **.NET** 섹션에서 **VS 2013** 또는 **VS 2015**를 클릭합니다.
- [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) 또는 [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)용 최신 Azure Data Factory 플러그 인을 다운로드합니다. Visual Studio 2013을 사용하는 경우 메뉴에서 **도구** -> **확장 및 업데이트** -> **온라인** -> **Visual Studio 갤러리** -> **Visual Studio용 Microsoft Azure Data Factory 도구** -> **업데이트**를 클릭하여 플러그 인을 업데이트할 수도 있습니다.

### 데이터 팩터리 템플릿 사용

1. 메뉴에서 **파일**을 클릭하고 **새로 만들기**를 가리킨 다음 **프로젝트**를 클릭합니다. 
2. **새 프로젝트** 대화 상자에서 다음을 수행합니다. 
	1. **템플릿** 아래의 **DataFactory**를 선택합니다. 
	2. 오른쪽 창의 **데이터 팩터리 템플릿**을 선택합니다. 
	3. 프로젝트의 **이름**을 입력합니다. 
	4. 프로젝트의 **위치**를 선택합니다. 
	5. **확인**을 클릭합니다. 

	![새 프로젝트 대화 상자](./media/data-factory-samples/vs-new-project-adf-templates.png)
6. **데이터 팩터리 템플릿** 대화 상자에서 **사용 사례 템플릿** 섹션에 있는 샘플 템플릿을 선택하고 **다음**을 클릭합니다. 다음 단계는 **고객 프로파일링** 템플릿 사용 방법에 대해 안내합니다. 단계는 다른 샘플에 대해서도 유사합니다. 

	![데이터 팩터리 템플릿 대화 상자](./media/data-factory-samples/vs-data-factory-templates-dialog.png) 
7. **데이터 팩터리 구성** 대화 상자에서 **데이터 팩터리 기본 사항** 페이지에 있는 **다음**을 클릭합니다.
8. **데이터 팩터리 구성** 페이지에서 다음을 수행합니다. 
	1. 이 연습용으로 **새 데이터 팩터리 만들기**를 선택합니다. **기존 데이터 팩터리 사용**을 선택할 수도 있습니다.
	2. 데이터 팩터리의 **이름**을 입력합니다.
	3. 데이터 팩터리를 만들려는 위치에 **Azure 구독**을 선택합니다. 
	4. 데이터 팩터리의 **리소스 그룹**을 선택합니다.
	5. **지역**에서 **미국 서부**, **미국 동부** 또는 **북유럽**을 선택합니다.
	6. **다음**을 클릭합니다. 
9. **데이터 저장소 구성** 페이지에서 기존 **Azure SQL 데이터베이스** 및 **Azure 저장소 계정**을 지정 (또는) 새로 만들고 다음을 클릭합니다. 
10. **계산 구성** 페이지에서 기본값을 선택하고 **다음**을 클릭합니다. 
11. **요약** 페이지에서 모든 설정을 검토하고 **다음**을 클릭합니다. 
12. **배포 상태** 페이지에서 배포가 완료될 때까지 대기하고 **마침**을 클릭합니다.
13. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다. 
19. **Microsoft 계정에 로그인** 대화 상자가 표시되면 Azure 구독이 있는 계정의 자격 증명을 입력하고 **로그인**을 클릭합니다.
20. 다음 대화 상자가 표시됩니다.

	![게시 대화 상자](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. **데이터 팩터리 구성** 페이지에서 다음을 수행합니다.
	1. **기존 데이터 팩터리 사용** 옵션을 확인합니다.
	2. 위의 템플릿을 사용할 때 선택한 **데이터 팩터리**를 선택합니다. 
	6. **다음**을 클릭하여 **항목 게시** 페이지로 전환합니다. **다음** 단추를 사용할 수 없는 경우 **Tab** 키를 눌러 이름 필드에서 나갑니다. 
23. **항목 게시** 페이지에서 모든 데이터 팩터리 엔터티가 선택되었는지 확인하고 **다음**을 클릭하여 **요약** 페이지로 전환합니다.     
24. 요약을 검토한 후 **다음**을 클릭하여 배포 프로세스를 시작하고 **배포 상태**를 봅니다.
25. **배포 상태** 페이지에 배포 프로세스의 상태가 표시됩니다. 배포가 완료되면 마침을 클릭합니다. 

Visual Studio를 사용하여 Data Factory 엔터티를 작성하고 Azure에 게시하는 방법에 대한 자세한 내용은 [첫 번째 데이터 팩터리(Visual Studio) 빌드](data-factory-build-your-first-pipeline-using-vs.md)를 참조하세요.

<!---HONumber=AcomDC_0518_2016-->