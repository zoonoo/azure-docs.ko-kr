---
title: 파워 쿼리로 Apache Hadoop에 Excel 연결 - Azure HDInsight
description: 비즈니스 인텔리전스 구성 요소를 사용하는 방법과 Excel용 파워 쿼리를 사용하여 HDInsight의 Hadoop에 저장된 데이터에 액세스하는 방법에 대해 알아봅니다.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 687a9884c861b4cf72e51f9179e6c0b43968801d
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202914"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>파워 쿼리를 사용하여 Apache Hadoop에 Excel 연결
Microsoft의 빅데이터 솔루션의 주요 기능 중 하나는 Microsoft BI(비즈니스 인텔리전스) 구성 요소를 Azure HDInsight의 Apache Hadoop 클러스터와 통합하는 것입니다. 주요 예제로 Microsoft Excel용 파워 쿼리 추가 기능을 사용하여 Hadoop 클러스터와 연결된 데이터가 포함된 Azure Storage 계정에 Excel을 연결하는 기능이 있습니다. 이 자습서에서는 파워 쿼리를 설정하고 사용하여 HDInsight로 관리하는 Hadoop 클러스터와 연결된 데이터를 쿼리하는 방법을 단계별로 안내합니다.

### <a name="prerequisites"></a>필수 조건
이 문서를 시작하기 전에 다음 항목이 있어야 합니다.

* **HDInsight 클러스터**. 구성하려면 [Azure HDInsight 시작][hdinsight-get-started]을 참조하세요.
* **워크스테이션** .
* **Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 Standalone 또는 Office 2010 Professional Plus**.

## <a name="install-power-query"></a>파워 쿼리 설치
파워 쿼리는 출력되었거나 HDInsight 클러스터에 대해 실행되는 Hadoop 작업에서 생성된 데이터를 가져올 수 있습니다.

Excel 2016에서는 파워 쿼리가 데이터 리본의 가져오기 및 변환 섹션 아래에 통합되었습니다. 이전 Excel 버전의 경우 [Microsoft 다운로드 센터][powerquery-download]에서 Microsoft Excel용 파워 쿼리를 다운로드하여 설치합니다.

## <a name="import-hdinsight-data-into-excel"></a>Excel로 HDInsight 데이터 가져오기
Excel용 파워 쿼리 추가 기능을 사용하면 HDInsight 클러스터에서 Excel로 쉽게 데이터를 가져올 수 있으며, 여기서 PowerPivot 및 파워 맵 같은 BI 도구를 사용하여 데이터를 검사하고 분석하며 표시할 수 있습니다.

**HDInsight 클러스터에서 데이터 가져오기**

1. Excel을 엽니다.
2. 비어 있는 새 통합 문서를 만듭니다.
3. Excel 버전에 따라 다음 단계를 수행합니다.

   - Excel 2016

     - **데이터** 메뉴를 클릭하고 **데이터 가져오기 및 변환** 리본에서 **데이터 가져오기**를 클릭한 후 **Azure에서**를 클릭하고 **Azure HDInsight(HDFS)에서**를 클릭합니다.

       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

   - Excel 2013/2010

     - **파워 쿼리** 메뉴를 클릭하고 **Azure에서**를 클릭한 후 **Microsoft Azure HDInsight에서**를 클릭합니다.
   
       ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
       **참고:** 표시 되지 않는 경우는 **파워 쿼리** 로 이동 메뉴 **파일** > **옵션** > **추가 기능**, 선택한  **COM 추가 기능** 드롭다운 목록에서 **관리** 페이지의 맨 위에 있는 상자입니다. **이동...** 단추를 선택하고 Excel용 파워 쿼리 추가 기능에 해당하는 상자가 선택되어 있는지 확인합니다.
       
       **참고:** 또한 파워 쿼리를 사용하면 **기타 원본에서**를 클릭하여 HDFS에서 데이터를 가져올 수 있습니다.
4. **Windows에서 Azure File Storage 시작** 이 계정은 기본 저장소 계정이 나 연결된 된 저장소 계정의 수 있습니다.  형식은 *https://&lt;StorageAccountName>.blob.core.windows.net/* 입니다.
5. 표준 스토리지 계정에는 Blob, 테이블, 큐 및 File Storage가 포함됩니다. (이 저장소에 처음 액세스할 때만 계정 정보를 입력하면 됩니다.)
6. Azure File Storage는 표준 SMB 프로토콜을 사용하여 클라우드에서 파일 공유를 제공합니다. 기본적으로 컨테이너 이름은 클러스터 이름과 같습니다.
7. **Name** 열에서 **HiveSampleData.txt**(폴더 경로 **../hive/warehouse/hivesampletable/**)를 찾은 후 HiveSampleData.txt 왼쪽에 있는 **Binary**를 클릭합니다. HiveSampleData.txt는 모든 클러스터와 함께 제공됩니다. 필요에 따라 사용자의 파일을 사용할 수 있습니다.
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. 원하는 경우 열 이름을 바꿀 수 있습니다. 준비가 되면 **닫은 후 로드**를 클릭합니다.  통합 문서에 데이터가 로드됩니다.
   
    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>다음 단계
이 문서에서는 파워 쿼리를 사용하여 HDInsight에서 Excel로 데이터를 가져오는 방법을 알아보았습니다. 마찬가지로 HDInsight에서 Azure SQL Database로 데이터를 가져올 수 있습니다. 데이터를 HDInsight에 업로드할 수도 있습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight에서 Microsoft Power BI를 사용하여 Apache Hive 데이터 시각화](apache-hadoop-connect-hive-power-bi.md)
* [Azure HDInsight에서 Power BI를 사용하여 대화형 쿼리 Hive 데이터 시각화](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Azure HDInsight에서 Apache Zeppelin을 사용하여 Apache Hive 쿼리 실행](./../hdinsight-connect-hive-zeppelin.md)
* [Microsoft Hive ODBC Driver로 HDInsight에 Excel 연결](apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결 및 Apache Hive 쿼리 실행](apache-hadoop-visual-studio-tools-get-started.md)
* [Azure HDInsight Tool for Visual Studio Code 사용](../hdinsight-for-vscode.md)
* [HDInsight에 데이터 업로드](./../hdinsight-upload-data.md)

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: https://go.microsoft.com/fwlink/?LinkID=286689
