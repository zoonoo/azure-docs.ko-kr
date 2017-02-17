---
title: "도메인에 가입된 Azure HDInsight 아키텍처| Microsoft Docs"
description: "도메인에 가입된 HDInsight를 계획하는 방법을 알아봅니다."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: b5cd321f3cd4c415f5e97ca550b7ab1679324921
ms.openlocfilehash: 94ef8147f1f73b293e818fc508096789373df7fc


---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>HDInsight에서 Azure 도메인에 가입된 Hadoop 클러스터 계획

기존 Hadoop는 단일 사용자 클러스터입니다. 빅 데이터 작업을 구축하는 작은 응용 프로그램 팀이 있는 대부분의 기업에 적합합니다. Hadoop의 인기에 따라 대부분의 기업은 클러스터를 IT 팀에서 관리하고 여러 응용 프로그램 팀이 클러스터를 공유하는 모델로 이동하고 있습니다. 따라서 다중 사용자 클러스터는 HDInsight에서 가장 많이 요청되는 기능 중 하나입니다.

고유한 다중 사용자 인증 및 권한 부여를 구축 하는 대신 HDInsight는 가장 인기 있는 ID 공급자 - AD(Active Directory)에 의존합니다. HDInsight에서 다중 사용자 인증을 관리하는 데 Active Directory의 강력한 보안 그룹 기능을 사용할 수 있습니다. Active Directory와 HDInsight를 통합하여 Active Directory 사용자는 Active Directory 자격 증명을 사용하여 클러스터와 통신할 수 있습니다. HDInsight는 HDInsight에서 실행 중인 모든 서비스(Ambari, Hive 서버, Ranger, Spark thrift 서버 등)가 인증된 사용자에 대해 원활하게 작동하도록 로컬 Hadoop 사용자에게 Active Directory 사용자를 매핑합니다.

## <a name="integrate-hdinsight-with-active-directory"></a>Active Directory와 HDInsight 통합

Active Directory와 HDInsight를 통합하여 HDInsight 클러스터 노드는 Active Directory 도메인에 대한 도메인에 가입됩니다. HDInsight는 클러스터에서 실행 중인 Hadoop 서비스에 대한 서비스 주체를 만들고 Active Directory의 지정된 OU(조직 구성 단위) 내에 배치합니다. 또한 HDInsight는 도메인에 가입되어 있는 노드의 IP 주소에 대해 Active Directory 도메인에 역방향 DNS 매핑을 만듭니다.

이 설치를 위해 수행할 수 있는 여러 아키텍처가 있습니다. 더 잘 작동하는 아키텍처를 결정해야 합니다.

**1. Azure IAAS에서 실행되는 AD와 통합된 HDInsight**

HDInsight를 Active Directory와 통합하기 위한 가장 간단한 아키텍처입니다. Active Directory 도메인 컨트롤러는 Azure에서 하나(또는 여러)의 VM(가상 컴퓨터)에서 실행됩니다. 일반적으로 이러한 VM은 가상 네트워크 내에 있습니다. HDInsight 클러스터에 다른 가상 네트워크를 설정합니다. HDInsight가 Active Directory에 대한 시야를 갖도록 하려면 [VNet 간 피어링](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md)을 사용하여 이러한 가상 네트워크를 피어링해야 합니다.

![도메인에 가입된 HDInsight 클러스터 토폴로지](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> 이 아키텍처에서 HDInsight 클러스터와 함께 Azure Data Lake Store를 사용할 수 없습니다.
 

Active Directory에 대한 필수 조건:

* HDInsight 클러스터 VM 및 클러스터에서 사용하는 서비스 주체를 배치하려는 [조직 구성 단위](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)를 만들어야 합니다.
* Active Directory와 통신하기 위해 [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)를 설치해야 합니다. LDAPS를 설치하는 데 사용된 인증서는 자체 서명된 인증서가 아닌 실제 인증서여야 합니다.
* HDInsight 서브넷의 IP 주소 범위에 대한 도메인에 역방향 DNS 영역을 만들어야 합니다(예: 이전 그림의 10.2.0.0/24).
* HDInsight 클러스터를 만드는 데 발급된 서비스 계정 또는 사용자 계정이 필요합니다. 이 계정에는 다음 사용 권한이 있어야 합니다.

    - 조직 구성 단위 내에 서비스 주체 개체 및 컴퓨터 개체를 만들 수 있는 권한
    - 역방향 DNS 프록시 규칙을 만들 수 있는 권한
    - 컴퓨터를 Active Directory 도메인에 조인할 수 있는 권한

**2. 클라우드 전용 Azure AD와 통합된 HDInsight**

클라우드 전용 Azure AD(Azure Active Directory)의 경우 Azure Active Directory와 HDInsight를 통합할 수 있도록 도메인 컨트롤러를 구성해야 합니다. 이 작업은 [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md)(Azure AD DS)를 사용하여 이루어집니다. Azure AD DS는 클라우드에서 도메인 컨트롤러 컴퓨터를 만들고 이에 대한 IP 주소를 제공합니다. 고가용성을 위해 두 개의 도메인 컨트롤러를 만듭니다.

현재 Azure AD DS는 클래식 VNet에만 존재합니다. 클래식 Azure Portal을 사용하여 액세스할 수 있습니다. HDInsight VNet은 VNet 간 피어링을 사용하여 클래식 VNet과 피어링되어야 하는 Azure Portal에 있습니다.

> [!NOTE]
> 클래식 VNet과 Azure Resource Manager VNet 간의 피어링을 위해 두 VNet은 동일한 지역에 있고 동일한 Azure 구독에 있어야 합니다.

![도메인에 가입된 HDInsight 클러스터 토폴로지](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Active Directory에 대한 필수 조건:

* HDInsight 클러스터 VM 및 클러스터에서 사용하는 서비스 주체를 배치하려는 [조직 구성 단위](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)를 만들어야 합니다. 
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)는 AD DS를 구성할 때 설정해야 합니다. LDAPS를 설치하는 데 사용된 인증서는 자체 서명된 인증서가 아닌 실제 인증서여야 합니다.
* HDI 서브넷의 IP 주소 범위에 대한 도메인에 역방향 DNS 영역을 만들어야 합니다(예: 이전 그림의 10.2.0.0/24). 
* [암호 해시](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)는 Azure AD에서 AD DS로 동기화되어야 합니다.
* HDInsight 클러스터를 만드는 데 사용되는 서비스 계정 또는 사용자 계정이 필요합니다. 이 계정에는 다음 사용 권한이 있어야 합니다.

    - 조직 구성 단위 내에 서비스 주체 개체 및 컴퓨터 개체를 만들 수 있는 권한
    - 역방향 DNS 프록시 규칙을 만들 수 있는 권한
    - 컴퓨터를 Active Directory 도메인에 조인할 수 있는 권한

**3. VPN을 통해 온-프레미스 AD와 통합된 HDInsight**

이 아키텍처는 아키텍처 #1과 유사합니다. 유일한 차이점은 Active Directory가 온-프레미스에 있고 Active Directory에 대한 HDInsight의 시야가 [Azure에서 온-프레미스 네트워크로 VPN 연결](../expressroute/expressroute-introduction.md)을 통한다는 점입니다.

![도메인에 가입된 HDInsight 클러스터 토폴로지](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_3.png)

> [!NOTE]
> 이 아키텍처에서 HDInsight 클러스터와 함께 Azure Data Lake Store를 사용할 수 없습니다.

Active Directory에 대한 필수 조건:

* HDInsight 클러스터 VM 및 클러스터에서 사용하는 서비스 주체를 배치하려는 [조직 구성 단위](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)를 만들어야 합니다.
* Active Directory와 통신하기 위해 [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)를 설치해야 합니다. LDAPS를 설치하는 데 사용된 인증서는 자체 서명된 인증서가 아닌 실제 인증서여야 합니다.
* HDI 서브넷의 IP 주소 범위에 대한 도메인에 역방향 DNS 영역을 만들어야 합니다(예: 이전 그림의 10.2.0.0/24).
* HDInsight 클러스터를 만드는 데 사용되는 서비스 계정 또는 사용자 계정이 필요합니다. 이 계정에는 다음 사용 권한이 있어야 합니다.

    - 조직 구성 단위 내에 서비스 주체 개체 및 컴퓨터 개체를 만들 수 있는 권한
    - 역방향 DNS 프록시 규칙을 만들 수 있는 권한
    - 컴퓨터를 Active Directory 도메인에 조인할 수 있는 권한

**4. Azure AD로 동기화된 온-프레미스 AD와 통합된 HDInsight**

이 아키텍처는 아키텍처 #2와 유사합니다. 유일한 차이점은 온-프레미스 Active Directory가 Azure Active Directory로 동기화된다는 점입니다. Azure Active Directory와 HDInsight를 통합할 수 있도록 클라우드에 도메인 컨트롤러를 구성해야 합니다. 이 작업은 [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md)(AD DS)를 사용하여 이루어집니다. AD DS는 클라우드에서 도메인 컨트롤러 컴퓨터를 만들고 이에 대한 IP 주소를 제공합니다. 고가용성을 위해 두 개의 도메인 컨트롤러를 만듭니다.

현재 Azure AD DS는 클래식 VNet에만 존재합니다. 클래식 Azure Portal을 사용하여 액세스할 수 있습니다. HDInsight VNet은 VNet 간 피어링을 사용하여 클래식 VNet과 피어링되어야 하는 Azure Portal에 있습니다.

> [!NOTE]
> 클래식 VNet과 Azure Resource Manager VNet 간의 피어링을 위해 두 VNet은 동일한 지역에 있고 동일한 Azure 구독에 있어야 합니다.

![도메인에 가입된 HDInsight 클러스터 토폴로지](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Active Directory에 대한 필수 조건:

* HDInsight 클러스터 VM 및 클러스터에서 사용하는 서비스 주체를 배치하려는 [조직 구성 단위](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)를 만들어야 합니다. 
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)는 AD DS를 구성할 때 설정해야 합니다. LDAPS를 설치하는 데 사용된 인증서는 자체 서명된 인증서가 아닌 실제 인증서여야 합니다.
* HDI 서브넷의 IP 주소 범위에 대한 도메인에 역방향 DNS 영역을 만들어야 합니다(예: 이전 그림의 10.2.0.0/24). 
* [암호 해시](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)는 Azure AD에서 AD DS로 동기화되어야 합니다.
* HDInsight 클러스터를 만드는 데 사용되는 서비스 계정 또는 사용자 계정이 필요합니다. 이 계정에는 다음 사용 권한이 있어야 합니다.

    - 조직 구성 단위 내에 서비스 주체 개체 및 컴퓨터 개체를 만들 수 있는 권한
    - 역방향 DNS 프록시 규칙을 만들 수 있는 권한
    - 컴퓨터를 Active Directory 도메인에 조인할 수 있는 권한

**5. 기본이 아닌 Azure AD와 통합된 HDInsight(테스트 및 개발용으로만 권장)**

이 아키텍처는 아키텍처 #2와 유사합니다. 대부분의 회사의 경우 Active Directory에 대한 관리자 액세스는 특정 개인으로만 제한됩니다. 따라서 개념 증명을 수행하거나 관리자가 Active Directory에 대한 필수 구성 요소를 구성하기를 기다리는 대신 도메인에 가입된 클러스터를 만들려고 시도하려는 경우 구독에 새 Azure Active Directory를 만드는 것이 도움이 될 수 있습니다. 이는 사용자가 만든 Azure AD이므로 AD DS를 구성할 수 있는 이 Azure AD에 대한 모든 권한이 있습니다.

AD DS는 클라우드에서 도메인 컨트롤러 컴퓨터를 만들고 이에 대한 IP 주소를 제공합니다. 고가용성을 위해 두 개의 도메인 컨트롤러를 만듭니다.

현재 AD DS는 클래식 VNet에만 있으므로 클래식 포털에 대한 액세스가 필요하고 AD DS 구성을 위한 클래식 VNet을 만들어야 합니다. HDInsight VNet은 VNet 간 피어링을 사용하여 클래식 VNet과 피어링되어야 하는 Azure Portal에 있습니다.

> [!NOTE]
> 클래식 VNet과 Azure Resource Manager VNet 간의 피어링을 위해 두 VNet은 동일한 지역에 있고 동일한 Azure 구독에 있어야 합니다.

![도메인에 가입된 HDInsight 클러스터 토폴로지](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Active Directory에 대한 필수 조건:

* HDInsight 클러스터 VM 및 클러스터에서 사용하는 서비스 주체를 배치하려는 [조직 구성 단위](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)를 만들어야 합니다. 
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)는 AD DS를 구성할 때 설정해야 합니다. LDAPS를 구성하도록 [자체 서명된 인증서](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)를 만들 수 있습니다. 그러나 자체 서명된 인증서를 사용하려면 <a href="mailto:hdipreview@microsoft.com">hdipreview@microsoft.com</a>에서 예외를 요청해야 합니다.
* HDI 서브넷의 IP 주소 범위에 대한 도메인에 역방향 DNS 영역을 만들어야 합니다(예: 이전 그림의 10.2.0.0/24). 
* [암호 해시](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)는 Azure AD에서 AD DS로 동기화되어야 합니다.
* HDInsight 클러스터를 만드는 데 사용되는 서비스 계정 또는 사용자 계정이 필요합니다. 이 계정에는 다음 사용 권한이 있어야 합니다.

    - 조직 구성 단위 내에 서비스 주체 개체 및 컴퓨터 개체를 만들 수 있는 권한
    - 역방향 DNS 프록시 규칙을 만들 수 있는 권한
    - 컴퓨터를 Active Directory 도메인에 조인할 수 있는 권한

## <a name="next-steps"></a>다음 단계
* 도메인에 가입된 HDInsight 클러스터 구성에 대한 자세한 내용은 [도메인에 가입된 HDInsight 클러스터 구성](hdinsight-domain-joined-configure.md)을 참조하세요.
* 도메인에 가입된 HDInsight 클러스터 관리에 대한 자세한 내용은 [도메인에 가입된 HDInsight 클러스터 관리](hdinsight-domain-joined-manage.md)를 참조하세요.
* Hive 정책 및 Hive 쿼리 실행에 대한 자세한 내용은 [도메인에 가입된 HDInsight 클러스터에 대한 Hive 정책 구성](hdinsight-domain-joined-run-hive.md)을 참조하세요.
* 도메인에 가입된 HDInsight 클러스터에서 SSH를 사용하여 Hive 쿼리를 실행하는 방법에 대한 자세한 내용은 [Linux, Unix 또는 OS X에서 SSH를 HDInsight의 Linux 기반 Hadoop과 함께 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.




<!--HONumber=Feb17_HO1-->


