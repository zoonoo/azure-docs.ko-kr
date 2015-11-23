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
	ms.date="10/13/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - 샘플

## Azure 포털의 샘플
Azure 포털의 **샘플 파이프라인** 블레이드를 사용하여 Azure Data Factory 샘플을 신속하게 배포, 검토 및 테스트할 수 있습니다.

1. 새 데이터 팩터리를 만들거나 기존 데이터 팩터리를 엽니다. 데이터 팩터리를 만드는 단계는 [Azure Data Factory 시작][data-factory-get-started]을 참조하세요.
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
   

다음 표에서는 **샘플 파이프라인** 타일에서 사용할 수 있는 샘플에 대한 간략한 설명을 제공합니다.

샘플 이름 | description
----------- | -----------
게임 고객 프로파일링 | Contoso는 게임 콘솔, 핸드헬드 장치, PC(개인용 컴퓨터) 등 다양한 플랫폼용 게임을 만드는 게임 회사입니다. 이러한 각 게임은 많은 로그를 생성합니다. Contoso는 이러한 게임에서 생성되는 로그를 수집 및 분석하여 사용량 정보를 얻고, 업셀 및 교차 판매 기회를 포착하고, 탁월한 새 기능을 개발하는 등을 통해 비즈니스를 개선하고 고객에게 더 나은 환경을 제공하려는 목표가 있습니다. 이 샘플은 샘플 로그를 수집하고, 참조 데이터를 사용하여 이 로그를 처리 및 보강하고, 데이터를 변환하여 Contoso가 최근 시작한 마케팅 캠페인의 효과를 평가합니다.
 
## GitHub의 샘플
[GitHub Azure-DataFactory 리포지토리](https://github.com/azure/azure-datafactory)에는 신속하게 Azure Data Factory 서비스의 규모를 확장하거나 스크립트를 수정하고 자체 응용 프로그램에서 사용하는 데 도움이 되는 여러 샘플이 포함되어 있습니다. Samples\\JSON 폴더에는 일반적인 시나리오에 대한 JSON 조각이 포함되어 있습니다.


[data-factory-get-started]: data-factory-get-started.md#CreateDataFactory

<!---HONumber=Nov15_HO3-->