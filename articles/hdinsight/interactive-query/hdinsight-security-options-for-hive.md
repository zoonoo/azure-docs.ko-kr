---
title: Azure HDInsight의 Hive에 대 한 보안 옵션
description: 표준 및 ESP 클러스터의 Hive에 대 한 보안 옵션입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: 8573ba99b7aef13025b4f175640ac9583ad5a679
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545959"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Azure HDInsight의 Hive에 대 한 보안 옵션

이 문서에서는 HDInsight의 Hive에 대 한 권장 보안 옵션을 설명 합니다. 이러한 옵션은 Ambari를 통해 구성할 수 있습니다.

![' Hive에 대 한 보안 옵션 '](./media/hdinsight-security-options-for-hive/security-options-hive.png "Hive에 대 한 보안 옵션")

## <a name="hiveserver2-authentication"></a>HiveServer2 인증

표준 클러스터의 경우 HiveServer2 authentication에 권장 되는 설정인 기본값은 none입니다. 인증을 사용 하도록 설정 하려면 [ESP](../domain-joined/hdinsight-security-overview.md) (Enterprise Security Package) 클러스터로 업그레이드 하는 것이 좋습니다. 

ESP 클러스터의 경우 [Kerberos](https://web.mit.edu/Kerberos/) 인증은 기본적으로 사용 하도록 설정 됩니다. PAM (플러그형 인증 모듈) 및 사용자 지정 인증 체계가 지원 되지 않습니다.

## <a name="hiveserver2-authorization"></a>HiveServer2 권한 부여

표준 클러스터의 경우 기본 설정은 None입니다. [Sqlstdauth (SQL 표준 기반 권한 부여)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) 를 사용 하도록 설정할 수 있습니다. 표준 클러스터에는 [Apache 레인저](https://ranger.apache.org/) 를 통한 권한 부여가 지원 되지 않습니다. 레인저 권한 부여를 위해 ESP 클러스터로 업그레이드 하는 것이 좋습니다. 

ESP 클러스터의 경우에는 기본적으로 레인저를 통한 권한 부여가 사용 하도록 설정 됩니다. 


## <a name="ssl-encryption-for-hiveserver2"></a>HiveServer2에 대 한 SSL 암호화

표준 또는 ESP 클러스터의 경우 Hiveserver2 SSL을 사용 하지 않는 것이 좋습니다. 대신 SSL이 게이트웨이에서 사용 됩니다. [전송 중 암호화](../domain-joined/encryption-in-transit.md) 는 [IPSec (인터넷 프로토콜 보안)](https://en.wikipedia.org/wiki/IPsec)을 사용 하 여 클러스터 노드 간 통신을 암호화 하는 데 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계
* [HiveServer2 인증 개요](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [HiveServer2 권한 부여 개요](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization#:~:text=%20Overview%20of%20Authorization%20Modes%20%201%201,and%20Apache%20Sentry%20are%20apache%20projects...%20More%20)
* [SQL 표준 기반 Hive 권한 부여 사용](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Hive를 사용 하는 Apache 레인저](../domain-joined/apache-domain-joined-run-hive.md)
