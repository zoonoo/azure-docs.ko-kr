---
title: Apache 레인저의 Apache Hive 정책-Azure HDInsight
description: Enterprise Security Package를 사용하여 Azure HDInsight 서비스에서 Hive용 Apache Ranger 정책을 구성하는 방법을 알아봅니다.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: f2d9c96a616f05c22c8b999fdc6cab2505c27485
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544939"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Enterprise Security Package를 사용하여 HDInsight에서 Apache Hive 정책 구성

Apache Hive에 대한 Apache Ranger 정책을 구성하는 방법에 대해 알아봅니다. 이 문서에서는 hivesampletable에 대한 액세스를 제한하는 두 개의 Ranger 정책을 만들 수 있습니다. hivesampletable은 HDInsight 클러스터와 함께 제공됩니다. 정책을 구성한 후 Excel 및 ODBC 드라이버를 사용 하 여 HDInsight의 Hive 테이블에 연결 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Enterprise Security Package가 포함된 HDInsight 클러스터. [ESP가 포함된 HDInsight 클러스터 구성](./apache-domain-joined-configure-using-azure-adds.md)을 참조하세요.
* 엔터프라이즈, Office 2016, Office 2013 Professional Plus, Excel 2013 독립 실행형 또는 Office 2010 Professional Plus 용 Microsoft 365 apps를 사용 하는 워크스테이션

## <a name="connect-to-apache-ranger-admin-ui"></a>Apache Ranger 관리 UI에 연결
**Ranger 관리 UI에 연결하려면**

1. 브라우저에서의 레인저 관리 UI로 이동 합니다 `https://CLUSTERNAME.azurehdinsight.net/Ranger/` . 여기서 CLUSTERNAME은 클러스터의 이름입니다.

   > [!NOTE]  
   > Ranger는 다른 Apache Hadoop 클러스터가 아닌 자격 증명을 사용합니다. 브라우저가 캐시된 Hadoop 자격 증명을 사용하지 않도록 방지하려면 새 InPrivate 브라우저 창을 사용하여 Ranger 관리 UI에 연결합니다.

2. 클러스터 관리자 도메인 사용자 이름 및 암호를 사용하여 로그인합니다.

    ![HDInsight ESP Ranger 홈페이지](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Ranger는 현재 Yarn 및 Hive에서만 작동합니다.

## <a name="create-domain-users"></a>도메인 사용자 만들기

hiveruser1 및 hiveuser2를 만드는 방법에 대한 내용은 [ESP로 HDInsight 클러스터 만들기](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)를 참조하세요. 이 문서에서는 두 개의 사용자 계정을 사용 합니다.

## <a name="create-ranger-policies"></a>Ranger 정책 만들기

이 섹션에서는 hivesampletable에 액세스하기 위한 두 개의 Ranger 정책을 만듭니다. 다른 열 집합에 대한 선택 사용 권한을 제공합니다. 두 사용자는 모두 [ESP로 HDInsight 클러스터 만들기](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)를 사용하여 생성됩니다. 다음 섹션에서는 Excel에서 두 개의 정책을 테스트 합니다.

**Ranger 정책을 만들려면**

1. Ranger 관리 UI를 엽니다. Apache Ranger 관리 UI에 연결을 참조하세요.
2. **Hive** 에서 **CLUSTERNAME_Hive** 를 선택 합니다. 두 개의 미리 구성 정책이 표시되어야 합니다.
3. **새 정책 추가** 를 선택 하 고 다음 값을 입력 합니다.

    |속성 |값 |
    |---|---|
    |정책 이름|읽기 hivesampletable-모두|
    |Hive 데이터베이스|기본값|
    |테이블|hivesampletable|
    |Hive 열|*|
    |사용자 선택|hiveuser1|
    |사용 권한|선택|

    ![HDInsight ESP 레인저 Hive 정책 구성](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    > [!NOTE]  
    > 사용자 선택에서 도메인 사용자가 채워지지 않으면 Ranger가 AAD와 동기화되기를 몇 분 정도 기다립니다.

4. **추가** 를 선택하여 정책을 저장합니다.

5. 다음 속성을 가진 다른 정책을 만들려면 마지막 두 단계를 반복합니다.

    |속성 |값 |
    |---|---|
    |정책 이름|읽기-hivesampletable-devicemake|
    |Hive 데이터베이스|기본값|
    |테이블|hivesampletable|
    |Hive 열|clientid, devicemake|
    |사용자 선택|hiveuser2|
    |사용 권한|선택|

## <a name="create-hive-odbc-data-source"></a>Hive ODBC 데이터 원본 만들기

[Hive ODBC 데이터 원본 만들기](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)에서 지침을 찾을 수 있습니다.  

 | 속성  |설명 |
 | --- | --- |
 | 데이터 원본 이름 | 데이터 원본에 이름 지정 |
 | 호스트 | CLUSTERNAME.azurehdinsight.net를 입력 합니다. 예를 들면 myHDICluster.azurehdinsight.net과 같습니다. |
 | 포트 | **443** 을 사용합니다. (이 포트는 563에서 443으로 변경됨) |
 | 데이터베이스 | **기본값** 을 사용 합니다. |
 | Hive 서버 유형 | **Hive 서버 2** 선택 |
 | 메커니즘 | **Azure HDInsight Service** 선택 |
 | HTTP 경로 | 비워 둠 |
 | 사용자 이름 | hiveuser1@contoso158.onmicrosoft.com를 입력합니다. 다른 경우 도메인 이름을 업데이트 합니다. |
 | 암호 | hiveuser1의 암호를 입력합니다. |

데이터 원본을 저장하기 전에 **테스트** 를 클릭해야 합니다.

## <a name="import-data-into-excel-from-hdinsight"></a>HDInsight에서 Excel로 데이터 가져오기

마지막 섹션에서는 두 개의 정책을 구성 했습니다.  hiveuser1에는 모든 열에 대한 선택 사용 권한이 있으며 hiveuser2에는 두 열에 대한 선택 사용 권한이 있습니다. 이 섹션에서는 데이터를 Excel로 가져오는 두 사용자를 가장할 수 있습니다.

1. Excel에서 새 통합 문서나 기존 통합 문서를 엽니다.

1. **데이터** 탭에서 **데이터 가져오기** > **기타 원본에서** > **ODBC에서** 로 이동하여 **ODBC에서** 창을 시작합니다.

    ![데이터 연결 마법사 열기](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. 드롭다운 목록에서 마지막 섹션에서 만든 데이터 원본 이름을 선택 하 고 **확인** 을 선택 합니다.

1. 처음 사용 하는 경우 **ODBC 드라이버** 대화 상자가 열립니다. 왼쪽 메뉴에서 **창** 을 선택 합니다. 그런 다음 **연결** 을 선택 하 여 **탐색기** 창을 엽니다.

1. **데이터베이스 및 테이블 선택** 대화 상자가 열릴 때까지 기다립니다. 몇 초 정도 걸릴 수 있습니다.

1. **Hivesampletable** 을 선택 하 고 **다음** 을 선택 합니다.

1. **완료** 를 선택합니다.

1. **데이터 가져오기** 대화 상자에서 쿼리를 변경하거나 지정할 수 있습니다. 이렇게 하려면 **속성** 을 선택 합니다. 몇 초 정도 걸릴 수 있습니다.

1. **정의** 탭을 선택 합니다. 명령 텍스트는 다음과 같습니다.

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"`
    ```

   정의한 Ranger 정책으로 hiveuser1에는 모든 열에 대한 선택 사용 권한이 생성됩니다.  이 쿼리는 1의 자격 증명을 사용 하 여 작동 하지만이 쿼리는 hiveuser2's 자격 증명으로 작동 하지 않습니다.

1. **확인** 을 선택 하 여 연결 속성 대화 상자를 닫습니다.

1. **확인** 을 선택 하 여 **데이터 가져오기** 대화 상자를 닫습니다.  

1. hiveuser1의 암호를 다시 입력하고 **확인** 을 클릭합니다. 데이터를 Excel로 가져올 때까지 몇 초 정도 걸립니다. 완료 되 면 11 개의 데이터 열이 표시 됩니다.

지난 섹션에서 만든 두 번째 정책(read-hivesampletable-devicemake)을 테스트하려면

1. Excel에서 새 시트를 추가합니다.
2. 데이터를 가져오는 마지막 절차를 따릅니다.  유일한 차이점은 hiveuser1의 자격 증명 대신 hiveuser2의 자격 증명을 사용하는 것입니다. hiveuser2에 두 열을 표시하는 사용 권한이 있기 때문에 작업에 실패합니다. 다음 오류가 표시됩니다.

    ```output
    [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
    ```

3. 데이터를 가져오는 동일한 절차를 따릅니다. 또한 이번에는 hiveuser2의 자격 증명을 사용하여 다음에서 select 문을 수정합니다.

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"
    ```

    다음과 같이 변경합니다.

    ```sql
    SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"
    ```

    완료 되 면 가져온 데이터 열이 두 개 표시 됩니다.

## <a name="next-steps"></a>다음 단계

* Enterprise Security Package가 포함된 HDInsight 클러스터 구성에 대한 내용은 [ESP가 포함된 HDInsight 클러스터 구성](./apache-domain-joined-configure-using-azure-adds.md)을 참조하세요.
* ESP가 포함된 HDInsight 클러스터를 관리하려면 [ESP가 포함된 HDInsight 클러스터 관리](apache-domain-joined-manage.md)를 참조하세요.
* ESP가 포함된 HDInsight 클러스터에서 SSH를 사용하여 Hive 쿼리를 실행하려면 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)을 참조하세요.
* Hive JDBC를 사용하여 Hive를 연결하는 자세한 내용은 [Hive JDBC 드라이버를 사용하여 Azure HDInsight에서 Apache Hive에 연결](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)을 참조하세요.
* Hive ODBC를 사용하여 Hadoop에 Excel을 연결하는 자세한 내용은 [Microsoft Hive ODBC 드라이브와 함께 Apache Hadoop에 Excel 연결](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)을 참조하세요.
* 파워 쿼리를 사용하여 Hadoop에 Excel을 연결하는 자세한 내용은 [파워 쿼리를 사용하여 Apache Hadoop에 Excel 연결](../hadoop/apache-hadoop-connect-excel-power-query.md)을 참조하세요.