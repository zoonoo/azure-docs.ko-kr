<properties 
	pageTitle="기계 학습 고급 분석 환경 계획 | Microsoft Azure" 
	description="주요 질문을 고려하여 고급 분석 환경을 계획합니다." 
	services="machine-learning" 
	documentationCenter="" 
	authors="msolhab"
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="mohabib;bradsev" />


# Azure 기계 학습 고급 분석 환경 계획

Azure 기계 학습을 통해 고급 분석을 수행하는 환경을 설정할 준비가 되었다면 사용자의 분석 문제와 어떤 시나리오가 일치할까요? 필요한 리소스는 데이터의 유형, 크기 및 원본 위치와 이 데이터의 대상 위치에 따라 결정하면 됩니다. 이 문서에서는 시나리오를 식별하는 데 도움이 될 이러한 질문에 대해 설명합니다.

관련 시나리오를 확인했으면 [학습 경로: Azure에서 고급 분석 솔루션 구축](machine-learning-data-science-how-to-create-machine-learning-service.md)에 나온 고급 분석 프로세스 및 기술(ADAPT) 워크플로를 사용하여 데이터 집합을 가져오고 모델을 생성하여 응용 프로그램에서 사용할 수 있는 Azure 웹 서비스로 게시하는 과정까지 일련의 작업을 단계별로 수행할 수 있습니다.

또한 이 항목에는 이 고급 분석 프로세스에서 사용되는 리소스 및 도구 중 일부가 열거되어 있습니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 질문에 대답
질문에 대답하여 고급 분석 환경을 만들기 전에 어떤 시나리오에서 작업 중인지 파악할 수 있습니다.

1. **데이터가 어디에 있습니까?** 이 위치를 ***데이터 소스***라고 합니다. 예:
	- 데이터가 HTTP 주소에 공개적으로 제공됩니다.
	- 데이터가 로컬/네트워크 파일 위치에 있습니다.
	- 데이터가 SQL Server 데이터베이스에 있습니다.
	- 데이터가 Azure 저장소 컨테이너에 저장됩니다.
2. **데이터 형식이 어떻습니까?** 예:
    - 쉼표 또는 탭으로 구분되고 압축되지 않은 파일.
    - 쉼표 또는 탭으로 구분되고 압축된 파일.
	- 압축된 또는 압축되지 않은 Azure blob.
	- SQL Server 테이블.
3. **데이터가 얼마나 큽니까?**
    - 작음: 2GB 미만
    - 보통: 2GB보다 크고 10GB보다 작음
	- 큼: 10GB 초과
	- 매우 큼: 수백 GB
4. **데이터에 얼마나 익숙합니까?**
    - 데이터 스키마, 변수 분포, 누락된 값 등을 찾으려면 데이터를 검색해야 합니까? 
	- 데이터를 테이블 형식 표현으로 변환하려면 사전 처리 또는 정리 작업이 필요합니까? 
5. **모든 데이터를 Azure 저장소로 이동할 계획입니까 또는 이동할 수 있습니까?**
    - 예, 전체 데이터 집합을 클라우드에 복사하여 처리할 계획입니다.
	- 아니요, 데이터의 하위 집합만 Azure에 복사할 것입니다.
6. **선호하는 Azure 클라우드 대상이 무엇입니까?** 예:
	- 데이터를 Azure 저장소 blob로 이동합니다.
	- 탑재식 Azure 가상 하드 디스크에 데이터를 저장합니다.
	- Azure 가상 컴퓨터의 SQL Server 데이터베이스에 데이터를 로드합니다.
	- 데이터를 Azure HDInsight Hive 테이블에 매핑합니다.

## 적합한 시나리오
이전 섹션의 질문에 대답하고 나면 어떤 시나리오가 가장 적합한지 결정할 수 있습니다. 샘플 시나리오는 [Azure 기계 학습의 고급 분석 시나리오](../machine-learning-data-science-plan-sample-scenarios.md)에 나와 있습니다.

## Azure의 고급 분석 리소스
시나리오에 따라 다음 도구와 리소스 중 일부가 필요할 수 있습니다.

1.  Azure 도구: 
	* 	[Azure PowerShell SDK](../install-configure-powershell.md), 
	* 	[Azure 저장소 탐색기](http://azurestorageexplorer.codeplex.com/)
	* 	[AzCopy](../storage-use-azcopy.md)
2.  SQL Server를 실행하는 Azure 가상 컴퓨터
3.  Azure HDInsight(Hadoop)
4.  Azure 파일 공유를 위한 온-프레미스 Azure 가상 네트워크
5.  예약된 데이터 이동을 위한 Azure 데이터 팩터리






 

<!---HONumber=July15_HO5-->