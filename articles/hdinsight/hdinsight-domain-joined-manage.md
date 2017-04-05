---
title: "도메인 가입 HDInsight 클러스터 관리 | Microsoft 문서"
description: "도메인에 가입된 HDInsight 클러스터를 관리하는 방법을 알아봅니다."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 6ebc4d2f-2f6a-4e1e-ab6d-af4db6b4c87c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/25/2016
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: e2b2e8d6110062efb99201f4daac1e1295d9b904
ms.lasthandoff: 03/25/2017


---
# <a name="manage-domain-joined-hdinsight-clusters-preview"></a>도메인에 가입된 HDInsight 클러스터 관리(미리 보기)
도메인에 가입된 HDInsight의 사용자 및 역할에 대해 알아보고 도메인에 가입된 HDInsight 클러스터를 관리하는 방법을 알아봅니다.

## <a name="users-of-domain-joined-hdinsight-clusters"></a>도메인에 가입된 HDInsight 클러스터의 사용자
도메인 가입되어 있지 않은 HDInsight 클러스터에는 클러스터를 만드는 중에 생성되는 두 개의 사용자 계정이 있습니다.

* **Ambari 관리자**: 이 계정을 *Hadoop 사용자* 또는 *HTTP 사용자*라고도 합니다. 이 계정은 https://&lt;clustername>.azurehdinsight.net에서 Ambari에 로그온할 때 사용할 수 있습니다. 또한 Ambari 뷰에서 쿼리를 실행하고, 외부 도구(즉, PowerShell, Templeton, Visual Studio)를 통해 작업을 실행하고, Hive ODBC 드라이버와 BI 도구(즉, Excel, PowerBI 또는 Tableau)를 인증할 때에도 사용할 수 있습니다.
* **SSH 사용자**: 이 계정은 SSH와 함께 사용할 수 있으며, sudo 명령을 실행합니다. 그리고 Linux VM에 대한 루트 권한이 있습니다.

도메인에 가입된 HDInsight 클러스터에는 Ambari 관리자와 SSH 사용자 외에도 3개의 새로운 사용자가 있습니다.

* **Ranger 관리자**: 이 계정은 로컬 Apache Ranger 관리자 계정입니다. 이 계정은 Active Directory 도메인 사용자가 아닙니다. 이 계정은 정책을 설정하고 다른 사용자를 관리자 또는 위임된 관리자로 만드는 데(해당 사용자가 정책을 관리할 수 있도록) 사용할 수 있습니다. 기본적으로 사용자 이름은 *admin*이고 암호는 Ambari 관리자 암호와 동일합니다. 암호는 Ranger의 설정 페이지에서 업데이트할 수 있습니다.
* **클러스터 관리 도메인 사용자**: 이 계정은 Ambari 및 Ranger를 포함하여 Hadoop 클러스터 관리자로 지정된 Active Directory 도메인 사용자입니다. 클러스터를 만드는 동안 이 사용자의 자격 증명을 입력해야 합니다. 이 사용자는 다음과 같은 권한을 갖고 있습니다.

  * 컴퓨터를 도메인에 가입하고 클러스터를 만드는 동안 지정하는 OU 내에 배치합니다.
  * 클러스터를 만드는 동안 지정하는 OU 내에 서비스 사용자를 만듭니다.
  * 역방향 DNS 항목을 만듭니다.

    다른 AD 사용자도 이러한 권한이 있습니다.

    클러스터 내에는 Ranger를 통해 관리되지 않는 끝점(예: Templeton)이 있으며, 따라서 이러한 끝점은 안전하지 않습니다. 이러한 끝점은 클러스터 관리 도메인 사용자를 제외한 모든 사용자에게 잠겨 있습니다.
* **일반**: 클러스터를 만들 때 여러 Active Directory 그룹을 제공할 수 있습니다. 이러한 그룹의 사용자는 Ranger 및 Ambari와 동기화됩니다. 이러한 사용자는 도메인 사용자이며 Ranger를 통해 관리되는 끝점(예: Hiveserver2)에만 액세스할 수 있습니다. 이러한 사용자에게는 모든 RBAC 정책 및 감사가 적용됩니다.

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>도메인에 가입된 HDInsight 클러스터의 역할
도메인에 가입된 HDInsight에는 다음과 같은 역할이 있습니다.

* 클러스터 관리자
* 클러스터 운영자
* 서비스 관리자
* 서비스 운영자
* 클러스터 사용자

**이러한 역할의 사용 권한을 보려면**

1. Ambari 관리 UI를 엽니다.  [Ambari 관리 UI 열기](#open-the-ambari-management-ui)를 참조하세요.
2. 왼쪽 메뉴에서 **역할**을 클릭합니다.
3. 파란색 물음표를 클릭하여 사용 권한을 살펴봅니다.

    ![도메인에 가입된 HDInsight 클러스터의 역할 사용 권한](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Ambari 관리 UI 열기
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 블레이드에서 HDInsight 클러스터를 엽니다. [클러스터 나열 및 표시](hdinsight-administer-use-management-portal.md#list-and-show-clusters)를 참조하세요.
3. 위쪽 메뉴에서 **대시보드** 를 클릭하여 Ambari를 엽니다.
4. 클러스터 관리자 도메인 사용자 이름 및 암호를 사용하여 Ambari에 로그온합니다.
5. 오른쪽 상단 모서리에서 **관리자** 드롭다운 메뉴를 클릭한 다음 **Ambari 관리**를 클릭합니다.

    ![도메인에 가입된 HDInsight 관리 Ambari](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    UI는 다음과 같습니다.

    ![도메인에 가입된 HDInsight Ambari 관리 UI](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Active Directory에서 동기화된 도메인 사용자 나열
1. Ambari 관리 UI를 엽니다.  [Ambari 관리 UI 열기](#open-the-ambari-management-ui)를 참조하세요.
2. 왼쪽 메뉴에서 **사용자**를 클릭합니다. Active Directory에서 HDInsight 클러스터로 동기화된 모든 사용자가 표시됩니다.

    ![도메인에 가입된 HDInsight Ambari 관리 UI 사용자 나열](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Active Directory에서 동기화된 도메인 그룹 나열
1. Ambari 관리 UI를 엽니다.  [Ambari 관리 UI 열기](#open-the-ambari-management-ui)를 참조하세요.
2. 왼쪽 메뉴에서 **그룹**을 클릭합니다. Active Directory에서 HDInsight 클러스터로 동기화된 모든 그룹이 표시됩니다.

    ![도메인에 가입된 HDInsight Ambari 관리 UI 그룹 나열](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Hive 뷰 사용 권한 구성
1. Ambari 관리 UI를 엽니다.  [Ambari 관리 UI 열기](#open-the-ambari-management-ui)를 참조하세요.
2. 왼쪽 메뉴에서 **뷰**를 클릭합니다.
3. **HIVE**를 클릭하여 세부 정보를 표시합니다.

    ![도메인에 가입된 HDInsight Ambari 관리 UI Hive 뷰](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. **Hive 뷰**를 클릭하여 Hive 뷰를 구성합니다.
5. 아래에 있는 **사용 권한** 섹션으로 스크롤합니다.

    ![도메인에 가입된 HDInsight Ambari 관리 UI Hive 뷰 사용 권한 구성](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. **사용자 추가** 또는 **그룹 추가**를 클릭한 다음 Hive 뷰를 사용할 수 있는 사용자 또는 그룹을 지정합니다.

## <a name="configure-users-for-the-roles"></a>역할에 대해 사용자 구성
 역할 및 각 역할의 사용 권한을 보려면 [도메인에 가입된 HDInsight 클러스터의 역할](#roles-of-domain---joined-hdinsight-clusters)을 참조하세요.

1. Ambari 관리 UI를 엽니다.  [Ambari 관리 UI 열기](#open-the-ambari-management-ui)를 참조하세요.
2. 왼쪽 메뉴에서 **역할**을 클릭합니다.
3. **사용자 추가** 또는 **그룹 추가**를 클릭하여 여러 역할에 사용자 및 그룹을 할당합니다.

## <a name="next-steps"></a>다음 단계
* 도메인에 가입된 HDInsight 클러스터 구성에 대한 자세한 내용은 [도메인에 가입된 HDInsight 클러스터 구성](hdinsight-domain-joined-configure.md)을 참조하세요.
* Hive 정책 및 Hive 쿼리 실행에 대한 자세한 내용은 [도메인에 가입된 HDInsight 클러스터에 대한 Hive 정책 구성](hdinsight-domain-joined-run-hive.md)을 참조하세요.
* 도메인에 가입된 HDInsight 클러스터에서 SSH를 사용하여 Hive 쿼리를 실행하려면 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)을 참조하세요.

