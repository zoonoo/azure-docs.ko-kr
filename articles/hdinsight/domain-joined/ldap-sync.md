---
title: Azure HDInsight에서 Ranger와 Apache Ambari의 LDAP 동기화
description: Ranger와 Ambari의 LDAP 동기화를 처리하고 일반적인 지침을 제공합니다.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: fb9035b4d816c1af84b15e6865335aa1bdf86f5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933422"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight에서 Ranger와 Apache Ambari의 LDAP 동기화

HDInsight ESP(Enterprise Security Package) 클러스터는 권한 부여를 위해 Ranger를 사용합니다. Apache Ambari와 Ranger는 둘 다 독립적으로 사용자와 그룹을 동기화하고 약간 다르게 작동합니다. 이 문서는 Ranger와 Ambari의 LDAP 동기화를 처리하기 위한 것입니다.

## <a name="general-guidelines"></a>일반 지침

* 항상 하나 이상의 그룹이 있는 클러스터를 배포합니다.
* 클러스터에서 더 많은 그룹을 사용하려면 Azure AD(Azure Active Directory)에서 그룹 멤버 자격을 업데이트하는 것이 적절한지 여부를 확인합니다.
* 클러스터 그룹을 변경하려는 경우 Ambari를 사용하여 동기화 필터를 변경할 수 있습니다.
* Azure AD의 모든 그룹 멤버 자격 변경 내용은 후속 동기화에서 클러스터에 반영됩니다. 변경 내용을 먼저 Azure AD DS(Azure AD Domain Services)에 동기화한 후 클러스터에 동기화해야 합니다.
* HDInsight 클러스터는 Samba/Winbind를 사용하여 클러스터 노드에서 그룹 멤버 자격을 프로젝션합니다.
* 그룹 멤버는 Ambari와 Ranger에 대해 둘 다 전이적으로 동기화됩니다(모든 하위 그룹과 해당 멤버). 

## <a name="users-are-synced-separately"></a>사용자는 개별적으로 동기화됨

 * Ambari와 Ranger는 두 가지 다른 용도로 사용되므로 사용자 데이터베이스를 공유하지 않습니다. 
   * Ambari UI를 사용해야 하는 사용자는 Ambari로 동기화되어야 합니다. 
   * 사용자가 Ambari에 동기화되지 않으면 Ambari UI/API는 동기화를 거부하지만 시스템의 다른 부분은 작동합니다(해당 부분은 Ambari가 아닌 Ranger나 Resource Manager에 의해 보호됨).
   * Ranger 정책에 사용자나 그룹을 포함하려면 보안 주체는 Ranger에서 명시적으로 동기화되어야 합니다.

## <a name="ambari-user-sync-and-configuration"></a>Ambari 사용자 동기화와 구성

헤드 노드에서 cron 작업 `/opt/startup_scripts/start_ambari_ldap_sync.py`는 1시간마다 실행되어 사용자 동기화를 예약합니다. cron 작업은 Ambari REST API를 호출하여 동기화를 수행합니다. 스크립트는 동기화할 사용자 및 그룹 목록을 제출합니다(사용자가 지정된 그룹에 속하지 않을 수 있으므로 둘 다 개별적으로 지정됨). Ambari는 sAMAccountName을 사용자 이름으로 동기화하고 모든 그룹 멤버를 전이적으로 동기화합니다.

로그는 `/var/log/ambari-server/ambari-server.log`에 있어야 합니다. 자세한 내용은 [Ambari 로깅 수준 구성](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)을 참조하세요.

Data Lake 클러스터에서 게시 사용자 만들기 후크는 동기화된 사용자의 홈 폴더를 만드는 데 사용되며 해당 사용자는 홈 폴더의 소유자로 설정됩니다. 사용자가 Ambari에 올바르게 동기화되지 않으면 홈 폴더가 올바르게 설정되지 않을 수 있으므로 사용자에게는 작업 실행 시 오류가 발생할 수 있습니다.

## <a name="ranger-user-sync-and-configuration"></a>Ranger 사용자 동기화와 구성

Ranger에는 사용자를 동기화하기 위해 매시간 실행되는 기본 제공 동기화 엔진이 있습니다. Ranger는 Ambari와 사용자 데이터베이스를 공유하지 않습니다. HDInsight는 클러스터를 만드는 동안 지정된 그룹의 멤버, watchdog 사용자, 관리 사용자를 동기화하도록 검색 필터를 구성합니다. 그룹 멤버는 전이적으로 동기화됩니다.

1. 증분 동기화를 사용하지 않도록 설정합니다.
1. 사용자 그룹 동기화 맵을 사용하도록 설정합니다.
1. 전이적 그룹 멤버를 포함하도록 검색 필터를 지정합니다.
1. 사용자의 sAMAccountName 특성과 그룹의 name 특성을 동기화합니다.

### <a name="group-or-incremental-sync"></a>그룹 또는 증분 동기화

Ranger는 그룹 동기화 옵션을 지원하지만 그룹 멤버 자격과 사용자 필터 간 합집합이 아니라 사용자 필터와 교집합으로 작동합니다. Ranger에서 그룹 동기화 필터의 일반적인 사용 사례는 그룹 필터: (dn=clusteradmingroup), 사용자 필터: (city=seattle)입니다.

증분 동기화는 이미 동기화된(처음으로) 사용자에 대해서만 작동합니다. 증분은 초기 동기화 후에 그룹에 추가된 새 사용자를 동기화하지 않습니다.

### <a name="update-ranger-sync-filter"></a>Ranger 동기화 필터 업데이트

LDAP 필터는 Ambari UI의 Ranger 사용자 동기화 구성 섹션에서 찾을 수 있습니다. 기존 필터는 `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` 형식입니다. 끝부분에 조건자를 추가하고 `net ads` 검색 명령이나 ldp.exe 같은 항목을 사용하여 필터를 테스트해야 합니다.

## <a name="ranger-user-sync-logs"></a>Ranger 사용자 동기화 로그

Ranger 사용자 동기화는 헤드 노드 중 하나에서 발생할 수 있습니다. 로그는 `/var/log/ranger/usersync/usersync.log`에 있습니다. 로그의 세부 정보 표시를 늘리려면 다음 단계를 수행합니다.

1. Ambari에 로그인합니다.
1. Ranger 구성 섹션으로 이동합니다.
1. 고급 **usersync-log4j** 섹션으로 이동합니다.
1. `log4j.rootLogger`를 `DEBUG` 수준으로 변경합니다. 변경한 후에는 `log4j.rootLogger = DEBUG,logFile,FilterLog`와 같이 표시됩니다.
1. 구성을 저장하고 Ranger를 다시 시작합니다.

## <a name="known-issues-with-ranger-user-sync"></a>Ranger 사용자 동기화의 알려진 문제
* 그룹 이름에 유니코드 문자가 있으면 Ranger 동기화가 해당 개체를 동기화하지 못합니다. 사용자가 국제 문자를 포함하는 그룹에 속하는 경우 Ranger는 부분 그룹 멤버 자격을 동기화합니다.
* 사용자 이름(sAMAccountName)과 그룹 이름(name)은 길이가 20자 이하여야 합니다. 그룹 이름이 더 길면 사용자는 권한을 계산할 때 그룹에 속하지 않는 것처럼 처리됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight의 인증 문제](./domain-joined-authentication-issues.md)
* [Azure AD 사용자를 HDInsight 클러스터에 동기화](../hdinsight-sync-aad-users-to-cluster.md)
