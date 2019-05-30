---
title: Power BI를 사용하여 Apache Hive 데이터 시각화 - Azure HDInsight
description: Microsoft Power BI를 사용하여 Azure HDInsight에서 처리한 Hive 데이터를 시각화하는 방법에 대해 알아봅니다.
keywords: hdinsight,hadoop,hive,대화형 쿼리,대화형 hive,LLAP,odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: hrasheed
ms.openlocfilehash: 1e0c043e484e4eaf2639f76c9af3fef15ad85047
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237495"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Azure HDInsight의 ODBC를 사용하여 Microsoft Power BI와 Apache Hive 데이터 시각화

Apache Hive 데이터 시각화 하 고 ODBC를 사용 하 여 Azure HDInsight에 Microsoft Power BI Desktop을 연결 하는 방법을 알아봅니다.

>[!IMPORTANT]
> Hive ODBC 드라이버를 활용하여 Power BI Desktop에서 제네릭 ODBC 커넥터를 통해 가져오기를 수행할 수 있습니다. 그러나 BI 워크로드의 경우 Hive 쿼리 엔진의 비대화형 특성을 지정하지 않는 것이 좋습니다. 성능을 개선하기 위해 [대화형 쿼리 HDInsight 커넥터](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) 및 [HDInsight Spark 커넥터](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect)를 선택할 수 있습니다.

이 자습서에서 데이터 로드는 `hivesampletable` Power BI로 Hive 테이블. Hive 테이블에는 일부 휴대폰 사용 현황 데이터가 포함되어 있습니다. 그런 다음 전 세계 맵에 사용 현황 데이터를 그림으로 나타냅니다.

![HDInsight Power BI 맵 보고서](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

정보는 새 [대화형 쿼리](../interactive-query/apache-interactive-query-get-started.md) 클러스터 유형에도 적용됩니다. 직접 쿼리를 사용하여 HDInsight 대화형 쿼리에 연결하는 방법은 [Visualize Interactive Query Hive data with Microsoft Power BI using direct query in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)(Azure HDInsight의 직접 쿼리를 사용하여 Microsoft Power BI로 대화형 쿼리 Hive 데이터 시각화)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 문서를 시작하기 전에 다음 항목이 있어야 합니다.

* **HDInsight 클러스터**. 클러스터는 Hive를 사용한 HDInsight 클러스터 또는 새로 릴리스된 대화형 쿼리 클러스터일 수 있습니다. 클러스터를 만드는 방법은 [클러스터 만들기](apache-hadoop-linux-tutorial-get-started.md#create-cluster)를 참조하세요.

* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=45331)에서 복사본을 다운로드할 수 있습니다.

## <a name="create-hive-odbc-data-source"></a>Hive ODBC 데이터 원본 만들기

[Hive ODBC 데이터 원본 만들기](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source)를 참조하세요.

## <a name="load-data-from-hdinsight"></a>HDInsight에서 데이터 로드

hivesampletable Hive 테이블은 모든 HDInsight 클러스터와 함께 제공됩니다.

1. Power BI Desktop을 시작 합니다.

2. 위쪽 메뉴에서로 이동 **Home** > **데이터 가져오기** > **더...** .

    ![HDInsight Power BI 데이터 열기](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

3. **데이터 가져오기** 대화 상자에서 **다른** 왼쪽에서 선택 **ODBC** 오른쪽에서 선택한 후 **Connect** 맨 아래에서.

4. **에서 ODBC** 대화 상자에서 데이터 소스는 드롭다운 목록에서 마지막 섹션에서 만든 이름 및 선택한 **확인**합니다.

5. **탐색기** 대화 상자에서 확장 **ODBC > HIVE > 기본**를 선택 **hivesampletable**를 선택한 후 **부하**합니다.

6. **ODBC 드라이버** 대화 상자에서 **기본 또는 사용자 지정**을 선택한 후 **Connect**합니다.

## <a name="visualize-data"></a>데이터 가상화

마지막 절차에서 계속 진행합니다.

1. 시각화 창에서 **맵**을 선택합니다.  지구 아이콘입니다.

    ![HDInsight Power BI 보고서 사용자 지정](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. **필드** 창 **국가** 하 고 **devicemake**합니다. 맵에 표시된 데이터를 볼 수 있습니다.
3. 맵을 확장합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Power BI를 사용하여 HDInsight에서 데이터를 시각화하는 방법을 알아보았습니다.  자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight에서 Apache Zeppelin을 사용하여 Apache Hive 쿼리 실행](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Microsoft Hive ODBC Driver로 HDInsight에 Excel 연결](./apache-hadoop-connect-excel-hive-odbc-driver.md)
* [파워 쿼리를 사용하여 Apache Hadoop에 Excel 연결](apache-hadoop-connect-excel-power-query.md)
* [Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결 및 Apache Hive 쿼리 실행](apache-hadoop-visual-studio-tools-get-started.md)
* [Azure HDInsight Tool for Visual Studio Code 사용](../hdinsight-for-vscode.md)
* [HDInsight에 데이터 업로드](./../hdinsight-upload-data.md)
