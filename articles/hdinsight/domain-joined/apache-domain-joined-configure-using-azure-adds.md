---
title: Azure AD DS를 갖춘 엔터프라이즈 보안 - Azure HDInsight
description: Azure Active Directory 도메인 서비스를 사용하여 HDInsight 엔터프라이즈 보안 패키지 클러스터를 설정하고 구성하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 02/03/2020
ms.openlocfilehash: cf239cbf69f3816e5ec03e07e2bd5fe370308f22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272839"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>HDInsight의 Azure Active Directory 도메인 서비스를 갖춘 엔터프라이즈 보안 패키지 구성

ESP(엔터프라이즈 보안 패키지) 클러스터는 Azure HDInsight 클러스터에서 다중 사용자 액세스를 제공합니다. ESP를 사용하는 HDInsight 클러스터는 도메인에 연결되기 때문에 도메인 사용자는 자신의 도메인 자격 증명을 사용하여 클러스터를 인증하고 빅 데이터 작업을 실행할 수 있습니다.

이 문서에서는 Azure Active Directory 도메인 서비스(Azure AD DS)를 사용하여 ESP를 사용하여 HDInsight 클러스터를 구성하는 방법을 알아봅니다.

> [!NOTE]  
> ESP는 일반적으로 HDInsight 3.6 및 4.0에서 아파치 스파크, 인터랙티브, 하두프 및 HBase와 같은 클러스터 유형에 사용할 수 있습니다. 아파치 카프카 클러스터 유형에 대한 ESP는 최상의 지원만으로 미리 보기상태입니다. ESP GA 날짜(2018년 10월 1일) 이전에 생성된 ESP 클러스터는 지원되지 않습니다.

## <a name="enable-azure-ad-ds"></a>Azure AD DS 사용

> [!NOTE]  
> 테넌트 관리자만 Azure AD DS를 사용하도록 설정할 수 있는 권한이 있습니다. 클러스터 저장소가 Azure Data Lake Storage Gen1 또는 Gen2인 경우 기본 Kerberos 인증을 사용하여 클러스터에 액세스해야 하는 사용자에 대해서만 Azure 다단계 인증을 비활성화해야 합니다. 
>
> [신뢰할 수 있는 IP](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) 또는 [조건부 액세스를](../../active-directory/conditional-access/overview.md) 사용하여 특정 사용자가 HDInsight 클러스터의 가상 네트워크에 대한 IP 범위에 액세스하는 *경우에만* 다단계 인증을 비활성화할 수 있습니다. 조건부 액세스를 사용하는 경우 HDInsight 가상 네트워크에서 Active Directory 서비스 끝점을 사용하도록 설정해야 합니다.
>
> 클러스터 저장소가 Azure Blob 저장소인 경우 다단계 인증을 사용하지 않도록 설정하지 마십시오.

ESP를 사용하여 HDInsight 클러스터를 만들기 전에 Azure AD DS를 사용하도록 설정하는 것이 전제 조건입니다. 자세한 내용은 [Azure 포털을 사용하여 Azure Active Directory 도메인 서비스 활성화를](../../active-directory-domain-services/tutorial-create-instance.md)참조하세요. 

Azure AD DS를 사용하도록 설정하면 모든 사용자와 개체가 기본적으로 Azure Active Directory(Azure AD)에서 Azure AD DS로 동기화되기 시작합니다. 동기화 작업의 길이는 Azure AD의 개체 수에 따라 달라집니다. 동기화는 수십만 개의 개체에 대해 며칠이 걸릴 수 있습니다. 

Azure AD DS에서 사용하는 도메인 이름은 HDInsight에서 작동하려면 39자 이하여야 합니다.

HDInsight 클러스터에 액세스해야 하는 그룹만 동기화하도록 선택할 수 있습니다. 특정 그룹만 동기화하는 이 옵션은 *범위가 지정된 동기화*라고 합니다. 지침의 경우 [Azure AD에서 관리되는 도메인으로의 범위 별 동기화 구성을](../../active-directory-domain-services/scoped-synchronization.md)참조하십시오.

보안 LDAP를 사용하도록 설정하면 주체 이름에 도메인 이름을 넣고 인증서에 대체 이름을 넣습니다. 예를 들어 도메인 이름이 *contoso100.onmicrosoft.com*인 경우 인증서 주체 이름 또는 주체 대체 이름이 정확해야 합니다. 자세한 내용은 [Azure AD DS 관리되는 도메인에 대해 보안 LDAP 구성](../../active-directory-domain-services/tutorial-configure-ldaps.md)을 참조하세요. 

다음 예제는 자체 서명된 인증서를 만듭니다. *contoso100.onmicrosoft.com* 도메인 이름은 (제목 이름) `DnsName` 및 (제목 대체 이름)에 있습니다. `Subject`

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Azure AD DS 상태 확인
**관리** 범주에서 상태를 선택하여 Azure Active Directory 도메인 서비스의 **상태를** 봅니다. Azure AD DS의 상태가 녹색(실행 중)이고 동기화가 완료되었는지 확인합니다.

![Azure AD DS 상태](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>관리되는 ID 만들기 및 권한 부여

*사용자 할당된 관리 되는 ID를* 사용 하 여 단순화 하 고 보안 도메인 서비스 작업을 도울 수 있습니다. 관리되는 ID에 **HDInsight 도메인 서비스 기여자** 역할을 할당하면 도메인 서비스 작업을 읽고, 만들고, 수정하고, 삭제할 수 있습니다. 

HDInsight 엔터프라이즈 보안 패키지에는 O 및 서비스 주체 를 만드는 것과 같은 특정 도메인 서비스 작업이 필요합니다. 모든 구독에서 관리되는 ID를 만들 수 있습니다. 일반적으로 관리되는 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리ID를](../../active-directory/managed-identities-azure-resources/overview.md)참조하십시오. Azure HDInsight에서 관리되는 ID가 작동하는 방식에 대한 자세한 내용은 [Azure HDInsight의 관리ID](../hdinsight-managed-identities.md)를 참조하십시오.

ESP 클러스터를 설정하려면 아직 없는 경우 사용자 지정 관리 ID를 만듭니다. 지침의 경우 [Azure 포털을 사용하여 사용자가 할당한 관리 ID에 역할 만들기, 목록, 삭제 또는 할당을](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)참조하세요. 

다음으로 Azure AD DS에 대한 **액세스 제어에서** 관리되는 ID에 **HDInsight 도메인 서비스 기여자** 역할을 할당합니다. 이 역할 할당을 수행하려면 Azure AD DS 관리자 권한이 필요합니다.

![Azure Active Directory Domain Services 액세스 제어](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

**HDInsight 도메인 서비스 기여자** 역할을 할당하면 이 ID가 Azure AD DS 도메인에서 도메인 서비스 작업을 수행할 수 있는 적절한 액세스 권한을 갖습니다. 이러한 작업에는 O만들기 및 삭제가 포함됩니다.

관리되는 ID를 만들고 올바른 역할이 부여된 후 Azure AD DS 관리자는 이 관리되는 ID를 사용할 수 있는 사람을 설정할 수 있습니다. 먼저 관리자는 포털에서 관리되는 ID를 선택한 다음 **개요**에서 **IAM(액세스 제어)을** 선택합니다. 그런 다음 오른쪽에서 관리자는 HDInsight ESP 클러스터를 만들려는 사용자 또는 그룹에 **관리되는 ID 운영자** 역할을 할당합니다. 

예를 들어 Azure AD DS 관리자는 다음 이미지와 같이 **sjmsi** 관리 ID에 대해 **MarketingTeam** 그룹에 이 역할을 할당할 수 있습니다. 이 할당을 통해 조직의 올바른 사용자는 관리되는 ID를 사용하여 ESP 클러스터를 만들 수 있습니다.

![HDInsight 관리 ID 운영자 역할 할당](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>네트워크 고려 사항

> [!NOTE]  
> Azure AD DS는 Azure 리소스 관리자 기반 가상 네트워크에 배포해야 합니다. Azure AD DS에는 클래식 가상 네트워크가 지원되지 않습니다. 자세한 내용은 [Azure 포털을 사용하여 Azure Active Directory 도메인 서비스 활성화를](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)참조하세요.

Azure AD DS를 사용하도록 설정하면 로컬 도메인 이름 시스템(DNS) 서버가 VM(Active Directory 가상 시스템)에서 실행됩니다. 이러한 사용자 지정 DNS 서버를 사용하도록 Azure AD DS 가상 네트워크를 구성합니다. 올바른 IP 주소를 찾으려면 **관리** 범주에서 **속성을** 선택하고 **가상 네트워크의 IP 주소**아래를 찾습니다.

![로컬 DNS 서버의 IP 주소 찾기](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

**설정** 범주에서 DNS 서버를 선택하여 이러한 사용자 지정 IP를 사용하도록 Azure AD DS 가상 네트워크의 **DNS 서버 구성을 변경합니다.** 그런 다음 **사용자 지정** 옵션을 선택하고 텍스트 상자에 첫 번째 IP 주소를 입력하고 **저장을**선택합니다. 동일한 단계를 사용하여 IP 주소를 더 추가합니다.

![가상 네트워크 DNS 구성 업데이트](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Azure AD DS 인스턴스와 HDInsight 클러스터를 동일한 Azure 가상 네트워크에 배치하는 것이 더 쉽습니다. 다른 가상 네트워크를 사용하려는 경우 도메인 컨트롤러가 HDInsight VM에 표시되도록 해당 가상 네트워크를 피어로 이동해야 합니다. 자세한 내용은 [가상 네트워크 피어링](../../virtual-network/virtual-network-peering-overview.md)을 참조하세요. 

가상 네트워크를 피어낸 후 사용자 지정 DNS 서버를 사용하도록 HDInsight 가상 네트워크를 구성하고 Azure AD DS 개인 IP를 DNS 서버 주소로 입력합니다. 두 가상 네트워크가 동일한 DNS 서버를 사용하는 경우 사용자 지정 도메인 이름이 올바른 IP로 확인되고 HDInsight에서 연결할 수 있습니다. 예를 들어 도메인 이름이 `contoso.com`이 단계 이후에 `ping contoso.com` 올바른 Azure AD DS IP로 확인되어야 합니다.

![피어링된 가상 네트워크에 대한 사용자 지정 DNS 서버 구성](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

HDInsight 서브넷에서 NSG(네트워크 보안 그룹) 규칙을 사용하는 경우 인바운드 트래픽과 아웃바운드 트래픽 모두에 [필요한 IP를](../hdinsight-management-ip-addresses.md) 허용해야 합니다.

네트워크가 올바르게 설정되어 있는지 테스트하려면 Windows VM을 HDInsight 가상 네트워크/서브넷에 가입하고 도메인 이름을 ping합니다. (IP로 해결해야 합니다.) **ldp.exe를** 실행하여 Azure AD DS 도메인에 액세스합니다. 그런 다음 이 Windows VM을 도메인에 조인하여 필요한 모든 RPC 호출이 클라이언트와 서버 간에 성공하는지 확인합니다. 

**nslookup을** 사용하여 저장소 계정 또는 사용할 수 있는 외부 데이터베이스(예: 외부 Hive 메타스토어 또는 레인저 DB)에 대한 네트워크 액세스를 확인할 수도 있습니다. NSG가 Azure AD DS 를 보호하는 데 도움이 되는 경우 필요한 모든 [포트가](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) Azure AD DS 서브넷의 NSG 규칙에서 허용되는지 확인합니다. 이 Windows VM의 도메인 조인이 성공하면 다음 단계로 계속 하여 ESP 클러스터를 만들 수 있습니다.

## <a name="create-an-hdinsight-cluster-with-esp"></a>ESP를 사용하여 HDInsight 클러스터 만들기

이전 단계를 올바르게 설정한 후 다음 단계는 ESP를 사용하도록 설정한 HDInsight 클러스터를 만드는 것입니다. HDInsight 클러스터를 만들 때 **보안 + 네트워킹** 탭에서 엔터프라이즈 보안 패키지를 활성화할 수 있습니다. 배포에 Azure 리소스 관리자 템플릿을 사용하려면 포털 환경을 한 번 사용하고 나중에 다시 사용할 수 있도록 **검토 + 만들기** 페이지에서 미리 채워진 템플릿을 다운로드합니다. 

클러스터를 만드는 동안 [HDInsight ID 브로커](identity-broker.md) 기능을 활성화할 수도 있습니다. ID 브로커 기능을 사용하면 다단계 인증을 사용하여 Ambari에 로그인하고 Azure AD DS에서 암호 해시없이 필요한 Kerberos 티켓을 얻을 수 있습니다.

> [!NOTE]  
> ESP 클러스터 이름의 처음 6자는 사용자 환경에서 고유해야 합니다. 예를 들어 서로 다른 가상 네트워크에 여러 ESP 클러스터가 있는 경우 클러스터 이름의 처음 6개 문자가 고유한지 확인하는 명명 규칙을 선택합니다.

![Azure HDInsight 엔터프라이즈 보안 패키지에 대한 도메인 유효성 검사](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

ESP를 사용하도록 설정하면 Azure AD DS와 관련된 일반적인 잘못된 구성이 자동으로 검색되고 유효성이 검사됩니다. 이러한 오류를 수정한 후 다음 단계를 계속할 수 있습니다.

![Azure HDInsight 엔터프라이즈 보안 패키지 실패 도메인 유효성 검사](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

ESP를 사용하여 HDInsight 클러스터를 만들 때 다음 매개 변수를 제공해야 합니다.

- **클러스터 관리자 사용자**: 동기화된 Azure AD DS 인스턴스에서 클러스터에 대한 관리자를 선택합니다. 이 도메인 계정은 이미 동기화되어 Azure AD DS에서 사용할 수 있어야 합니다.

- **클러스터 액세스 그룹:** 동기화할 사용자가 클러스터에 액세스할 수 있는 보안 그룹은 Azure AD DS에서 사용할 수 있어야 합니다. 예를 들어 HiveUsers 그룹입니다. 자세한 내용은 [Azure Active Directory에서 그룹 만들기 및 멤버 추가](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

- **LDAPS URL**: `ldaps://contoso.com:636`예는 .

새 클러스터를 만들 때 사용자가 **지정한 관리되는 ID** 드롭다운 목록에서 사용자가 만든 관리되는 ID를 선택할 수 있습니다.

![Azure HDInsight ESP Active Directory 도메인 서비스 관리 ID](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>다음 단계

* Hive 정책 구성 및 Hive 쿼리 실행에 대한 내용은 [ESP가 포함된 HDInsight 클러스터에 대한 Apache Hive 정책 구성](apache-domain-joined-run-hive.md)을 참조하세요.
* SSH를 사용하여 ESP로 HDInsight 클러스터에 연결하는 방법은 [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Apache Hadoop과 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)을 참조하세요.
