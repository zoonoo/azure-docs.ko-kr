---
title: AAD-DS를 사용하여 도메인에 가입된 HDInsight 클러스터 구성
description: Azure Active Directory Domain Services를 사용하여 도메인에 가입된 HDInsight 클러스터를 설정 및 구성하는 방법을 알아봅니다.
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 8064940e0d0f035010a2521752d6f32f3f9ccd9f
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849835"
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services를 사용하여 도메인에 가입된 HDInsight 클러스터 구성

도메인에 가입된 클러스터는 HDInsight 클러스터에서 다중 사용자 액세스 기능을 제공합니다. 도메인에 가입된 HDInsight 클러스터는 도메인에 연결되기 때문에 도메인 사용자는 자신의 도메인 자격 증명을 사용하여 클러스터를 인증하고 빅 데이터 작업을 실행할 수 있습니다. 

이 문서에서는 Azure Active Directory Domain Services를 사용하여 도메인에 가입된 HDInsight 클러스터를 구성하는 방법을 알아봅니다.

## <a name="create-azure-adds"></a>Azure ADDS 만들기

도메인에 가입된 HDInsight 클러스터를 만들려면 먼저 AAD-DS(Azure AD Domain Services)를 사용하도록 설정해야 합니다. AAD-DS 인스턴스를 사용하도록 설정하려면 [Azure Portal을 사용하여 AAD-DS를 사용하도록 설정하는 방법](../../active-directory-domain-services/active-directory-ds-getting-started.md)을 참조하세요. 

> [!NOTE]
> AAD-DS 인스턴스를 만들 권한은 테넌트 관리자에게만 있습니다. HDInsight에 대한 기본 저장소로 ADLS(Azure Data Lake Storage)를 사용하는 경우 ADLS에 대한 기본 Azure AD 테넌트가 HDInsight 클러스터에 대한 도메인과 동일한지 확인합니다. Hadoop은 Kerberos 및 기본 인증을 사용하므로 클러스터에 액세스할 수 있는 사용자에 대해 다단계 인증을 사용하지 않도록 설정해야 합니다.

AAD-DS를 프로비전한 후에는 적절한 사용 권한으로 AAD(AAD-DS로 동기화됨)에 서비스 계정을 만들어야 합니다. 이 서비스 계정이 이미 있는 경우 해당 암호를 다시 설정하고 AAD-DS로 동기화될 때까지 기다려야 합니다(이 다시 설정은 kerberos 암호 해시를 생성하고 AAD-DS로 동기화될 때까지 최대 30분이 걸릴 수 있음). 이 서비스 계정에는 다음 권한이 있어야 합니다.

- 컴퓨터를 도메인에 가입하고 컴퓨터 보안 주체를 클러스터를 만드는 동안 지정하는 OU 내에 배치합니다.
- 클러스터를 만드는 동안 지정하는 OU 내에 서비스 사용자를 만듭니다.

> [!NOTE]
> Apache Zeppelin은 도메인 이름을 사용하여 관리 서비스 계정을 인증하기 때문에 제대로 작동하려면 서비스 계정의 도메인 이름이 Apache Zeppelin의 UPN 접미사와 반드시 동일해야 합니다.

OU 및 관리 방법을 자세히 알아보려면 [AAD-DS에서 OU 만들기](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)를 참조하세요. 

AAD-DS 관리되는 도메인에 대한 보안 LDAP가 필요합니다. 보안 LDAP를 사용하도록 설정하려면 [AAD-DS 관리되는 도메인에 대해 보안 LDAP(LDAPS) 구성](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)을 참조하세요.

## <a name="create-a-domain-joined-hdinsight-cluster"></a>도메인에 가입된 HDInsight 클러스터 만들기

다음 단계는 이전 섹션에서 만든 AAD-DS와 서비스 계정을 사용하여 HDInsight 클러스터를 만드는 것입니다.

AAD-DS와 HDInsight 클러스터를 동일한 Azure VNet(가상 네트워크)에 배치하는 것이 더 쉽습니다. 다른 VNet에 배치하려는 경우 HDI VM이 VM을 조인하기 위해 도메인 컨트롤러에 연결되도록 해당 VNet을 피어링해야 합니다. 자세한 내용은 [가상 네트워크 피어링](../../virtual-network/virtual-network-peering-overview.md)을 참조하세요.

도메인에 가입된 HDInsight 클러스터를 만드는 경우 다음 매개 변수를 제공해야 합니다.

- **도메인 이름**: AAD-DS와 연결된 도메인 이름입니다. 예: contoso.onmicrosoft.com.
- **도메인 사용자 이름**: 이전 섹션에서 만든 관리되는 도메인의 서비스 계정입니다. 예: hdiadmin@contoso.onmicrosoft.com 이 도메인 사용자는 이 HDInsight 클러스터의 관리자가 됩니다.
- **도메인 암호**: 서비스 계정의 암호입니다.
- **조직 구성 단위**: HDInsight 클러스터에 사용하려는 OU의 고유한 이름입니다. 예: OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com. 이 OU가 없는 경우 HDInsight 클러스터는 서비스 계정에 있는 권한을 사용하여 OU를 만들려고 시도합니다. (예를 들어 서비스 계정이 AAD-DS 관리자 그룹에 있는 경우에는 OU를 만들 수 있는 올바른 권한을 갖습니다. 그렇지 않은 경우에는 먼저 OU를 만들고 서비스 계정에 OU에 대한 전체 제어 권한을 부여해야 합니다. [AAD-DS에서 OU 만들기](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) 참조).

    > [!IMPORTANT]
    > OU 뒤에 쉼표로 구분된 모든 DC를 포함하는 것이 중요합니다(예: OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com).

- **LDAPS URL**: 예, ldaps://contoso.onmicrosoft.com:636

    > [!IMPORTANT]
    > ldaps:// 및 포트 번호(:636)를 포함한 전체 URL을 입력합니다.

- **액세스 사용자 그룹**: 클러스터에 동기화할 사용자가 속하는 보안 그룹입니다. 예: HiveUsers. 여러 사용자 그룹을 지정하려면 쉼표 ','로 구분하십시오.
 
다음 스크린샷에는 Azure Portal의 구성이 나와 있습니다.

![Azure HDInsight 도메인에 가입된 Active Directory Domain Services 구성](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png)에서도 확인할 수 있습니다.


## <a name="next-steps"></a>다음 단계
* Hive 정책 및 Hive 쿼리 실행에 대한 자세한 내용은 [도메인에 가입된 HDInsight 클러스터에 대한 Hive 정책 구성](apache-domain-joined-run-hive.md)을 참조하세요.
* SSH를 사용하여 도메인 가입 HDInsight 클러스터에 연결하려면 [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)을 참조하세요.

