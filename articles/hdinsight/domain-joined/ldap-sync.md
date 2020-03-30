---
title: Azure HDInsight에서 레인저와 아파치 암바리에서 LDAP 동기화
description: 레인저와 암바리에서 LDAP 동기화를 해결하고 일반적인 지침을 제공합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463220"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight에서 레인저와 아파치 암바리에서 LDAP 동기화

HDInsight 엔터프라이즈 보안 패키지(ESP) 클러스터는 승인을 위해 레인저를 사용합니다. 아파치 암바리와 레인저 는 모두 독립적으로 사용자와 그룹을 동기화하고 조금 다르게 작동합니다. 이 문서는 레인저와 암바리에서 LDAP 동기화를 해결하기위한 것입니다.

## <a name="general-guidelines"></a>일반 지침

* 항상 그룹으로 클러스터를 배포합니다.
* Ambari 및 Ranger에서 그룹 필터를 변경하는 대신 Azure AD에서 이러한 모든 필터를 관리하고 중첩된 그룹을 사용하여 필요한 사용자를 가져옵니다.
* 사용자가 동기화되면 사용자가 그룹에 속하지 않더라도 제거되지 않습니다.
* LDAP 필터를 직접 변경해야 하는 경우 먼저 UI에 몇 가지 유효성 검사가 포함되어 있으므로 UI를 사용합니다.

## <a name="users-are-synced-separately"></a>사용자가 별도로 동기화됩니다.

Ambari와 레인저는 두 가지 다른 목적을 제공하기 때문에 사용자 데이터베이스를 공유하지 않습니다. 사용자가 Ambari UI를 사용해야 하는 경우 사용자를 Ambari에 동기화해야 합니다. 사용자가 Ambari에 동기화되지 않은 경우 Ambari UI / API는 거부하지만 시스템의 다른 부분은 작동합니다 (이들은 Ambari가 아닌 레인저 또는 리소스 관리자에 의해 보호됩니다). 사용자를 레인저 정책에 포함하려면 사용자를 레인저에 동기화합니다.

보안 클러스터가 배포되면 그룹 구성원은 Ambari 및 Ranger 모두에 전이적으로 동기화됩니다(모든 하위 그룹 및 해당 멤버). 

## <a name="ambari-user-sync-and-configuration"></a>암바리 사용자 동기화 및 구성

헤드 노드에서 cron 작업은 `/opt/startup_scripts/start_ambari_ldap_sync.py`사용자 동기화를 예약하기 위해 매시간 실행됩니다. cron 작업은 Ambari 나머지 API를 호출하여 동기화를 수행합니다. 스크립트는 동기화할 사용자 및 그룹 목록을 제출합니다(사용자가 지정된 그룹에 속하지 않을 수 있기 때문에 둘 다 개별적으로 지정됩니다). Ambari는 sAMAccountName을 사용자 이름과 모든 그룹 구성원으로 전이적으로 동기화합니다.

로그는 에 `/var/log/ambari-server/ambari-server.log`있어야 합니다. 자세한 내용은 [Ambari 로깅 수준 구성을](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)참조하십시오.

Data Lake 클러스터에서 포스트 사용자 만들기 후크는 동기화된 사용자의 홈 폴더를 만드는 데 사용되며 홈 폴더의 소유자로 설정됩니다. 사용자가 Ambari에 올바르게 동기화되지 않으면 스테이징 및 기타 임시 폴더에 액세스하는 데 실패할 수 있습니다.

### <a name="update-groups-to-be-synced-to-ambari"></a>Ambari에 동기화할 그룹 업데이트

Azure AD에서 그룹 구성원 자격을 관리할 수 없는 경우 다음 두 가지 를 선택할 수 있습니다.

* [LDAP 사용자 및 그룹 동기화에서](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html)설명한 대로 한 번 동기화를 수행합니다. 그룹 멤버 자격이 변경될 때마다 이 동기화를 다시 수행해야 합니다.

* cron 작업을 작성하고 새 그룹과 [주기적으로 Ambari API를](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) 호출합니다.

## <a name="ranger-user-sync-and-configuration"></a>레인저 사용자 동기화 및 구성

레인저는 사용자를 동기화하기 위해 매 시간마다 실행되는 붙들인 동기화 엔진이 있습니다. Ambari와 사용자 데이터베이스를 공유 하지 않습니다. HDInsight는 클러스터 생성 중에 지정된 그룹의 관리자 사용자, 감시 사용자 및 구성원을 동기화하도록 검색 필터를 구성합니다. 그룹 구성원은 전이적으로 동기화됩니다.

* 증분 동기화를 사용하지 않도록 설정합니다.
* 사용자 그룹 동기화 맵을 사용하도록 설정합니다.
* 전이적 그룹 구성원을 포함하도록 검색 필터를 지정합니다.
* 사용자를 위한 sAMAccountName 동기화 및 그룹에 대한 이름 특성입니다.

### <a name="group-or-incremental-sync"></a>그룹 또는 증분 동기화

레인저는 그룹 동기화 옵션을 지원하지만 사용자 필터와의 교차점으로 작동합니다. 그룹 멤버 자격과 사용자 필터 간의 공용 구조조가 아닙니다. Ranger에서 그룹 동기화 필터의 일반적인 사용 사례는 그룹 필터:(dn=clusteradmingroup), 사용자 필터:(city=seattle)입니다.

증분 동기화는 이미 동기화된 사용자(처음)에만 작동합니다. 증분은 초기 동기화 후 그룹에 추가된 새 사용자를 동기화하지 않습니다.

### <a name="update-ranger-sync-filter"></a>업데이트 레인저 동기화 필터

LDAP 필터는 레인저 사용자 동기화 구성 섹션의 Ambari UI에서 찾을 수 있습니다. 기존 필터는 폼에 `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`있을 것입니다. 끝에 술어를 추가하고 검색 명령 또는 ldp.exe 또는 이와 유사한 것을 사용하여 `net ads` 필터를 테스트하십시오.

## <a name="ranger-user-sync-logs"></a>레인저 사용자 동기화 로그

레인저 사용자 동기화는 헤드 노드 중 하나에서 발생할 수 있습니다. 로그는 에 `/var/log/ranger/usersync/usersync.log`있습니다. 로그의 상세도를 높이려면 다음 단계를 수행합니다.

1. 암바리에 로그인합니다.
1. 레인저 구성 섹션으로 이동합니다.
1. 고급 사용자 **동기화 log4j** 섹션으로 이동합니다.
1. 레벨을 `log4j.rootLogger` 변경합니다(변경 `DEBUG` 후 는 `log4j.rootLogger = DEBUG,logFile,FilterLog`것처럼 보입니다).
1. 구성을 저장하고 레인저를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight의 인증 문제](./domain-joined-authentication-issues.md)
* [Azure AD 사용자를 HDInsight 클러스터에 동기화](../hdinsight-sync-aad-users-to-cluster.md)
