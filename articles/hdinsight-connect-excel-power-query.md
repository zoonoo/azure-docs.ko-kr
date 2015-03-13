<properties 
	pageTitle="파워 쿼리로 Hadoop에 Excel 연결 | Azure" 
	description="비즈니스 인텔리전스 구성 요소를 사용하는 방법과 Excel을 통해 파워 쿼리를 사용하여 Azure HDInsight에 저장된 데이터에 액세스하는 방법에 대해 알아봅니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="bradsev"/>




#파워 쿼리로 Hadoop에 Excel 연결

Microsoft의 빅데이터 솔루션의 주요 기능 중 하나는  Microsoft BI(비즈니스 인텔리전스) 구성 요소를 HDInsight의 Hadoop 클러스터와 통합하는 것입니다. 이 통합의 주요 예제로 Microsoft Excel용 파워 쿼리를 사용하여 Excel을 Hadoop 클러스터와 연결된 데이터가 포함된 Azure 저장소 계정에 연결하는 기능을 들 수 있습니다. 이 자습서에서는 Excel에서 파워 쿼리를 설정하고 사용하여 HDInsight으로 관리하는 Hadoop 클러스터와 연결된 데이터를 쿼리하는 방법을 단계별로 안내합니다. 

**필수 조건**

이 문서를 시작하기 전에 다음이 있어야 합니다.

- HDInsight 클러스터. 구성하려면 [Azure HDInsight 시작][hdinsight-get-started]을 참조하세요.
- Windows 8, Windows 7, Windows Server 2012 또는 Windows Server 2008 R2를 실행하는 컴퓨터.
- Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone 또는 Office 2010 Professional Plus

## 이 문서에서는 다음을 수행합니다.

- [Microsoft Excel용 파워 쿼리 설치](#InstallPowerQuery)
- [Excel로 데이터 가져오기](#ImportData)
- [다음 단계](#NextSteps)


## <a id="InstallPowerQuery"></a>Microsoft Excel용 파워 쿼리 설치

파워 쿼리를 사용하여 다양한 원본의 데이터를 Microsoft Excel로 가져올 수 있으며 여기서 파워 쿼리는 PowerPivot 및 파워 뷰와 같은 BI(비즈니스 인텔리전스) 도구를 구동할 수 있습니다. 특히 파워 쿼리는 출력되었거나 HDInsight 클러스터에 대해 실행되는 Hadoop 작업에서 생성된 데이터를 가져올 수 있습니다. 

[Microsoft 다운로드 센터][powerquery-download]에서 Microsoft Excel용 파워 쿼리를 다운로드하여 설치합니다.

## <a id="ImportData"></a>Excel로 HDInsight 데이터 가져오기

Excel용 파워 쿼리 추가 기능을 사용하면 HDInsight 클러스터에서 Excel로 쉽게 데이터를 가져올 수 있으며, 여기서 PowerPivot 및 파워 맵 같은 비즈니스 인텔리전스 도구를 사용하여 데이터를 검사, 분석하고 표시할 수 있습니다.

**HDInsight 클러스터에서 데이터를 가져오려면**

1. Excel을 엽니다.

2. 비어 있는 새 통합 문서를 만듭니다.

3. **파워 쿼리** 메뉴를 클릭하고 **기타 원본**을 클릭한 후 **Azure HDInsight에서**를 클릭합니다. 

	![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

	참고: **파워 쿼리** 메뉴가 표시되지 않는 경우 **파일** > **옵션** > **추가 기능**으로 이동하고 페이지의 맨 아래에 있는 드롭다운 **관리자** 상자에서 **COM 추가 기능**을 선택합니다. **이동...** 단추를 선택하여 Microsoft Office Excel용 파워 쿼리 추가 기능에 해당하는 상자가 선택되어 있는지 확인합니다.

3. 클러스터와 연결된 Azure Blob 저장소 계정의 **계정 이름**을 입력하고 **확인**을 클릭합니다. 

4. Blob 저장소 계정의 **계정 키**를 입력하고 **저장**을 클릭합니다. (이 저장소에 처음 액세스할 때만 이 단계를 수행하면 됩니다.)	

5. **쿼리 편집기** 왼쪽에 있는 **탐색기** 창에서 Blob 저장소 컨테이너 이름을 두 번 클릭합니다. 기본적으로 컨테이너 이름은 클러스터 이름과 같습니다. 

6. **Name** 열에서 **HiveSampleData.txt**(폴더 경로 **../hive/warehouse/hivesampletable/**)를 찾은 후 HiveSampleData.txt 왼쪽에 있는 **Binary**를 클릭합니다.

	![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. 원하는 경우 열 이름을 바꿀 수 있습니다. 준비가 되면 **적용 후 닫기**를 클릭합니다.	

	![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a id="NextSteps"></a>다음 단계

이 문서에서는 파워 쿼리를 사용하여 HDInsight에서 Excel로 데이터를 가져오는 방법을 알아보았습니다. 마찬가지로 HDInsight에서 SQL Azure로 데이터를 가져올 수 있습니다. 데이터를 HDInsight에 업로드할 수도 있습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Microsoft Hive ODBC 드라이버로 HDInsight에 Excel 연결][hdinsight-ODBC](영문)
* [HDInsight에 데이터 업로드][hdinsight-upload-data]

[hdinsight-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png 
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG 

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689 
<!--HONumber=42-->
