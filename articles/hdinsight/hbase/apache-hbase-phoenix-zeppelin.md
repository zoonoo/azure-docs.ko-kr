---
title: Apache Phoenix를 사용 하 여 Azure HDInsight에서 Apache Base 쿼리 실행
description: Apache Zeppelin를 사용 하 여 Phoenix로 Apache 기본 쿼리를 실행 하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 10/14/2019
ms.openlocfilehash: 2a6b324c4caf9619566ba335ab2e5a318ab68bb6
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86079431"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Apache Zeppelin를 사용 하 여 Azure HDInsight에서 Apache HBase를 통해 Apache Phoenix 쿼리 실행

Apache Phoenix는 HBase에서 구축되는 오픈 소스 대규모 병렬 관계형 데이터베이스 계층입니다. Phoenix를 사용 하면 HBase를 통해 SQL과 같은 쿼리를 사용할 수 있습니다. Phoenix는 아래의 JDBC 드라이버를 사용 하 여 SQL 테이블, 인덱스, 뷰 및 시퀀스를 만들고 삭제 하 고 변경할 수 있습니다.  Phoenix를 사용 하 여 개별적으로 또는 대량으로 행을 업데이트할 수도 있습니다. Phoenix는 MapReduce를 사용 하 여 쿼리를 컴파일하는 대신 NOSQL 네이티브 컴파일을 사용 하 여 HBase 위에 대기 시간이 짧은 응용 프로그램을 만들 수 있도록 합니다.

Apache Zeppelin는 오픈 소스 웹 기반 노트북으로, 대화형 데이터 분석 및 SQL, Scala 등의 언어를 사용 하 여 데이터 기반 공동 작업 문서를 만들 수 있습니다. 데이터 개발자는 데이터 과학자 & 데이터 조작을 위한 코드를 개발, 구성, 실행 및 공유할 수 있습니다. 명령줄을 참조 하거나 클러스터 세부 정보를 필요로 하지 않고 결과를 시각화할 수 있습니다.

HDInsight 사용자는 Apache Zeppelin를 사용 하 여 Phoenix 테이블을 쿼리할 수 있습니다. Apache Zeppelin는 HDInsight 클러스터와 통합 되며이를 사용 하는 추가 단계가 없습니다. JDBC 인터프리터를 사용 하 여 Zeppelin 노트북을 만들고 Phoenix SQL 쿼리 작성을 시작 하기만 하면 됩니다.

## <a name="prerequisites"></a>전제 조건

HDInsight의 Apache HBase 클러스터. [Apache HBase 시작을](./apache-hbase-tutorial-get-started-linux.md)참조 하세요.

## <a name="create-an-apache-zeppelin-note"></a>Apache Zeppelin 노트 만들기

1. `CLUSTERNAME`을 다음 URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`에서 클러스터의 이름으로 바꿉니다. 그런 다음, 웹 브라우저에 URL을 입력합니다. 클러스터 로그인 사용자 이름 및 암호를 입력합니다.

1. Zeppelin 페이지에서 **새 노트 만들기**를 선택 합니다.

    ![HDInsight 대화형 쿼리 zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. **새 메모 만들기** 대화 상자에서 다음 값을 입력하거나 선택합니다.

    - 참고 이름: 메모의 이름을 입력 합니다.
    - 기본 인터프리터: 드롭다운 목록에서 **jdbc** 를 선택 합니다.

    그런 다음 **메모 만들기**를 선택 합니다.

1. 노트북 머리글이 연결 된 상태를 표시 하는지 확인 합니다. 오른쪽 위 모서리에 녹색 점으로 표시 됩니다.

    ![Zeppelin 노트북 상태](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Zeppelin 노트북 상태")

1. HBase 테이블을 만듭니다. 다음 명령을 입력 하 고 **Shift + enter**를 누릅니다.

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    첫 번째 줄의 **% jdbc (phoenix)** 문은 전자 필기장에 phoenix jdbc 인터프리터를 사용 하도록 지시 합니다.

1. 만든 테이블을 봅니다.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. 테이블에 값을 삽입합니다.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. 테이블을 쿼리합니다.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. 레코드를 삭제합니다.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. 테이블을 삭제합니다.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>다음 단계

- [이제 Apache Phoenix는 Azure HDInsight에서 Zeppelin을 지원 합니다.](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [Apache Phoenix 문법](https://phoenix.apache.org/language/index.html)
