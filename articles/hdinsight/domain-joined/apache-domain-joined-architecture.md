---
title: 도메인에 연결된 Azure HDInsight 아키텍처 | Microsoft Docs
description: 도메인에 가입된 HDInsight를 계획하는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: f4380f5d6ec379d5807f697294623a672bd270ae
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715244"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>HDInsight에서 Azure 도메인에 가입된 Hadoop 클러스터 계획

표준 HDInsight 클러스터는 단일 사용자 클러스터입니다. 대량 데이터 워크로드를 구축하는 작은 응용 프로그램 팀이 있는 대부분의 회사에 적합합니다. 각 사용자에게는 주문형으로 자신 전용인 다른 클러스터가 있고 더 이상 필요하지 않은 경우 삭제합니다. 하지만 대부분의 기업이 클러스터를 IT 팀에서 관리하고 여러 응용 프로그램 팀이 클러스터를 공유하는 모델로 이동하고 있습니다. 따라서 클러스터에 대한 다중 사용자 액세스는 이러한 대기업에 대한 Azure HDInsight에 필요합니다.

HDInsight는 가장 널리 사용되는 ID 공급자인 AD(Active Directory)를 관리되는 방식으로 사용합니다. [AAD-DS(Azure Active Directory Domain Services)](../../active-directory-domain-services/active-directory-ds-overview.md)와 HDInsight를 통합하고 도메인 자격 증명을 사용하여 클러스터에 액세스할 수 있습니다. HDInsight의 VM은 제공된 도메인에 도메인 가입됩니다. 따라서 HDInsight에서 실행 중인 모든 서비스(Ambari, Hive 서버, Ranger, Spark thrift 서버 등)가 인증된 사용자에 대해 원활하게 작동합니다. 관리자는 Apache 레인저를 사용하여 클러스터의 리소스에 대한 역할 기반 액세스 제어를 제공하는 강력한 권한 부여 정책을 만들 수 있습니다.


## <a name="integrate-hdinsight-with-active-directory"></a>Active Directory와 HDInsight 통합

오픈 소스 Hadoop는 인증 및 보안을 제공하기 위해 Kerberos를 사용합니다. 따라서 HDInsight 클러스터 노드는 AAD-DS에서 관리하는 도메인에 도메인 가입됩니다. Kerberos 보안은 클러스터의 Hadoop 구성 요소에 대해 구성됩니다. 각 Hadoop 구성 요소의 경우 서비스 사용자는 자동으로 생성됩니다. 또한 해당 컴퓨터 보안 주체는 도메인에 가입된 각 컴퓨터에 대해 만들어집니다. 이러한 서비스 및 컴퓨터 주체를 저장하기 위해 이러한 원칙을 배치할 위치인 도메인 컨트롤러(AAD-DS) 내에서 OU(조직 구성 단위)를 제공해야 합니다. 

요약하면 다음으로 환경을 설정해야 합니다.

- Active Directory 도메인(AAD-DS에서 관리함)
- AAD-DS에서 사용하도록 설정된 LDAP(LDAPS)를 보호합니다.
- HDInsight의 VNET에서 AAD-DS VNET로 적절한 네트워크를 연결한 경우 여기에 별도 VNET을 선택합니다. HDI VNET 내의 VM은 VNET 피어링을 사용하여 AAD-DS를 사용해야 합니다. HDI 및 AAD-DS 모두를 동일한 VNET에 배포하면 연결이 자동으로 제공되고 추가 작업이 필요하지 않습니다.
- [AAD-DS에서 만든](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) OU(조직 구성 단위)
- 다음에 대한 사용 권한을 가진 서비스 계정:
    - OU에 서비스 사용자를 만듭니다.
    - 컴퓨터를 도메인에 가입하고 OU에 컴퓨터 보안 주체를 만듭니다.

다음 스크린샷은 contoso.com에서 만든 OU를 보여 줍니다. 서비스 사용자 및 컴퓨터 보안 주체 중 일부도 스크린샷에 표시됩니다.

![도메인에 가입된 HDInsight 클러스터 OU](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png)에서도 확인할 수 있습니다.

### <a name="different-domain-controllers-setup"></a>다른 도메인 컨트롤러 설정:
HDInsight는 클러스터가 Kerberise 클러스터와 연결되는 주 도메인 컨트롤러로 현재 AAD-DS만을 지원합니다. 그러나 HDI 액세스에 AAD-DS를 사용하도록 설정하면 복잡한 다른 AD 설치도 가능합니다.

- **[AAD-DS(Azure Active Directory Domain Services)](../../active-directory-domain-services/active-directory-ds-overview.md)**: 이 서비스는 Windows Server Active Directory와 완벽하게 호환되는 관리 도메인을 제공합니다. Microsoft는 HA(고가용성) 설정에서 도메인을 관리하고, 패치하고, 모니터링하는 작업에 주의를 기울입니다. 도메인 컨트롤러를 유지 관리할 걱정 없이 클러스터를 배포할 수 있습니다. 사용자가 회사 자격 증명을 사용하여 클러스터에 로그인할 수 있도록 사용자, 그룹 및 암호가 AAD(Azure Active Directory)에서 동기화됩니다[AAD에서 AAD-DS로 단방향 동기화]. 자세한 내용은 [AAD-DS를 사용하여 도메인 가입 HDInsight 클러스터를 구성하는 방법](./apache-domain-joined-configure-using-azure-adds.md)을 참조하세요.
- **온-프레미스 AD 또는 IaaS VM의 AD**: 도메인에 대해 온-프레미스 AD 또는 더 복잡한 다른 AD 설정이 있는 경우 AD Connect를 사용하여 AAD에 해당 ID를 동기화한 다음, 해당 AD 테넌트에서 AAD-DS를 사용할 수 있습니다. Kerberos가 암호 해시를 사용하므로 [AAD-DS에서 암호 해시 동기화를 사용하도록 설정](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)해야 합니다. ADFS(AD 페더레이션 서비스)에서 페더레이션을 사용하면 ADFS 인프라에 실패하는 경우 백업으로 암호 해시 동기화를 선택적으로 설정할 수 있습니다. 자세한 내용은 [AAD Connect에서 암호 해시 동기화 사용](../../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)을 참조하세요. AAD와 AAD-DS 없이 온-프레미스 AD 또는 IaaS VMs의 AD만을 사용하면 HDI 클러스터 도메인 가입에 지원되는 구성이 없습니다.

## <a name="next-steps"></a>다음 단계
* [도메인 가입 HDInsight 클러스터 구성](apache-domain-joined-configure-using-azure-adds.md)
* [도메인 가입 HDInsight 클러스터에 Hive 정책 구성](apache-domain-joined-run-hive.md)
* [도메인 가입 HDInsight 클러스터 관리](apache-domain-joined-manage.md) 
