---
title: Azure AD DS를 사용하여 도메인에 가입된 HDInsight 클러스터 구성
description: Azure Active Directory Domain Services를 사용하여 도메인에 가입된 HDInsight 클러스터를 설정 및 구성하는 방법을 알아봅니다.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
editor: jasonwhowell
ms.topic: conceptual
ms.date: 07/17/2018
ms.openlocfilehash: 0d44812c92fd14bf87aac9a942241f8de55f2eec
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590589"
---
# <a name="configure-a-domain-joined-hdinsight-cluster-by-using-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services를 사용하여 도메인에 가입된 HDInsight 클러스터 구성

도메인에 가입된 클러스터는 Azure HDInsight 클러스터에서 다중 사용자 액세스 기능을 제공합니다. 도메인에 가입된 HDInsight 클러스터는 도메인에 연결되기 때문에 도메인 사용자는 자신의 도메인 자격 증명을 사용하여 클러스터를 인증하고 빅 데이터 작업을 실행할 수 있습니다. 

이 문서에서는 Azure AD DS(Azure Active Directory Domain Services)를 사용하여 도메인에 가입된 HDInsight 클러스터를 구성하는 방법을 알아봅니다.

## <a name="enable-azure-ad-ds"></a>Azure AD DS 사용

도메인에 가입된 HDInsight 클러스터를 만들려면 먼저 Azure AD DS를 사용하도록 설정해야 합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화](../../active-directory-domain-services/active-directory-ds-getting-started.md)를 참조하세요. 

> [!NOTE]
> 테넌트 관리자만 Azure AD DS 인스턴스를 만들 수 있는 권한이 있습니다. HDInsight에 대한 기본 저장소로 Azure Data Lake Storage Gen1을 사용하는 경우, Data Lake Storage Gen1의 기본 Azure AD 테넌트가 HDInsight 클러스터의 도메인과 동일한지 확인합니다. Hadoop은 Kerberos 및 기본 인증을 사용하므로 클러스터에 액세스할 사용자에 대해 다단계 인증을 사용하지 않도록 설정해야 합니다.

Azure AD DS 인스턴스를 프로비전한 후 Azure AD(Azure Active Directory)에서 올바른 권한이 있는 서비스 계정을 만듭니다. 이 서비스 계정이 이미 있는 경우, 암호를 재설정하고 Azure AD DS와 동기화될 때까지 기다립니다. 이렇게 재설정하면 Kerberos 암호 해시가 생성되며, Azure AD DS와 동기화되는 데 최대 30분이 걸릴 수 있습니다. 

서비스 계정에는 다음 권한이 필요합니다.

- 컴퓨터를 도메인에 가입하고 컴퓨터 보안 주체를 클러스터를 만드는 동안 지정하는 OU 내에 배치합니다.
- 클러스터를 만드는 동안 지정하는 OU 내에 서비스 사용자를 만듭니다.

> [!NOTE]
> Apache Zeppelin은 도메인 이름을 사용하여 관리 서비스 계정을 인증하기 때문에 제대로 작동하려면 서비스 계정의 도메인 이름이 Apache Zeppelin의 UPN 접미사와 *반드시* 동일해야 합니다.

OU 및 OU 관리 방법에 대한 자세한 내용은 [Azure AD DS 관리되는 도메인에서 OU 만들기](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)를 참조하세요. 

보안 LDAP는 Azure AD DS 관리되는 도메인에 사용됩니다. 자세한 내용은 [Azure AD DS 관리되는 도메인에 대해 보안 LDAP 구성](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)을 참조하세요.

## <a name="create-a-domain-joined-hdinsight-cluster"></a>도메인에 가입된 HDInsight 클러스터 만들기

다음 단계는 이전 섹션에서 만든 서비스 계정과 Azure AD DS를 사용하여 HDInsight 클러스터를 만드는 것입니다.

Azure AD DS 인스턴스와 HDInsight 클러스터를 동일한 Azure 가상 네트워크에 배치하는 것이 더 쉽습니다. 다른 가상 네트워크에 배치하도록 선택하는 경우, HDInsight VM이 VM을 조인하기 위해 도메인 컨트롤러에 연결되도록 해당 가상 네트워크를 피어링해야 합니다. 자세한 내용은 [가상 네트워크 피어링](../../virtual-network/virtual-network-peering-overview.md)을 참조하세요.

도메인에 가입된 HDInsight 클러스터를 만드는 경우 다음 매개 변수를 제공해야 합니다.

- **도메인 이름**: Azure AD DS와 연결된 도메인 이름입니다. 예를 들어, contoso.onmicrosoft.com입니다.

- **도메인 사용자 이름**: 이전 섹션에서 만든 Azure ADDS DC 관리되는 도메인의 서비스 계정입니다. 예는 hdiadmin@contoso.onmicrosoft.com입니다. 이 도메인 사용자는 이 HDInsight 클러스터의 관리자가 됩니다.

- **도메인 암호**: 서비스 계정의 암호입니다.

- **조직 구성 단위**: HDInsight 클러스터에 사용하려는 OU의 고유 이름입니다. 예를 들어, OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com입니다. 이 OU가 없는 경우, HDInsight 클러스터는 서비스 계정에 있는 권한을 사용하여 OU를 만들려고 합니다. 예를 들어, 서비스 계정이 Azure AD DS 관리자 그룹에 있는 경우, OU를 만들 수 있는 권한이 있습니다. 관리자 그룹에 없는 경우, 먼저 OU를 만든 다음, 해당 OU에 대한 모든 권한을 서비스 계정에 제공해야 합니다. 자세한 내용은 [Azure AD DS 관리되는 도메인에 OU 만들기](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)를 참조하세요.

    > [!IMPORTANT]
    > OU 뒤에 모든 DC를 쉼표로 구분하여 포함합니다(예: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com).

- **LDAPS URL**: 예를 들어 ldaps://contoso.onmicrosoft.com:636입니다.

    > [!IMPORTANT]
    > “ldaps://” 및 포트 번호(:636)를 포함한 전체 URL을 입력합니다.

- **액세스 사용자 그룹**: 클러스터에 동기화할 사용자가 속하는 보안 그룹입니다. 예: HiveUsers. 여러 사용자 그룹을 지정하려면 세미콜론 ‘;’으로 구분합니다. 그룹은 프로비전하기 전에 디렉터리에 있어야 합니다. 자세한 내용은 [Azure Active Directory에서 그룹 만들기 및 멤버 추가](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요. 그룹이 존재하지 않는 경우 오류: "그룹 HiveUsers를 Active Directory에서 찾을 수 없음"이 발생합니다.

다음 스크린샷에는 Azure Portal의 구성이 나와 있습니다.

   ![Azure HDInsight 도메인에 가입된 Active Directory Domain Services 구성](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>다음 단계
* Hive 정책을 구성하고 Hive 쿼리를 실행하는 방법은 [도메인에 가입된 HDInsight 클러스터에 대한 Hive 정책 구성](apache-domain-joined-run-hive.md)을 참조하세요.
* SSH를 사용하여 도메인에 가입된 HDInsight 클러스터에 연결하는 방법은 [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)을 참조하세요.

