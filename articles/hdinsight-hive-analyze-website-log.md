<properties 
	pageTitle="HDInsight Hadoop에서 웹 사이트 로그 분석에 Hive 사용 | Azure" 
	description="HDInsight와 함께 Hive를 사용하여 웹 사이트 로그를 분석하는 방법에 대해 알아봅니다. 로그 파일을 HDInsight 테이블에 대한 입력으로 사용하고 HiveQL을 사용해 데이터를 쿼리합니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/14/2014" 
	ms.author="nitinme"/>

# HDInsight와 함께 Hive를 사용하여 웹 사이트의 로그 분석

HDInsight와 함께 HiveQL을 사용하여 웹 사이트의 로그를 분석하는 방법에 대해 알아봅니다. 웹 사이트 로그 분석을 통해 비슷한 활동을 기준으로 대상을 구분하고, 인구 통계별로 사이트 방문자를 분류하고, 방문자가 보는 콘텐츠와 이전에 방문했던 웹 사이트 등을 확인할 수 있습니다.

이 샘플에서는 HDInsight 클러스터를 사용하여 웹 사이트 로그 파일을 분석해 외부 웹 사이트로부터의 일일 웹 사이트 방문 빈도를 파악하고 사용자에게 발생하는 웹 사이트 오류의 요약을 확인하는 방법에 대해 알아봅니다. 다음 방법에 대해 알아봅니다.

- 웹 사이트 로그 파일을 포함하는 Azure 저장소 Blob에 연결
- HIVE 테이블을 만들어 로그 쿼리
- HIVE 쿼리를 만들어 데이터 분석
- Microsoft Excel을 사용하여 ODBC 연결을 통해 HDInsight에 연결해 분석된 데이터 검색

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##필수 조건

- **HDInsight 클러스터**를 미리 프로비전해야 합니다. 관련 지침은 [HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요. 
- Microsoft Excel 2010 또는 Microsoft Excel 2013을 설치해야 합니다.
- Hive에서 Excel로 데이터를 가져오려면 [Microsoft Hive ODBC 드라이버](http://www.microsoft.com/ko-kr/download/details.aspx?id=40886)가 필요합니다.


##샘플을 실행하려면

1. Azure 관리 포털에서 샘플을 실행할 클러스터를 클릭한 다음 아래쪽에서 **쿼리 콘솔**을 클릭합니다. 다음 URL을 사용하여 쿼리 콘솔을 직접 열 수도 있습니다.

	 	https://<clustername>.azurehdinsight.net
	
	메시지가 표시되면 클러스터를 프로비전할 때 사용한 관리자 사용자 이름과 암호를 사용하여 인증합니다.
  
2. 그러면 열리는 웹 페이지에서 **시작 갤러리** 탭을 클릭하고 **샘플** 범주에서 **웹 사이트 로그 분석** 샘플을 클릭합니다.
3. 웹 페이지에서 제공되는 지침에 따라 샘플을 완료합니다.

##다음 단계
샘플을 실행하여 Azure HDInsight를 사용해 센서 데이터를 분석하는 방법을 확인할 수 있습니다. [HDInsight와 함께 Hive를 사용하여 센서 데이터 분석][hdinsight-sensor-data-sample]을 참조하세요.


[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis/

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png<!--HONumber=42-->
