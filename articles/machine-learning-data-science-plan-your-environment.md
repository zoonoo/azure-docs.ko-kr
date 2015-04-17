<properties 
	pageTitle="클라우드 데이터 과학 환경 계획 | Azure" 
	description="클라우드 데이터 과학 환경 계획" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
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
	ms.date="03/24/2015" 
	ms.author="mohabib;bradsev" /> 


# Azure 기계 학습 데이터 과학 환경 계획

Azure 기계 학습 데이터 과학 환경을 설정할 때 몇 가지 사항을 결정해야 합니다. 데이터의 유형, 크기 및 원본 위치와 클라우드에서 이 데이터의 대상 위치에 따라 결정하면 됩니다. 클라우드 데이터 과학 프로세스는 일부 소스의 원본 데이터를 가져와서 응용 프로그램에서 사용 가능한 Azure 웹 서비스로써 모델을 만들어서 게시하는 일련의 작업입니다.

클라우드 데이터 과학 프로세스 워크플로는 **[데이터 과학 프로세스 맵](machine-learning-data-science-how-to-create-machine-learning-service.md)**에 표시되어 있습니다. 데이터 과학 프로세스의 각 단계에 대한 자세한 내용을 보려면 [맵](machine-learning-data-science-how-to-create-machine-learning-service.md)의 관련 항목을 클릭하세요.

이 문서에서는 클라우드 데이터 과학 환경을 설정할 때 고려할 질문을 알아보고, 이 프로세스에 유용한 리소스 및 도구를 살펴보고, 클라우드 데이터 과학 프로세스 맵을 사용하는 방법에 대한 지침을 제공합니다.

## 고려할 질문

클라우드 데이터 과학 환경을 만들기 전에 다음 질문을 고려해야 합니다.

1. **데이터가 어디에 있습니까?** 이 위치는 ***데이터 원본***으로 참조됩니다. 예를 들면 다음과 같습니다.
	- 데이터가 HTTP 주소에 공개적으로 제공됩니다.
	- 데이터가 로컬/네트워크 파일 위치에 있습니다.
	- 데이터가 SQL Server 데이터베이스에 있습니다.
	- 데이터가 Azure 저장소 컨테이너에 저장됩니다.
2. **데이터 형식이 어떻습니까?** 예를 들면 다음과 같습니다.
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
5. **모든 데이터를 Azure로 이동할 계획입니까 또는 이동할 수 있습니까?**
    - 예, 전체 데이터 집합을 클라우드에 복사하여 처리할 계획입니다.
	- 아니요, 데이터의 하위 집합만 Azure에 복사할 것입니다.
6. **선호하는 Azure 클라우드 대상이 무엇입니까?** 예를 들면 다음과 같습니다.
	- 데이터를 Azure 저장소 blob로 이동합니다.
	- 탑재식 Azure 가상 하드 디스크에 데이터를 저장합니다.
	- Azure 가상 컴퓨터의 SQL Server 데이터베이스에 데이터를 로드합니다.
	- 데이터를 Azure HDInsight Hive 테이블에 매핑합니다.

## Azure의 클라우드 데이터 과학 리소스

시나리오에 따라 다음이 필요할 수 있습니다.

1.  Azure 도구: [Azure PowerShell SDK](install-configure-powershell.md), [Azure 저장소 탐색기](http://azurestorageexplorer.codeplex.com/), [AzCopy](storage-use-azcopy.md) 및 기타 도구
2.  SQL Server를 실행하는 Azure 가상 컴퓨터
3.  Azure HDInsight(Hadoop)
4.  Azure 파일 공유를 위한 온-프레미스 Azure 가상 네트워크
5.  예약된 데이터 이동을 위한 Azure 데이터 팩터리


## 클라우드 데이터 과학 프로세스 맵 사용 방법

[클라우드 데이터 과학 프로세스 맵](machine-learning-data-science-how-to-create-machine-learning-service.md)은 다양한 데이터 과학 연습을 제공합니다. 맵은 일반 데이터 과학 워크플로와 관련된 핵심 단계를 보여 줍니다. 데이터 과학 연습의 모든 단계가 반드시 필요한 것은 아닙니다. 또한 프로세스가 반복적이며, 연습에 따라 단계의 순서가 달라질 수 있습니다. 위의 질문에 대한 답을 고민해 보면 사례와 관련된 단계를 결정할 때 도움이 됩니다. 프로세스에서 해당 단계가 필요한 시기와 단계 반복이 필요한 상황을 파악하는 데 도움이 될 것입니다.

Azure에서 원본 데이터 크기, 데이터 원본 위치 및 대상 리포지토리에 따른 샘플 시나리오는 [Azure 기계 학습의 클라우드 데이터 과학 프로세스](hmachine-learning-data-science-plan-sample-scenarios.md)를 참조하세요.



<!--HONumber=49-->