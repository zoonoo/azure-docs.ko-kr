---
title: Azure AD-DS를 사용하여 Enterprise Security Package로 HDInsight 클러스터 구성
description: Azure Active Directory Domain Services를 사용하여 HDInsight Enterprise Security Package 클러스터를 설정 및 구성하는 방법을 알아봅니다.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a5b377381fd540c2a9f1d85e0cb7edce32c2dae8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968376"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services를 사용하여 Enterprise Security Package로 HDInsight 클러스터 구성

ESP(Enterprise Security Package) 클러스터는 Azure HDInsight 클러스터에서 다중 사용자 액세스를 제공합니다. ESP를 사용하는 HDInsight 클러스터는 도메인에 연결되기 때문에 도메인 사용자는 자신의 도메인 자격 증명을 사용하여 클러스터를 인증하고 빅 데이터 작업을 실행할 수 있습니다. 

이 문서에서는 Azure AD DS(Azure Active Directory Domain Services)를 사용하여 ESP로 HDInsight 클러스터를 구성하는 방법을 알아봅니다.

>[!NOTE]
>ESP는 Spark, Interactive 및 Hadoop용 HDI 3.6+에서 사용할 수 있습니다. HBase 클러스터 유형에 대한 ESP는 미리 보기입니다.


## <a name="enable-azure-ad-ds"></a>Azure AD-DS 사용

ESP로 HDInsight 클러스터를 만들려면 먼저 Azure AD-DS를 사용하도록 설정해야 합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화](../../active-directory-domain-services/active-directory-ds-getting-started.md)를 참조하세요. 

> [!NOTE]
> 테넌트 관리자만 Azure AD-DS 인스턴스를 만들 수 있는 권한이 있습니다. HDInsight에 대한 기본 저장소로 Azure Data Lake Storage Gen1을 사용하는 경우, Data Lake Storage Gen1의 기본 Azure AD 테넌트가 HDInsight 클러스터의 도메인과 동일한지 확인합니다. Hadoop은 Kerberos 및 기본 인증을 사용하므로 클러스터에 액세스할 사용자에 대해 다단계 인증을 사용하지 않도록 설정해야 합니다.

보안 LDAP는 Azure AD-DS 관리되는 도메인에 사용됩니다. LDAPS를 사용하도록 설정할 때, 인증서의 주체 이름 또는 대체 주체 이름에 도메인 이름을 입력합니다. 자세한 내용은 [Azure AD-DS 관리되는 도메인에 대해 보안 LDAP 구성](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)을 참조하세요.

## <a name="add-managed-identity"></a>관리 ID 추가

Azure AD-DS를 사용하도록 설정한 후에는 관리 ID를 만들고 Azure AD-DS 액세스 제어의 **HDInsight 도메인 서비스 참가자** 역할에 할당합니다.

![Azure Active Directory Domain Services 액세스 제어](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

## <a name="create-a-hdinsight-cluster-with-esp"></a>ESP로 HDInsight 클러스터 만들기

다음 단계는 Azure AD-DS를 사용하여 ESP로 사용하여 HDInsight 클러스터를 만드는 것입니다.

Azure AD-DS 인스턴스와 HDInsight 클러스터를 동일한 Azure Virtual Network에 배치하는 것이 더 쉽습니다. 다른 가상 네트워크에 배치하도록 선택하는 경우, HDInsight VM이 VM을 조인하기 위해 도메인 컨트롤러에 연결되도록 해당 가상 네트워크를 피어링해야 합니다. 자세한 내용은 [가상 네트워크 피어링](../../virtual-network/virtual-network-peering-overview.md)을 참조하세요.

HDInsight 클러스터를 만들 때는 Enterprise Security Package를 사용하여 클러스터를 Azure AD-DS와 연결할 수 있습니다. 

![Azure HDInsight 보안 및 네트워킹](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

ESP를 사용하도록 설정하면 Azure AD-DS와 관련된 일반적인 구성 오류가 자동으로 검색되고 유효성이 검사됩니다.

![Azure HDInsight Enterprise Security Package 도메인 유효성 검사](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

초기 검색은 클러스터를 만들기 전에 오류를 수정할 수 있도록 하여 시간 절약 효과를 가져옵니다.

![Azure HDInsight Enterprise Security Package의 도메인 유효성 검사 실패](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

ESP로 HDInsight 클러스터를 만드는 경우 다음 매개 변수를 제공해야 합니다.

- **클러스터 관리 사용자**: 동기화된 Azure AD-DS에서 클러스터에 대한 관리자를 선택합니다.

- **클러스터 액세스 그룹**: 해당 사용자를 클러스터와 동기화하려는 보안 그룹이 Azure AD-DS에서 동기화되고 사용 가능해야 합니다. 예: HiveUsers. 여러 사용자 그룹을 지정하려면 세미콜론 ‘;’으로 구분합니다. 그룹은 프로비전하기 전에 디렉터리에 있어야 합니다. 자세한 내용은 [Azure Active Directory에서 그룹 만들기 및 멤버 추가](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요. 그룹이 존재하지 않는 경우 오류: "그룹 HiveUsers를 Active Directory에서 찾을 수 없음"이 발생합니다.

- **LDAPS URL**: 예를 들어 ldaps://contoso.onmicrosoft.com:636입니다.

    > [!IMPORTANT]
    > “ldaps://” 및 포트 번호(:636)를 포함한 전체 URL을 입력합니다.

다음 스크린샷에는 Azure Portal의 구성이 나와 있습니다.

   ![Azure HDInsight ESP Active Directory Domain Services 구성](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>다음 단계
* Hive 정책 구성 및 Hive 쿼리 실행에 대한 내용은 [ESP가 포함된 HDInsight 클러스터에 대한 Hive 정책 구성](apache-domain-joined-run-hive.md)을 참조하세요.
* SSH를 사용하여 ESP로 HDInsight 클러스터에 연결하는 방법은 [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)을 참조하세요.

