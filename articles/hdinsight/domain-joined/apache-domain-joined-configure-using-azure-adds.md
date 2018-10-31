---
title: Azure AD-DS를 사용하여 Enterprise Security Package로 HDInsight 클러스터 구성
description: Azure Active Directory Domain Services를 사용하여 HDInsight Enterprise Security Package 클러스터를 설정 및 구성하는 방법을 알아봅니다.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 10/9/2018
ms.openlocfilehash: 6218a96b3939b2a07832dd3d6d19327cfb039b68
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986936"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services를 사용하여 Enterprise Security Package로 HDInsight 클러스터 구성

ESP(Enterprise Security Package) 클러스터는 Azure HDInsight 클러스터에서 다중 사용자 액세스를 제공합니다. ESP를 사용하는 HDInsight 클러스터는 도메인에 연결되기 때문에 도메인 사용자는 자신의 도메인 자격 증명을 사용하여 클러스터를 인증하고 빅 데이터 작업을 실행할 수 있습니다. 

이 문서에서는 Azure AD DS(Azure Active Directory Domain Services)를 사용하여 ESP로 HDInsight 클러스터를 구성하는 방법을 알아봅니다.

>[!NOTE]
>ESP는 Spark, Interactive 및 Hadoop용 HDI 3.6에서 GA(일반적으로 사용할 수) 있습니다. HBase 및 Kafka 클러스터 유형에 대한 ESP는 미리 보기입니다.

## <a name="enable-azure-ad-ds"></a>Azure AD-DS 사용

> [!NOTE]
> 테넌트 관리자만 Azure AD-DS 인스턴스를 만들 수 있는 권한이 있습니다. 클러스터 저장소가 ADLS(Azure Data Lake Store) Gen1 또는 Gen2인 경우 클러스터에 액세스하는 사용자만 MFA(Multi-Factor Authentication)를 해제하세요. 클러스터 저장소가 Azure Blob Storage(WASB)인 경우 MFA를 해제하지 마세요.

ESP로 HDInsight 클러스터를 만들려면 먼저 Azure AD-DS를 사용하도록 설정해야 합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화](../../active-directory-domain-services/active-directory-ds-getting-started.md)를 참조하세요. 

Azure AD-DS를 사용하도록 설정하는 경우 모든 사용자 및 개체는 기본적으로 Azure Active Directory에서 Azure AD-DS로 동기화를 시작합니다. 동기화 작업의 길이는 Azure AD의 개체 수에 따라 달라집니다. 수십만 개의 개체에 대한 동기화는 며칠이 걸릴 수 있습니다. 

고객은 HDInsight 클러스터에 대한 액세스 권한이 필요한 그룹만 동기화하도록 선택할 수 있습니다. 특정 그룹만 동기화하는 이 옵션은 *범위가 지정된 동기화*라고 합니다. 지침은 [Azure AD부터 관리 도메인까지로 범위가 지정된 동기화 구성](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization)을 참조하세요.

보안 LDAP를 사용하도록 설정할 경우 인증서의 주체 이름 또는 대체 주체 이름에 도메인 이름을 입력합니다. 예를 들어 도메인 이름이 *contoso.com*인 경우 인증서 주체 이름 또는 대체 주체 이름에 정확한 이름이 있어야 합니다. 자세한 내용은 [Azure AD-DS 관리되는 도메인에 대해 보안 LDAP 구성](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)을 참조하세요.


## <a name="check-azure-ad-ds-health-status"></a>Azure AD-DS 상태 확인
**관리** 범주에서 **상태**를 선택하여 Azure Active Directory Domain Services의 상태를 확인합니다. Azure AD-DS 상태가 녹색(실행 중)인지, 동기화가 완료됐는지 확인합니다.

![Azure Active Directory Domain Services 상태](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>관리 ID 만들기 및 권한 부여

**사용자 할당 관리 ID**는 도메인 서비스 작업을 단순화하는 데 사용됩니다. HDInsight 도메인 서비스 기여자 역할에 관리 ID를 할당하면 도메인 서비스 작업을 읽고, 만들고, 수정하고, 삭제할 수 있습니다. OU 및 서비스 주체 만들기 같은 특정 도메인 서비스 작업은 HDInsight Enterprise Security Package에 필요합니다. 관리 ID는 모든 구독에서 만들 수 있습니다. 자세한 내용은 [Azure 리소스에 대한 ID 관리](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

HDInsight ESP 클러스터에 사용할 관리 ID를 설정하려면 사용자 할당 관리 ID를 만들어야 합니다(아직 없는 경우). 작업 지침은 [Azure Portal을 사용하여 사용자 할당 관리 ID를 생성, 나열, 삭제 또는 할당](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)을 참조하세요. 다음으로, Azure AD-DS 액세스 제어의 **HDInsight 도메인 서비스 기여자** 역할에 관리 ID를 할당합니다(이 역할을 할당하려면 AAD-DS 관리 권한이 필요).

![Azure Active Directory Domain Services 액세스 제어](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

**HDInsight 도메인 서비스 기여자** 역할에 관리 ID를 할당하면 해당 ID에 AAD-DS 도메인에서 특정 도메인 서비스 작업을 수행하기에 적합한 권한이 있는지 확인할 수 있습니다.

관리 ID를 만들고 올바른 역할을 할당한 후에는 AAD-DS 관리자가 이 관리 ID를 사용할 사용자를 설정할 수 있습니다. 관리 ID를 사용할 사용자를 설정하려면 관리자는 포털에서 관리 ID를 선택한 다음, **개요**에서 **IAM(액세스 제어)** 을 클릭해야 합니다. 그런 다음, 오른쪽에서 HDInsight ESP 클러스터를 만들 사용자 또는 그룹에 "관리 ID 운영자" 역할을 할당합니다. 예를 들어 AAD-DS 관리자는 아래 그림처럼 "sjmsi" 관리 ID를 사용할 "MarketingTeam" 그룹에 이 역할을 할당할 수 있습니다.

![HDInsight 관리 ID 운영자 역할 할당](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>네트워킹 고려 사항

Azure AD-DS를 사용하도록 설정하면 로컬 DNS(도메인 이름 서비스) 서버가 AD VM(Virtual Machine)에서 실행됩니다. 이러한 사용자 지정 DNS 서버를 사용하도록 Azure AD-DS VNET(가상 네트워크)을 구성합니다. 올바른 IP 주소를 찾으려면 **관리** 범주 아래에서 **속성**을 선택하고, **가상 네트워크의 IP 주소** 아래에 나열된 IP 주소를 살펴봅니다.

![로컬 DNS 서버의 IP 주소 찾기](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

**설정** 범주 아래에서 **DNS 서버**를 선택하여 이러한 사용자 지정 IP를 사용하도록 Azure AD-DS VNET에서 DNS 서버의 구성을 변경합니다. 그런 다음, **사용자 지정** 옆에 있는 라디오 단추를 클릭하고, 아래의 텍스트 상자에 첫 번째 IP 주소를 입력하고, **저장**을 클릭합니다. 동일한 단계를 사용하여 추가 IP 주소를 추가합니다.

![VNET DNS 구성 업데이트](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Azure AD-DS 인스턴스와 HDInsight 클러스터를 동일한 Azure Virtual Network에 배치하는 것이 더 쉽습니다. 다른 VNET을 사용할 계획인 경우 HDI VM이 도메인 컨트롤러를 인식할 수 있도록 해당 가상 네트워크를 피어링해야 합니다. 자세한 내용은 [가상 네트워크 피어링](../../virtual-network/virtual-network-peering-overview.md)을 참조하세요. 

VNET이 피어링된 후에는 사용자 지정 DNS 서버를 사용하도록 HDInsight VNET을 구성하고 Azure AD-DS 개인 IP를 DNS 서버 주소로 입력합니다. 두 VNET이 동일한 DNS 서버를 사용하는 경우 사용자 지정 도메인 이름이 올바른 IP로 확인되고 HDInsight에서 연결할 수 있게 됩니다. 예를 들어 도메인 이름이 "contoso.com"인 경우 이 단계를 완료하고 "contoso.com"을 ping하면 올바른 Azure AD-DS IP로 확인되어야 합니다. 

![피어링된 VNET에 대한 사용자 지정 DNS 서버 구성](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

네트워킹이 올바르게 설정되었는지 **테스트**하려면 Windows VM을 HDInsight VNET/서브넷에 조인하고 도메인 이름을 ping한 다음(IP로 확인되어야 함), **ldp.exe**를 실행하여 Azure AD-DS 도메인에 액세스합니다. 그런 다음, **이 Windows VM을 도메인에 조인**하여 클라이언트와 서버 간에 필요한 모든 RPC 호출이 성공했는지 확인합니다. **nslookup**을 사용하여 저장소 계정 또는 외부 DB(예: 외부 Hive metastore 또는 Ranger DB)에 대한 네트워킹 액세스를 확인할 수도 있습니다.
AAD-DS가 NSG로 보호되는 경우 [필요한 모든 포트](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers)가 AAD-DS 서브넷 네트워크 보안 그룹 규칙의 허용 목록에 추가되어야 합니다. 

## <a name="create-a-hdinsight-cluster-with-esp"></a>ESP로 HDInsight 클러스터 만들기

이전 단계를 올바르게 설정한 후에 할 일은 ESP가 활성화된 HDInsight 클러스터를 만드는 것입니다. HDInsight 클러스터를 만들 때 **사용자 지정** 탭에서 Enterprise Security Package를 사용하도록 설정할 수 있습니다. 배포에 Azure Resource Manager 템플릿을 사용하는 것을 선호하는 경우 마지막 "요약" 페이지에서 포털 환경을 한 번 사용하여 미리 채워진 템플릿을 다운로드해 두면 나중에 재사용할 수 있습니다.

![Azure HDInsight 보안 및 네트워킹](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

ESP를 사용하도록 설정하면 Azure AD-DS와 관련된 일반적인 구성 오류가 자동으로 검색되고 유효성이 검사됩니다.

![Azure HDInsight Enterprise Security Package 도메인 유효성 검사](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

초기 검색은 클러스터를 만들기 전에 오류를 수정할 수 있도록 하여 시간 절약 효과를 가져옵니다.

![Azure HDInsight Enterprise Security Package의 도메인 유효성 검사 실패](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

ESP로 HDInsight 클러스터를 만드는 경우 다음 매개 변수를 제공해야 합니다.

- **클러스터 관리 사용자**: 동기화된 Azure AD-DS에서 클러스터에 대한 관리자를 선택합니다. 이 계정은 이미 동기화되고 Azure AD-DS에서 사용할 수 있어야 합니다.

- **클러스터 액세스 그룹**: 클러스터에 동기화하려는 사용자의 보안 그룹은 Azure AD-DS에서 사용할 수 있어야 합니다. 예: HiveUsers 그룹. 자세한 내용은 [Azure Active Directory에서 그룹 만들기 및 멤버 추가](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

- **LDAPS URL**: 예를 들어 ldaps://contoso.com:636입니다.

다음 스크린샷에서는 Azure Portal의 성공적인 구성을 보여줍니다.

![Azure HDInsight ESP Active Directory Domain Services 구성](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

만든 관리 ID는 새 클러스터를 만들 때 사용자 할당 관리 ID 드롭다운에서 선택할 수 있습니다.

![Azure HDInsight ESP Active Directory Domain Services 구성](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>다음 단계
* Hive 정책 구성 및 Hive 쿼리 실행에 대한 내용은 [ESP가 포함된 HDInsight 클러스터에 대한 Hive 정책 구성](apache-domain-joined-run-hive.md)을 참조하세요.
* SSH를 사용하여 ESP로 HDInsight 클러스터에 연결하는 방법은 [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)을 참조하세요.
