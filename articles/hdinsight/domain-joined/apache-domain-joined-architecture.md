---
title: Enterprise Security Package를 사용하는 Azure HDInsight 아키텍처
description: Enterprise Security Package를 사용하여 HDInsight 보안을 계획하는 방법을 알아봅니다.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 975a4f7b15d1e1c13767cd7026e961e9d4227603
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998931"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>HDInsight에서 Enterprise Security Package 사용

표준 Azure HDInsight 클러스터는 단일 사용자 클러스터입니다. 대량 데이터 워크로드를 구축하는 작은 응용 프로그램 팀이 있는 대부분의 회사에 적합합니다. 각 사용자는 요청 시 전용 클러스터를 만들고 더 이상 필요하지 않은 경우, 삭제할 수 있습니다. 

대부분의 기업이 클러스터를 IT 팀에서 관리하고 여러 응용 프로그램 팀이 클러스터를 공유하는 모델로 이동했습니다. 이러한 대기업은 Azure HDInsight의 각 클러스터에 대한 다중 사용자 액세스 권한이 필요합니다.

HDInsight는 널리 사용되는 ID 공급자인 Active Directory를 관리되는 방식으로 사용합니다. [Azure AD DS(Azure Active Directory Domain Services)](../../active-directory-domain-services/active-directory-ds-overview.md)와 HDInsight를 통합하고 도메인 자격 증명을 사용하여 클러스터에 액세스할 수 있습니다. 

HDInsight의 VM(가상 머신)은 제공된 도메인에 가입된 도메인입니다. 따라서 HDInsight에서 실행 중인 모든 서비스(Ambari, Hive 서버, Ranger, Spark thrift 서버 등)가 인증된 사용자에 원활하게 작동합니다. 관리자는 Apache Ranger를 사용하여 클러스터의 리소스에 대한 역할 기반 액세스 제어를 제공하는 강력한 권한 부여 정책을 만들 수 있습니다.


## <a name="integrate-hdinsight-with-active-directory"></a>Active Directory와 HDInsight 통합

오픈 소스 Hadoop은 인증 및 보안에 Kerberos를 사용합니다. 따라서 ESP(Enterprise Security Package)가 포함된 HDInsight 클러스터 노드는 Azure AD DS에서 관리하는 도메인에 조인됩니다. Kerberos 보안은 클러스터의 Hadoop 구성 요소에 대해 구성됩니다. 

각 Hadoop 구성 요소의 경우, 서비스 주체는 자동으로 생성됩니다. 또한 도메인에 가입된 각 머신에 대한 해당 머신 보안 주체가 만들어집니다. 이러한 서비스 및 머신 주체를 저장하기 위해 이러한 원칙을 배치할 위치인 도메인 컨트롤러(Azure AD DS) 내에서 OU(조직 구성 단위)를 제공해야 합니다. 

요약하면 다음으로 환경을 설정해야 합니다.

- Active Directory 도메인(Azure AD DS에서 관리함)
- Azure AD DS에서 사용하도록 설정된 보안 LDAP(LDAPS)
- 가상 네트워크를 별도로 선택하는 경우, HDInsight 가상 네트워크에서 Azure AD DS 가상 네트워크로 적절한 네트워킹 연결 HDInsight 가상 네트워크 내의 VM은 가상 네트워크 피어링을 통해 Azure AD DS에 연결되어 있어야 합니다. HDInsight 및 Azure AD DS가 동일한 가상 네트워크에 배포된 경우, 연결이 자동으로 제공되며 추가 작업이 필요하지 않습니다.
- [Azure AD DS에서 생성된](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) OU
- 다음에 대한 사용 권한을 가진 서비스 계정:
    - OU에 서비스 사용자를 만듭니다.
    - 컴퓨터를 도메인에 가입하고 OU에 컴퓨터 보안 주체를 만듭니다.

다음 스크린샷은 contoso.com에서 만든 OU를 보여 줍니다. 또한 일부 서비스 사용자 및 머신 보안 주체를 표시합니다.

![ESP가 포함된 HDInsight 클러스터용 조직 구성 단위](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

## <a name="set-up-different-domain-controllers"></a>다른 도메인 컨트롤러 설정
HDInsight는 현재 클러스터가 Kerberos 통신에 사용하는 주 도메인 컨트롤러로 Azure AD DS만 지원합니다. 그러나 이러한 설정을 통해 HDInsight 액세스에 Azure AD DS를 사용하도록 설정하면 다른 복잡한 Active Directory 설정이 가능합니다.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services
[Azure AD DS](../../active-directory-domain-services/active-directory-ds-overview.md)는 관리되는 도메인을 제공합니다. 이 도메인은 Windows Server Active Directory와 완벽하게 호환됩니다. Microsoft는 HA(고가용성) 설정에서 도메인을 관리하고, 패치하고, 모니터링하는 작업에 주의를 기울입니다. 도메인 컨트롤러를 유지 관리할 걱정 없이 클러스터를 배포할 수 있습니다. 

사용자, 그룹 및 암호는 Azure AD(Azure Active Directory)에서 동기화됩니다. Azure AD 인스턴스에서 Azure AD DS로 단방향 동기화를 사용하면 사용자가 동일한 회사 자격 증명을 통해 클러스터에 로그인할 수 있습니다. 

자세한 내용은 [Azure AD DS를 사용하여 ESP가 포함된 HDInsight 클러스터 구성](./apache-domain-joined-configure-using-azure-adds.md)을 참조하세요.

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>온-프레미스 Active Directory 또는 IaaS VM의 Active Directory

도메인에 대한 온-프레미스 Active Directory 인스턴스 또는 더 복잡한 Active Directory 설정이 있는 경우, Azure AD Connect를 사용하여 해당 ID를 Azure AD에 동기화할 수 있습니다. 그런 다음, 해당 Active Directory 테넌트에서 Azure AD DS를 사용하도록 설정할 수 있습니다. 

Kerberos가 암호 해시를 사용하므로 [Azure AD DS에서 암호 해시 동기화를 사용하도록 설정](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)해야 합니다. AD FS(Active Directory Federation Services)와 페더레이션을 사용하는 경우, 필요에 따라 AD FS 인프라에 장애가 발생할 경우 백업으로 암호 해시 동기화를 설정할 수 있습니다. 자세한 내용은 [Azure AD Connect에서 암호 해시 동기화 사용](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)을 참조하세요. 

Azure AD 및 Azure AD DS 없이 온-프레미스 Active Directory 또는 IaaS VM의 Active Directory만 사용하는 방식은 ESP가 포함된 HDInsight 클러스터에 대해 지원되는 구성이 아닙니다.

## <a name="next-steps"></a>다음 단계

* [ESP가 포함된 HDInsight 클러스터 구성](apache-domain-joined-configure-using-azure-adds.md)
* [ESP가 포함된 HDInsight 클러스터용 Hive 정책 구성](apache-domain-joined-run-hive.md)
* [ESP가 포함된 HDInsight 클러스터 관리](apache-domain-joined-manage.md) 
