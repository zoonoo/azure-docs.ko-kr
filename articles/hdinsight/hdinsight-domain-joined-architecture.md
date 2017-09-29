---
title: "도메인에 연결된 Azure HDInsight 아키텍처 | Microsoft Docs"
description: "도메인에 가입된 HDInsight를 계획하는 방법을 알아봅니다."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2017
ms.author: saurinsh
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 08d61f95fb38962402142c0ccd0a5a2f3a7e802a
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>HDInsight에서 Azure 도메인에 가입된 Hadoop 클러스터 계획

기존 Hadoop은 단일 사용자 클러스터입니다. 대량 데이터 워크로드를 구축하는 작은 응용 프로그램 팀이 있는 대부분의 회사에 적합합니다. Hadoop이 인기를 끌면서 대부분의 기업이 클러스터를 IT 팀에서 관리하고 여러 응용 프로그램 팀이 클러스터를 공유하는 모델로 이동하고 있습니다. 따라서 다중 사용자 클러스터가 관련된 기능은 Azure HDInsight에서 가장 많이 요청되는 기능입니다.

고유한 다중 사용자 인증 및 권한 부여를 구축하는 대신 HDInsight는 가장 인기 있는 ID 공급자인 AD(Active Directory)를 사용합니다. AD의 강력한 보안 기능을 사용하여 HDInsight의 다중 사용자 권한 부여를 관리할 수 있습니다. HDInsight를 AD와 통합하면 AD 자격 증명을 사용하여 클러스터와 통신할 수 있습니다. HDInsight는 HDInsight에서 실행 중인 모든 서비스(Ambari, Hive 서버, Ranger, Spark thrift 서버 등)가 인증된 사용자에 대해 원활하게 작동하도록 AD 사용자를 로컬 Hadoop 사용자에 매핑합니다.

## <a name="integrate-hdinsight-with-active-directory"></a>Active Directory와 HDInsight 통합

Active Directory와 HDInsight를 통합하면 HDInsight 클러스터 노드는 AD 도메인에 가입됩니다. HDInsight는 클러스터에서 실행 중인 Hadoop 서비스에 대한 서비스 주체를 만들고 도메인의 지정된 OU(조직 구성 단위) 내에 배치합니다. 또한 HDInsight는 도메인에 연결되어 있는 노드의 IP 주소에 대해 도메인에 역방향 DNS 매핑을 만듭니다.

Active Directory에 대한 두 가지 배포 옵션은 다음과 같습니다.
* **[Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md):** 이 서비스는 Windows Server Active Directory와 완벽하게 호환되는 관리되는 Active Directory 도메인을 제공합니다. Microsoft에서 AD 도메인 관리, 패치 및 모니터링을 담당합니다. 도메인 컨트롤러를 유지 관리할 걱정 없이 클러스터를 배포할 수 있습니다. 사용자가 회사 자격 증명을 사용하여 클러스터에 로그인할 수 있도록 사용자, 그룹 및 암호가 Azure Active Directory에서 동기화됩니다.

* **Azure IaaS VM의 Windows Server Active Directory 도메인:** 이 옵션에서는 Azure IaaS VM에서 자체 Windows Server Active Directory 도메인을 배포 및 관리합니다. 

이러한 설정은 여러 아키텍처를 사용하여 적용할 수 있습니다. 다음 아키텍처에서 선택할 수 있습니다.


### <a name="hdinsight-integrated-with-an-azure-ad-domain-services-managed-ad-domain"></a>Azure AD Domain Services 관리되는 AD 도메인과 통합된 HDInsight
Azure AD DS([Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md)) 관리되는 도메인을 배포할 수 있습니다. Azure AD DS는 Microsoft에서 관리, 업데이트 및 모니터링하는 관리되는 AD 도메인을 Azure에 제공합니다. 또한 고가용성을 위해 두 개의 도메인 컨트롤러를 만들고 DNS 서비스를 포함합니다. 그런 다음 이 관리되는 도메인과 HDInsight 클러스터를 통합할 수 있습니다. 이 배포 옵션을 사용하면 도메인 컨트롤러 관리, 패치, 업데이트 및 모니터링에 대해 걱정할 필요가 없습니다.

![도메인에 가입된 HDInsight 클러스터 토폴로지](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Azure AD Domain Services와 통합하기 위한 필수 조건:

* [Azure AD Domain Services 관리되는 도메인 프로비전](../active-directory-domain-services/active-directory-ds-getting-started.md)
* [조직 구성 단위](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)를 만들어 HDInsight 클러스터 VM 및 클러스터에 사용되는 서비스 주체를 배치합니다.
* Azure AD DS를 구성할 때 [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)를 설정합니다. LDAPS를 설정하는 데 사용된 인증서는 자체 서명된 인증서가 아닌 공용 인증 기관에서 발급한 인증서여야 합니다.
* HDInsight 서브넷의 IP 주소 범위에 대한 관리되는 도메인에 역방향 DNS 영역을 만듭니다(예: 이전 그림의 10.2.0.0/24).
* Azure AD에서 Azure AD DS 관리되는 도메인까지 [NTLM 및 Kerberos 인증에 필요한 암호 해시 동기화](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)를 구성합니다.
* 서비스 계정 또는 사용자 계정이 필요합니다. 이 계정을 사용하여 HDInsight 클러스터를 만듭니다. 이 계정에는 다음 사용 권한이 있어야 합니다.

    - 조직 구성 단위 내에 서비스 주체 개체 및 컴퓨터 개체를 만들 수 있는 권한
    - 역방향 DNS 프록시 규칙을 만들 수 있는 권한
    - 컴퓨터를 Azure AD 도메인에 가입할 수 있는 권한


### <a name="hdinsight-integrated-with-windows-server-ad-running-on-azure-iaas"></a>Azure IaaS에서 실행되는 Windows Server AD와 통합된 HDInsight

Azure에서 하나 이상의 가상 컴퓨터에 Windows Server Active Directory Domain Services 역할을 배포하고 이러한 VM의 수준을 도메인 컨트롤러로 올릴 수 있습니다. 이 도메인 컨트롤러 VM은 리소스 관리자 배포 모델을 사용하여 HDInsight 클러스터와 동일한 가상 네트워크에 배포될 수 있습니다. 도메인 컨트롤러가 다른 가상 네트워크에 배포된 경우 [VNet 간 피어링](../virtual-network/virtual-network-create-peering.md)을 사용하여 이러한 가상 네트워크를 피어링해야 합니다. 

[추가 정보 - Azure VM에 Windows Server Active Directory 배포](../active-directory/virtual-networks-windows-server-active-directory-virtual-machines.md)

![도메인에 가입된 HDInsight 클러스터 토폴로지](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> 이 아키텍처에서 HDInsight 클러스터와 함께 Azure Data Lake Store를 사용할 수 없습니다.


Azure VM에서 Windows Server Active Directory와 통합하기 위한 필수 조건:

* [조직 구성 단위](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)를 만들어야 하며 여기에 HDInsight 클러스터 VM 및 클러스터에 사용되는 서비스 주체를 배치해야 합니다.
* [LDAP(Lightweight Directory Access Protocol)](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)가 AD와의 통신을 위해 설정되어야 합니다. LDAPS를 설정하는 데 사용된 인증서는 자체 서명된 인증서가 아닌 실제 인증서여야 합니다.
* HDInsight 서브넷의 IP 주소 범위에 대한 도메인에 역방향 DNS 영역을 만들어야 합니다(예: 이전 그림의 10.2.0.0/24).
* 서비스 계정 또는 사용자 계정이 필요합니다. 이 계정을 사용하여 HDInsight 클러스터를 만듭니다. 이 계정에는 다음 사용 권한이 있어야 합니다.

    - 조직 구성 단위 내에 서비스 주체 개체 및 컴퓨터 개체를 만들 수 있는 권한
    - 역방향 DNS 프록시 규칙을 만들 수 있는 권한
    - 컴퓨터를 Active Directory 도메인에 가입할 수 있는 권한


## <a name="next-steps"></a>다음 단계
* 도메인에 가입된 HDInsight 클러스터를 구성하려면 [도메인에 가입된 HDInsight 클러스터 구성](hdinsight-domain-joined-configure.md)을 참조하세요.
* 도메인에 가입된 HDInsight 클러스터를 관리하려면 [도메인에 가입된 HDInsight 클러스터 관리](hdinsight-domain-joined-manage.md)를 참조하세요.
* Hive 정책을 구성하고 Hive 쿼리를 실행하려면 [도메인에 가입된 HDInsight 클러스터에 대한 Hive 정책 구성](hdinsight-domain-joined-run-hive.md)을 참조하세요.
* 도메인에 가입된 HDInsight 클러스터에서 SSH를 사용하여 Hive 쿼리를 실행하려면 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

