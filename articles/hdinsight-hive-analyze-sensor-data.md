<properties 
	pageTitle="Hive 및 Microsoft Azure HDInsight(Hadoop)를 사용하여 센서 데이터 분석" 
	description="Hive와 Excel을 사용하여 HDInsight(Hadoop)에서 센서 데이터를 분석 및 시각화하는 방법에 대해 알아봅니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

# HDInsight와 함께 Hive를 사용하여 센서 데이터 분석

HDInsight(Hadoop)에서 Hive를 사용하여 센서 데이터를 분석한 다음 Microsoft Excel에서 Power View를 사용하여 데이터를 시각화하는 방법에 대해 알아봅니다.

이 샘플에서는 Hive를 사용하여 HVAC(공기조화 냉난방) 시스템에서 생성하는 기록 데이터를 처리해 설정된 온도를 안정적으로 유지할 수 없는 시스템을 식별합니다. 다음 방법에 대해 알아봅니다.

- HIVE 테이블을 만들어 CSV(쉼표로 구분된 값) 파일에 저장된 데이터 쿼리
- HIVE 쿼리를 만들어 데이터 분석
- Microsoft Excel을 사용하여 ODBC 연결을 통해 HDInsight에 연결해 분석된 데이터 검색
- Power View를 사용하여 데이터 시각화

![A diagram of the solution architecture](./media/hdinsight-use-hive-sensor-data-analysis/hvac-architecture.png)

## 필수 조건:

* HDInsight(Hadoop) 클러스터 - 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop 클러스터 프로비전](/ko-kr/documentation/articles/hdinsight-provision-clusters/)을 참조하세요.

* Microsoft Excel 2013

	> [AZURE.NOTE] Microsoft Excel은 현재 Windows에서만 제공되는 [Power View](https://support.office.com/en-US/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US)를 사용한 데이터 시각화에 사용됩니다.

* [Microsoft Hive ODBC 드라이버](http://www.microsoft.com/ko-kr/download/details.aspx?id=40886)

## 샘플을 실행하려면

1. Azure 관리 포털에서 샘플을 실행할 클러스터를 클릭한 다음 아래쪽에서 **쿼리 콘솔**을 클릭합니다. 다음 URL을 사용하여 쿼리 콘솔을 직접 열 수도 있습니다.

	 	https://<clustername>.azurehdinsight.net

	메시지가 표시되면 이 클러스터를 프로비전할 때 사용한 관리자 사용자 이름과 암호를 사용하여 인증합니다.

2. 그러면 열리는 웹 페이지에서 **시작 갤러리** 탭을 클릭하고 **샘플** 범주에서 **웹 사이트 로그 분석** 샘플을 클릭합니다.

3. 웹 페이지에서 제공되는 지침에 따라 샘플을 완료합니다.

<!--HONumber=45--> 
