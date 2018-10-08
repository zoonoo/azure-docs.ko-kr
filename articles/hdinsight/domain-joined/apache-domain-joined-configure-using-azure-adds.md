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
ms.openlocfilehash: eb24aa0471604696de99f4878baef764cfef0a8b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408357"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services를 사용하여 Enterprise Security Package로 HDInsight 클러스터 구성

ESP(Enterprise Security Package) 클러스터는 Azure HDInsight 클러스터에서 다중 사용자 액세스를 제공합니다. ESP를 사용하는 HDInsight 클러스터는 도메인에 연결되기 때문에 도메인 사용자는 자신의 도메인 자격 증명을 사용하여 클러스터를 인증하고 빅 데이터 작업을 실행할 수 있습니다. 

이 문서에서는 Azure AD DS(Azure Active Directory Domain Services)를 사용하여 ESP로 HDInsight 클러스터를 구성하는 방법을 알아봅니다.

>[!NOTE]
>ESP는 Spark, Interactive 및 Hadoop용 HDI 3.6에서 GA(일반적으로 사용할 수) 있습니다. HBase 및 Kafka 클러스터 유형에 대한 ESP는 미리 보기입니다.

## <a name="enable-azure-ad-ds"></a>Azure AD-DS 사용

ESP로 HDInsight 클러스터를 만들려면 먼저 Azure AD-DS를 사용하도록 설정해야 합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화](../../active-directory-domain-services/active-directory-ds-getting-started.md)를 참조하세요. 

Azure AD-DS를 사용하도록 설정하는 경우 모든 사용자 및 개체는 기본적으로 AAD(Azure Active Directory)에서 Azure AD-DS로 동기화를 시작합니다. 동기화 작업의 길이는 AAD의 개체 수에 따라 달라집니다. 수십만 개의 개체에 대한 동기화는 며칠이 걸릴 수 있습니다. 

고객은 HDInsight 클러스터에 대한 액세스 권한이 필요한 그룹만 동기화하도록 선택할 수 있습니다. 특정 그룹만 동기화하는 이 옵션은 *범위가 지정된 동기화*라고 합니다. 지침은 [Azure AD부터 관리 도메인까지로 범위가 지정된 동기화 구성](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization)을 참조하세요.

> [!NOTE]
> 테넌트 관리자만 Azure AD-DS 인스턴스를 만들 수 있는 권한이 있습니다. MFA(다단계 인증)는 클러스터에 액세스할 사용자에 대해서만 사용할 수 없도록 설정해야 합니다.

보안 LDAP를 사용하도록 설정할 경우 인증서의 주체 이름 또는 대체 주체 이름에 도메인 이름을 입력합니다. 예를 들어 도메인 이름이 *contoso.com*인 경우 인증서 주체 이름 또는 대체 주체 이름에 정확한 이름이 있어야 합니다. 자세한 내용은 [Azure AD-DS 관리되는 도메인에 대해 보안 LDAP 구성](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)을 참조하세요.

## <a name="check-aad-ds-health-status"></a>AAD-DS 상태 확인

**관리** 범주에서 **상태**를 선택하여 Azure Active Directory Domain Services의 상태를 확인합니다. AAD-DS 상태가 녹색(실행)인지, 동기화가 완료됐는지 확인합니다.

![Azure Active Directory Domain Services 상태](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="add-managed-identity"></a>관리 ID 추가

Azure AD-DS를 사용하도록 설정한 후에는 사용자 할당 관리 ID를 만들어 Azure AD-DS 액세스 제어의 **HDInsight 도메인 서비스 기여자** 역할에 할당합니다.

![Azure Active Directory Domain Services 액세스 제어](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

**HDInsight 도메인 서비스 기여자** 역할에 관리 ID를 할당하면 해당 ID에 AAD-DS 도메인에서 특정 도메인 서비스 작업을 수행하기에 적합한 권한이 있는지 확인할 수 있습니다. 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

## <a name="create-a-hdinsight-cluster-with-esp"></a>ESP로 HDInsight 클러스터 만들기

다음 단계는 Azure AD-DS를 사용하여 ESP로 사용하여 HDInsight 클러스터를 만드는 것입니다.

Azure AD-DS 인스턴스와 HDInsight 클러스터를 동일한 Azure Virtual Network에 배치하는 것이 더 쉽습니다. 다른 가상 네트워크에 배치하도록 선택하는 경우, HDInsight VM이 VM을 조인하기 위해 도메인 컨트롤러에 연결되도록 해당 가상 네트워크를 피어링해야 합니다. 자세한 내용은 [가상 네트워크 피어링](../../virtual-network/virtual-network-peering-overview.md)을 참조하세요. 피어링이 올바로 완료됐는지 테스트하려면 HDInsight VNET/Subnet에 VM을 조인하고, 도메인 이름을 ping하거나 **ldp.exe**를 실행하여 AAD-DS 도메인에 액세스합니다.

HDInsight 클러스터를 만드는 경우 사용자 지정 탭에서 Enterprise Security Package를 사용하도록 설정할 수 있습니다. 

![Azure HDInsight 보안 및 네트워킹](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

ESP를 사용하도록 설정하면 Azure AD-DS와 관련된 일반적인 구성 오류가 자동으로 검색되고 유효성이 검사됩니다.

![Azure HDInsight Enterprise Security Package 도메인 유효성 검사](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

초기 검색은 클러스터를 만들기 전에 오류를 수정할 수 있도록 하여 시간 절약 효과를 가져옵니다.

![Azure HDInsight Enterprise Security Package의 도메인 유효성 검사 실패](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

ESP로 HDInsight 클러스터를 만드는 경우 다음 매개 변수를 제공해야 합니다.

- **클러스터 관리 사용자**: 동기화된 Azure AD-DS에서 클러스터에 대한 관리자를 선택합니다. 이 계정은 이미 동기화되고 AAD-DS에서 사용할 수 있어야 합니다.

- **클러스터 액세스 그룹**: 클러스터에 동기화하려는 사용자의 보안 그룹은 Azure AD-DS에서 사용할 수 있어야 합니다. 예: HiveUsers 그룹. 자세한 내용은 [Azure Active Directory에서 그룹 만들기 및 멤버 추가](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

- **LDAPS URL**: 예를 들어 ldaps://contoso.com:636입니다.

다음 스크린샷에서는 Azure Portal의 성공적인 구성을 보여줍니다.

![Azure HDInsight ESP Active Directory Domain Services 구성](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

만든 관리 ID는 새 클러스터를 만들 때 사용자 할당 관리 ID 드롭다운에서 선택할 수 있습니다.

![Azure HDInsight ESP Active Directory Domain Services 구성](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>다음 단계
* Hive 정책 구성 및 Hive 쿼리 실행에 대한 내용은 [ESP가 포함된 HDInsight 클러스터에 대한 Hive 정책 구성](apache-domain-joined-run-hive.md)을 참조하세요.
* SSH를 사용하여 ESP로 HDInsight 클러스터에 연결하는 방법은 [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)을 참조하세요.