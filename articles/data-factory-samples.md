<properties 	
	pageTitle="Azure 데이터 팩터리-샘플" 
	description="Azure 데이터 팩터리 서비스와 함께 제공 되는 샘플에 대 한 세부 정보를 제공 합니다." 
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
	ms.date="04/25/2015" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리-샘플

## Azure 포털에서 샘플
신속 하 게 배포, 검토 하 고 테스트할 수 사용 하 여 Azure 데이터 팩터리 예제는 **파이프라인 샘플** Azure 포털에서 블레이드 합니다.

1. 기존 데이터 팩터리를 열거나 새 데이터 팩터리를 만듭니다. 참조 [Azure 데이터 팩터리 시작][data-factory-get-started] 데이터 팩터리를 만드는 단계에 대 한 합니다.
2. 에 **데이터 팩터리** 데이터 팩터리에 대 한 블레이드를 클릭는 **파이프라인 샘플** 바둑판식으로 배열 합니다.

	![샘플 파이프라인 타일](./media/data-factory-samples/SamplePipelinesTile.png)

2. 에 **파이프라인 샘플** 블레이드를 클릭 하 여는 **샘플** 배포 하려는 합니다.
	
	![샘플 파이프라인 블레이드](./media/data-factory-samples/SampleTile.png)

3. 이 샘플에 대 한 구성 설정을 지정 합니다. 예, 프로그램 Azure 저장소 계정 이름 및 계정 키, SQL Azure 서버 이름, 데이터베이스, 사용자 ID 및 암호, 등...

	![샘플 블레이드](./media/data-factory-samples/SampleBlade.png)

4. 클릭 하 여 구성 설정을 지정 수행한 후 **만들기** 만들기/배포 하는 샘플 파이프라인 및 파이프라인에서 사용 하는 연결 된 서비스/테이블입니다.
5. 배포의 상태 앞에 클릭 하면 샘플 타일에 표시 됩니다는 **파이프라인 샘플** 블레이드입니다.

	![배포 상태](./media/data-factory-samples/DeploymentStatus.png)

6. 표시 되 면는 **배포 성공** close 샘플에 대 한 타일에는 메시지는 **파이프라인 샘플** 블레이드입니다.
5.  **데이터 팩터리** 블레이드를 기다리면 연결 된 서비스, 데이터 집합 및 파이프라인 데이터 팩터리를에 추가 됩니다.  

	![데이터 팩터리 블레이드](./media/data-factory-samples/DataFactoryBladeAfter.png)
   

다음 표에서에서 사용할 수 있는 샘플의 간략 한 설명을 제공는 **파이프라인 샘플** 바둑판식으로 배열 합니다.

샘플 이름 | description
----------- | -----------
프로 파일링 하는 게임 고객 | Contoso는 여러 플랫폼에 대 한 게임을 만드는 게임 회사: 게임 콘솔, 핸드헬드의 장치 및 개인용 컴퓨터 (Pc). 이러한 각 게임은 많은 로그를 생성합니다. Contoso의 목표는 수집 및 이러한 게임을 사용량 정보를 가져올, 상향 판매 및 교차 판매 기회를 식별, 새로운 멋진 기능 등을 개발 하 여 생성 된 로그를 분석 하는 중... 높이고 비즈니스 고객에 게 더 나은 환경을 제공 합니다. 이 샘플 샘플 로그, 프로세스를 수집 하 고 참조 데이터를 강화 하 고 Contoso가 최근에 출시 하는 마케팅 캠페인의 효율성을 평가 하는 데이터 변환.
 
## GitHub 페이지
 [GitHub Azure-사용할 저장소](https://github.com/azure/azure-datafactory) Azure 데이터 팩터리 서비스와 수립할 수 있도록 여러 샘플을 신속 하 게 포함 (또는) 스크립트를 수정 하 고 자체 응용 프로그램에서 사용 합니다. 일반적인 시나리오에 대 한 JSON 조각을 Samples\JSON 폴더에 있습니다.

[data-factory-get-started]: data-factory-get-started.md#CreateDataFactory

<!---HONumber=GIT-SubDir-->