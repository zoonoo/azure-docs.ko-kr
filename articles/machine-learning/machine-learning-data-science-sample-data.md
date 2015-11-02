<properties 
	pageTitle="Cortana 분석 프로세스의 데이터 샘플링" 
	description="다양한 저장소 환경에서 데이터를 탐색하는 방법" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015" 
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>Cortana 분석 프로세스의 데이터 샘플링

이 **메뉴**는 다양한 저장소 환경에서 데이터를 샘플링하는 방법을 설명하는 항목에 연결되는 링크입니다. 이 작업은 Cortana 분석 프로세스(CAP)의 한 단계입니다.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-explore-data-selector.md)]

## 소개

이 문서에서는 프로그래밍 방식으로 다운로드한 다음 샘플 Python 코드로 샘플링하여 Azure Blob 저장소에 저장된 데이터를 샘플링하는 방법에 대해 알아봅니다. 작업 단계는 다음과 같습니다.

이 문서에서는 Cortana 분석 프로세스에 일반적으로 사용되는 세 위치가 저장된 데이터를 샘플링하는 방법을 설명합니다.

- **Azure blob 컨테이너 데이터**는 프로그래밍 방식으로 다운로드한 다음 샘플 Python 코드로 샘플링함으로써 샘플링됩니다.
- **SQL Server 데이터**는 SQL 및 Python 프로그래밍 언어를 모두 사용하여 샘플링됩니다. 
- **Hive 테이블 데이터**는 Hive 쿼리를 사용하여 샘플링됩니다.

<!---HONumber=Oct15_HO4-->