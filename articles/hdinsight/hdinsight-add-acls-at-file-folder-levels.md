---
title: "파일 및 폴더 수준에서 사용자 권한 관리 - Azure HDInsight | Microsoft Docs"
description: "도메인에 가입된 HDInsight 클러스터에 대한 파일 및 폴더 사용 권한을 관리하는 방법입니다."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 42d617ffeb8c2fee6be6d747b39d80b09774a1c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-permissions-at-the-file-and-folder-levels"></a>파일 및 폴더 수준에서 사용자 권한 관리

[도메인에 가입된 HDInsight 클러스터](hdinsight-domain-joined-introduction.md)는 Azure AD(Azure Active Directory) 사용자에 대해 강력한 인증을 사용하고, YARN 및 Hive와 같은 다양한 서비스에 대해 RBAC(*역할 기반 액세스 제어*)를 사용합니다. 클러스터의 기본 데이터 저장소가 Azure Storage 또는 MASB(Microsoft Azure Storage Blob)인 경우 파일 및 폴더 수준 사용 권한을 적용할 수도 있습니다. Apache Ranger를 사용하여 동기화된 Azure AD 사용자 및 그룹의 클러스터 파일에 대한 액세스를 제어할 수 있습니다.
<!-- [synchronized Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md). -->

HDInsight 도메인 가입 클러스터용 Apache Ranger 인스턴스는 Ranger-WASB 서비스로 미리 구성되어 있습니다. Ranger-WASB 서비스는 Ranger-HDFS와 유사한 정책 관리 엔진이지만 다른 Ranger 액세스 정책이 적용됩니다. Ranger-WASB 서비스에서 들어오는 리소스 요청에 일치하는 Ranger 정책이 없는 경우 기본 응답은 DENY입니다. Ranger 서비스는 WASB에 대한 권한 검사를 통과하지 못합니다.

## <a name="permission-and-policy-model"></a>사용 권한 및 정책 모델

리소스 액세스 요청은 다음과 같은 흐름을 사용하여 평가됩니다.

![Apache Ranger 정책 평가 흐름](./media/hdinsight-add-acls-at-file-folder-levels/ranger-policy-evaluation-flow.png)

먼저 DENY 규칙이 평가된 후 ALLOW 규칙이 평가됩니다. 일치 여부 확인이 끝난 후에 일치하는 정책이 없으면 DENY가 반환됩니다.

### <a name="user-variable"></a>USER 변수

사용자가 `/{username}` 하위 디렉터리에 액세스하기 위한 정책을 할당할 때 `{USER}` 변수를 사용할 수 있습니다. 예를 들면 다음과 같습니다.

```
resource: path=/app-logs/{USER}, user: {USER}, recursive=true, permissions: all, delegateAdmin=true
```

위 정책은 사용자에게 `/app-logs/` 디렉터리 아래의 자체 하위 폴더에 대한 액세스를 허가합니다. 이 정책은 Ranger 사용자 인터페이스에서 다음과 같이 표시됩니다.

![USER 변수 사용 예제](./media/hdinsight-add-acls-at-file-folder-levels/user-variable.png)

### <a name="policy-model-examples"></a>정책 모델 예제

다음 표에서는 정책 모델의 작동 방식에 대한 몇 가지 예제를 보여 줍니다.

| Ranger 정책 | 기존 파일 시스템 | 사용자 요청 | 결과 |
| -- | -- | -- | -- |
| /data/finance/, bob, WRITE | /data | bob, Create file /data/finance/mydatafile.txt | ALLOW - 상위 항목 검사 때문에 중간 폴더 'finance'가 만들어짐 |
| /data/finance/, bob, WRITE | /data | alice, Create file /data/finance/mydatafile.txt | DENY - 일치하는 정책 없음 |
| /data/finance*, bob, WRITE | /data | bob, Create file /data/finance/mydatafile.txt | ALLOW - 이 경우 선택적 재귀 정책(`*`)이 있음. [와일드카드](#wildcards) 참조 |
| /data/finance/mydatafile.txt, bob, WRITE | /data | bob, Create file /data/finance/mydatafile.txt | DENY - 정책이 없으므로 '/data'에 대한 상위 항목 검사가 실패함 |
| /data/finance/mydatafile.txt, bob, WRITE | /data/finance | bob, Create file /data/finance/mydatafile.txt | DENY- ‘/data/finance’의 상위 항목 검사에 대한 정책 없음 |

작업 유형에 따라 폴더 수준 또는 파일 수준의 권한이 필요합니다. 예를 들어 "read/open" 호출은 파일 수준의 읽기 액세스 권한을 요구하지만 “create” 호출은 상위 폴더 수준의 사용 권한을 요구합니다.

### <a name="wildcards-"></a>와일드카드(*)

정책 경로에 와일드카드(`*`)가 있으면 와일드카드는 해당 경로 및 전체 하위 트리에 적용됩니다. 이 재귀는 `recurse-flag`를 사용하는 것과 같습니다. Ranger-WASB에서 와일드카드는 재귀 및 부분 이름 일치 둘 다를 나타냅니다.

## <a name="manage-file-and-folder-level-permissions-with-apache-ranger"></a>Apache Ranger를 사용해 파일 및 폴더 수준 사용 권한 관리

새 도메인 가입 클러스터를 아직 프로비전하지 않은 경우 [다음 지침](hdinsight-domain-joined-configure.md)을 따라 프로비전하세요.

`https://<YOUR CLUSTER NAME>.azurehdinsight.net/ranger/`으로 이동하여 Ranger-WASB를 엽니다. 클러스터를 만들 때 정의한 클러스터 관리자 사용자 이름 및 암호를 입력합니다.

로그인하면 Ranger 대시보드가 표시됩니다.

![Ranger 대시보드](./media/hdinsight-add-acls-at-file-folder-levels/ranger-dashboard.png)

클러스터의 연결된 Azure Storage 계정에 대한 현재 파일 및 폴더 사용 권한을 보려면 WASB 제어 상자에서 ***CLUSTERNAME*_wasb** 링크를 클릭합니다.

![Ranger 대시보드](./media/hdinsight-add-acls-at-file-folder-levels/wasb-dashboard-link.png)

현재 정책 목록이 표시됩니다. 몇 가지 일반적인 정책이 시작점으로 포함되어 있습니다. 각 정책의 세부 정보에서 사용 예제를 볼 수 있습니다.

각 정책에 대해 정책이 사용되도록 설정되어 있는지 여부, 감사 로깅이 구성되어 있는지 여부, 할당된 그룹 및 사용자를 볼 수 있습니다. 각 정책에 대해 2개의 작업 단추인 Edit(편집) 및 Delete(삭제)가 제공됩니다.

![정책 목록](./media/hdinsight-add-acls-at-file-folder-levels/policy-list.png)

### <a name="adding-a-new-policy"></a>새 정책 추가

1. WASB 정책 페이지의 오른쪽 위에서 **Add New Policy**(새 정책 추가)를 선택합니다.

    ![Add New Policy(새 정책 추가)](./media/hdinsight-add-acls-at-file-folder-levels/add-new.png)

2. 설명을 포함하는 **정책 이름**을 입력합니다. 클러스터를 만들 때 지정한 클러스터의 Azure **Storage 계정**(*ACCOUNT_NAME*.blob.core.windows.net) 및 **저장소 계정 컨테이너**를 지정합니다. 액세스한 폴더 또는 파일의 **상대 경로**(클러스터 기준)를 입력합니다.

    ![새 정책 양식](./media/hdinsight-add-acls-at-file-folder-levels/new-policy.png)

3. 이 양식 아래에 이 새 리소스에 대한 **Allow Conditions**(허용 조건)를 지정합니다. 적용 가능한 그룹 및 사용자를 선택하고 권한을 설정합니다. 다음 예제에서는 `sales` 그룹의 모든 사용자에게 읽기/쓰기 액세스 권한을 허용합니다.

    ![sales 허용](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales.png)

4. **저장**을 선택합니다.

### <a name="example-policy-conditions"></a>예제 정책 조건

Apache Ranger [정책 평가 흐름](#permission-and-policy-model)을 요구에 맞는 허용 및 거부 조건 조합을 지정할 수 있습니다. 다음은 몇 가지 예입니다.

1. 모든 영업 담당 사용자 허용(인턴 제외):

    ![영업 담당 사용자 허용, 인턴 거부](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns.png)

2. 모든 영업 담당 사용자를 허용하고 모든 인턴을 거부하지만 "hiveuser3"라는 인턴은 예외적으로 읽기 권한을 갖습니다.

    ![영업 담당 사용자 허용, hiveuser3를 제외한 인턴 거부](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns-except-hiveuser3.png)

## <a name="next-steps"></a>다음 단계

* [도메인에 가입된 HDInsight에서 Hive 정책 구성](hdinsight-domain-joined-run-hive.md)
* [도메인에 가입된 HDInsight 클러스터 관리](hdinsight-domain-joined-manage.md)
* [Ambari 관리 - 사용자에게 Ambari 권한 부여](hdinsight-authorize-users-to-ambari.md)

<!-- * [Synchronize Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md) -->

