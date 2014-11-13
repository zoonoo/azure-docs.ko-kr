<properties urlDisplayName="HDInsight and Excel" pageTitle="파워 쿼리로 Hadoop에 Excel 연결 | Azure" metaKeywords="hdinsight, excel, data explorer, hive excel, hdinsight excel, power query" description="비즈니스 인텔리전스 구성 요소를 사용하는 방법과 Excel을 통해 파워 쿼리를 사용하여 Azure HDInsight에 저장된 데이터에 액세스하는 방법에 대해 알아봅니다." metaCanonical="" services="hdinsight" documentationCenter="" title="파워 쿼리로 Hadoop에 Excel 연결" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# 파워 쿼리로 Hadoop에 Excel 연결

Microsoft의 빅데이터 솔루션이 가진 주요 기능 중 하나는 Microsoft BI(비즈니스 인텔리전스) 구성 요소를 HDInsight의 Hadoop 클러스터와 통합하는 것입니다. 이 통합의 주요 예제로 Microsoft Excel용 파워 쿼리를 사용하여 Excel을 Hadoop 클러스터와 연결된 데이터가 포함된 Azure 저장소 계정에 연결하는 기능을 들 수 있습니다. 이 자습서에서는 Excel에서 파워 쿼리를 설정하고 사용하여 HDInsight으로 관리하는 Hadoop 클러스터와 연결된 데이터를 쿼리하는 방법을 단계별로 안내합니다.

**필수 조건**

이 문서를 시작하기 전에 다음이 있어야 합니다.

-   HDInsight 클러스터. 구성하려면 [Azure HDInsight 시작][Azure HDInsight 시작]을 참조하세요.
-   Windows 8, Windows 7, Windows Server 2012 또는 Windows Server 2008 R2를 실행하는 컴퓨터.
-   Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone 또는 Office 2010 Professional Plus

## 이 문서에서는 다음을 수행합니다.

-   [Microsoft Excel용 파워 쿼리 설치][Microsoft Excel용 파워 쿼리 설치]
-   [Excel로 데이터 가져오기][Excel로 데이터 가져오기]
-   [다음 단계][다음 단계]

## <span id="InstallPowerQuery"></span></a>Microsoft Excel용 파워 쿼리 설치

파워 쿼리를 사용하여 다양한 원본의 데이터를 Microsoft Excel로 가져올 수 있으며 여기서 파워 쿼리는 PowerPivot 및 파워 뷰와 같은 BI(비즈니스 인텔리전스) 도구를 구동할 수 있습니다. 특히 파워 쿼리는 출력되었거나 HDInsight 클러스터에 대해 실행되는 Hadoop 작업에서 생성된 데이터를 가져올 수 있습니다.

[Microsoft 다운로드 센터][Microsoft 다운로드 센터]에서 Excel용 Microsoft 파워 쿼리를 다운로드하여 설치합니다.

## <span id="ImportData"></span></a>Excel로 HDInsight 데이터 가져오기

Excel용 파워 쿼리 추가 기능을 사용하면 HDInsight 클러스터에서 Excel로 쉽게 데이터를 가져올 수 있으며, 여기서 PowerPivot 및 파워 맵 같은 비즈니스 인텔리전스 도구를 사용하여 데이터를 검사, 분석하고 표시할 수 있습니다.

**HDInsight 클러스터에서 데이터를 가져오려면**

1.  Excel을 엽니다.

2.  비어 있는 새 통합 문서를 만듭니다.

3.  **파워 쿼리** 메뉴를 클릭하고 **기타 원본**을 클릭한 후 **Azure HDInsight에서**를 클릭합니다.

    ![HDI.PowerQuery.SelectHdiSource][HDI.PowerQuery.SelectHdiSource]

    참고: **파워 쿼리** 메뉴가 표시되지 않는 경우 **파일** \> **옵션** \> **추가 기능**으로 이동하여 페이지 아래쪽에 있는 **관리자** 상자에서 **COM 추가 기능**을 선택합니다. **이동...** 단추를 선택하여 Microsoft Office Excel용 파워 쿼리 추가 기능에 해당하는 상자가 선택되어 있는지 확인합니다.

4.  클러스터와 연결된 Azure Blob 저장소 계정의 **계정 이름**을 입력하고 **확인**을 클릭합니다.

5.  Blob 저장소 계정의 **계정 키**를 입력하고 **저장**을 클릭합니다. (이 저장소에 처음 액세스할 때만 이 단계를 수행하면 됩니다.)

6.  **쿼리 편집기** 왼쪽에 있는 **탐색기** 창에서 Blob 저장소 컨테이너 이름을 두 번 클릭합니다. 기본적으로 컨테이너 이름은 클러스터 이름과 같습니다.

7.  **Name** 열에서 **HiveSampleData.txt**(폴더 경로 **../hive/warehouse/hivesampletable/**)를 찾은 후 HiveSampleData.txt 왼쪽에 있는 **Binary**를 클릭합니다.

    ![HDI.PowerQuery.ImportData][HDI.PowerQuery.ImportData]

8.  원하는 경우 열 이름을 바꿀 수 있습니다. 준비가 되면 **적용 후 닫기**를 클릭합니다.

    ![HDI.PowerQuery.ImportedTable][HDI.PowerQuery.ImportedTable]

## <span id="NextSteps"></span></a>다음 단계

이 문서에서는 파워 쿼리를 사용하여 HDInsight에서 Excel로 데이터를 가져오는 방법을 알아보았습니다. 마찬가지로 HDInsight에서 SQL Azure로 데이터를 가져올 수 있습니다. 데이터를 HDInsight에 업로드할 수도 있습니다. 자세한 내용은 다음 문서를 참조하세요.

-   [HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결][HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결]
-   [HDInsight에 데이터 업로드][HDInsight에 데이터 업로드].

  [Azure HDInsight 시작]: ../hdinsight-get-started/
  [Microsoft Excel용 파워 쿼리 설치]: #InstallPowerQuery
  [Excel로 데이터 가져오기]: #ImportData
  [다음 단계]: #NextSteps
  [Microsoft 다운로드 센터]: http://go.microsoft.com/fwlink/?LinkID=286689
  [HDI.PowerQuery.SelectHdiSource]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
  [HDI.PowerQuery.ImportData]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
  [HDI.PowerQuery.ImportedTable]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG
  [HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [HDInsight에 데이터 업로드]: ../hdinsight-upload-data/
