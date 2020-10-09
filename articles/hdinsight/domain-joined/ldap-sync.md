---
title: Azure HDInsight의 레인저 및 Apache Ambari의 LDAP 동기화
description: 레인저 및 Ambari에서 LDAP 동기화를 처리 하 고 일반 지침을 제공 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77463220"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight의 레인저 및 Apache Ambari의 LDAP 동기화

HDInsight Enterprise Security Package (ESP) 클러스터는 권한 부여를 위해 레인저를 사용 합니다. Apache Ambari와 레인저는 모두 독립적으로 사용자와 그룹을 동기화 하 고 약간 다르게 작동 합니다. 이 문서는 레인저 및 Ambari의 LDAP 동기화를 해결 하기 위한 것입니다.

## <a name="general-guidelines"></a>일반 지침

* 항상 그룹을 사용 하 여 클러스터를 배포 합니다.
* Ambari 및 레인저에서 그룹 필터를 변경 하는 대신 Azure AD에서 이러한 모든 항목을 관리 하 고 중첩 된 그룹을 사용 하 여 필요한 사용자를 가져옵니다.
* 사용자가 동기화 되 면 사용자가 그룹의 일부가 아닌 경우에도 제거 되지 않습니다.
* LDAP 필터를 직접 변경 해야 하는 경우 유효성 검사를 포함 하 고 있는 UI를 먼저 사용 합니다.

## <a name="users-are-synced-separately"></a>사용자가 별도로 동기화 됨

Ambari와 레인저는 서로 다른 두 가지 용도를 제공 하므로 사용자 데이터베이스를 공유 하지 않습니다. 사용자가 Ambari UI를 사용 해야 하는 경우 사용자를 Ambari로 동기화 해야 합니다. 사용자가 Ambari에 동기화 되지 않은 경우 Ambari UI/API는이를 거부 하지만 시스템의 다른 부분이 작동 합니다 (이는 레인저 또는 리소스 관리자에서 보호 되 고 Ambari는 안 됨). 사용자를 레인저 정책에 포함 하려면 사용자를 레인저에 동기화 합니다.

보안 클러스터를 배포 하는 경우 그룹 구성원은 Ambari 및 레인저 모두에 전이적으로 (모든 하위 그룹 및 해당 구성원) 동기화 됩니다. 

## <a name="ambari-user-sync-and-configuration"></a>Ambari 사용자 동기화 및 구성

헤드 노드에서 cron 작업은 `/opt/startup_scripts/start_ambari_ldap_sync.py` 1 시간 마다 실행 되어 사용자 동기화를 예약 합니다. Cron 작업은 Ambari rest Api를 호출 하 여 동기화를 수행 합니다. 이 스크립트는 동기화 할 사용자 및 그룹 목록을 전송 합니다. 사용자는 지정 된 그룹에 속할 수 없으며 둘 다 개별적으로 지정 됩니다. Ambari는 sAMAccountName을 사용자 이름으로, 모든 그룹 구성원을 전이적으로 동기화 합니다.

로그는에 있어야 합니다 `/var/log/ambari-server/ambari-server.log` . 자세한 내용은 [Ambari 로깅 수준 구성](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)을 참조 하세요.

Data Lake 클러스터에서 게시 사용자 만들기 후크는 동기화 된 사용자에 대 한 홈 폴더를 만드는 데 사용 되며 홈 폴더의 소유자로 설정 됩니다. 사용자가 Ambari에 올바르게 동기화 되지 않은 경우 사용자는 준비 및 기타 임시 폴더에 액세스할 때 오류가 발생할 수 있습니다.

### <a name="update-groups-to-be-synced-to-ambari"></a>Ambari 동기화 할 그룹 업데이트

Azure AD에서 그룹 멤버 자격을 관리할 수 없는 경우 다음 두 가지 옵션을 선택할 수 있습니다.

* 전체 [LDAP 사용자 및 그룹 동기화](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html)에 설명 된 대로 일회성 동기화를 수행 합니다. 그룹 멤버 자격이 변경 될 때마다이 동기화를 다시 수행 해야 합니다.

* Cron 작업을 작성 하 고 새 그룹을 사용 하 여 [주기적으로 AMBARI API](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) 를 호출 합니다.

## <a name="ranger-user-sync-and-configuration"></a>레인저 사용자 동기화 및 구성

레인저에는 사용자를 동기화 하기 위해 1 시간 마다 실행 되는 기본 제공 동기화 엔진이 있습니다. Ambari와 사용자 데이터베이스를 공유 하지 않습니다. HDInsight는 클러스터를 만드는 동안 지정 된 그룹의 멤버와 관리 사용자를 동기화 하도록 검색 필터를 구성 합니다. 그룹 구성원은 전이적으로 동기화 됩니다.

* 증분 동기화를 사용 하지 않습니다.
* 사용자 그룹 동기화 맵을 사용 하도록 설정 합니다.
* 전이적 그룹 구성원을 포함 하도록 검색 필터를 지정 합니다.
* 사용자에 대 한 sAMAccountName 및 그룹에 대 한 이름 특성을 동기화 합니다.

### <a name="group-or-incremental-sync"></a>그룹 또는 증분 동기화

레인저는 그룹 동기화 옵션을 지원 하지만 사용자 필터와의 교차로 작동 합니다. 그룹 멤버 자격과 사용자 필터 간의 공용 구조체가 아닙니다. 레인저의 그룹 동기화 필터에 대 한 일반적인 사용 사례는 그룹 필터: (dn = clusteradmingroup), 사용자 필터: (city = 시애틀).

증분 동기화는 이미 동기화 된 사용자 (처음으로는)에 대해서만 작동 합니다. 증분은 초기 동기화 후에 그룹에 추가 된 새 사용자를 동기화 하지 않습니다.

### <a name="update-ranger-sync-filter"></a>레인저 동기화 필터 업데이트

LDAP 필터는 Ambari UI의 레인저 사용자-동기화 구성 섹션에서 찾을 수 있습니다. 기존 필터는 형식이 됩니다 `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` . 끝에 조건자를 추가 하 고 `net ads` 검색 명령 또는 ldp.exe 또는 이와 유사한 항목을 사용 하 여 필터를 테스트 합니다.

## <a name="ranger-user-sync-logs"></a>레인저 사용자 동기화 로그

레인저 사용자 동기화는 헤드 노드 중 하나에서 발생할 수 있습니다. 로그는에 `/var/log/ranger/usersync/usersync.log` 있습니다. 로그의 자세한 정도를 높이려면 다음 단계를 수행 합니다.

1. Ambari에 로그인 합니다.
1. 레인저 구성 섹션으로 이동 합니다.
1. Advanced **usersync-log4j** 섹션으로 이동 합니다.
1. `log4j.rootLogger`을 수준으로 변경 합니다 `DEBUG` (변경 후 처럼 표시 되어야 함 `log4j.rootLogger = DEBUG,logFile,FilterLog` ).
1. 구성을 저장 하 고 레인저를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight의 인증 문제](./domain-joined-authentication-issues.md)
* [Azure AD 사용자를 HDInsight 클러스터에 동기화](../hdinsight-sync-aad-users-to-cluster.md)
