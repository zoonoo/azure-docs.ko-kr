---
title: Azure HDInsight의 엔터프라이즈 보안 일반 지침
description: Enterprise Security Package 배포 및 관리를 용이하게 하는 몇 가지 모범 사례가 있습니다.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 92ad8362f75cdf0613d4ee95f39c23aa6d4819bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933576"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Azure HDInsight의 엔터프라이즈 보안 일반 정보 및 지침

보안 HDInsight 클러스터를 배포할 때 배포 및 클러스터 관리를 보다 용이하게 하는 몇 가지 모범 사례가 있습니다. 몇 가지 일반적인 정보와 지침은 여기에 설명되어 있습니다.

## <a name="use-of-secure-cluster"></a>보안 클러스터 사용

### <a name="recommended"></a>권장

* 동시에 여러 사용자가 클러스터를 사용합니다.
* 사용자는 동일한 데이터에 대한 다양한 수준의 액세스 권한을 가집니다.

### <a name="not-necessary"></a>필요하지 않음

* 자동 작업만 실행하게 되며(예: 단일 사용자 계정) 표준 클러스터로 충분합니다.
* 표준 클러스터를 사용하여 데이터 가져오기를 수행하고, 사용자가 분석 작업을 실행할 수 있는 다른 보안 클러스터에서 동일한 스토리지 계정을 사용할 수 있습니다.

## <a name="use-of-local-account"></a>로컬 계정 사용

* 공유 사용자 계정 또는 로컬 계정을 사용하는 경우 해당 계정을 사용하여 구성 또는 서비스를 변경한 사람을 식별하기 어렵습니다.
* 사용자가 더 이상 조직의 일부가 아니면 로컬 계정을 사용하는 데 문제가 있을 수 있습니다.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>정책

* 기본적으로 Ranger는 정책으로 **거부** 를 사용합니다.

* 권한 부여를 사용하도록 설정한 서비스를 통해 데이터 액세스를 수행하는 경우:
  * Ranger 권한 부여 플러그 인이 사용되며 요청 컨텍스트가 제공됩니다.
  * Ranger는 서비스에 대해 구성된 정책을 적용합니다. Ranger 정책이 실패하면 파일 시스템에 대한 액세스 확인이 지연됩니다. MapReduce와 같은 일부 서비스는 요청을 제출하는 동일한 사용자가 해당 파일/폴더를 소유하고 있는지만 확인합니다. Hive와 같은 서비스는 소유권 일치 또는 적절한 파일 시스템 권한(`rwx`)을 확인합니다.

* Hive의 경우 사용자는 만들기/업데이트/삭제 권한을 수행할 수 있는 권한을 보유하는 것 외에도 스토리지의 디렉터리 및 모든 하위 디렉터리에 대한 `rwx` 권한이 있어야 합니다.

* 정책을 개인 대신 그룹(권장)에 적용할 수 있습니다.

* Ranger 권한 부여자는 각 요청에 대해 해당 서비스에 대한 모든 Ranger 정책을 평가합니다. 이 평가는 작업이나 쿼리를 수락하는 데 걸리는 시간에 영향을 미칠 수 있습니다.

### <a name="storage-access"></a>스토리지 액세스

* 스토리지 유형이 WASB인 경우에는 OAuth 토큰은 관련이 없습니다.
* Ranger가 권한 부여를 수행하는 경우 관리 ID를 사용하여 스토리지 액세스가 발생합니다.
* Ranger가 권한 부여를 수행하지 않은 경우 스토리지 액세스는 사용자의 OAuth 토큰을 사용하여 수행됩니다.

### <a name="hierarchical-name-space"></a>계층적 네임스페이스

계층적 네임스페이스를 사용하도록 설정하지 않은 경우:

* 상속된 사용 권한이 없습니다.
* 작동하는 유일한 파일 시스템 권한은 Azure Portal에서 직접 사용자에게 할당되는 **스토리지 데이터 XXXX** Azure 역할입니다.

### <a name="default-hdfs-permissions"></a>기본 HDFS 사용 권한

* 기본적으로 사용자는 HDFS의 **/** 폴더에 대한 액세스 권한이 없습니다(성공적인 액세스를 위해서는 스토리지 Blob 소유자 역할에 속해야 함).
* mapreduce 및 기타 작업을 위한 스테이징 디렉터리의 경우 사용자 특정 디렉터리가 만들어지고 `sticky _wx` 권한이 제공됩니다. 사용자는 아래에 파일과 폴더를 만들 수 있지만 다른 항목은 볼 수 없습니다.

### <a name="url-auth"></a>URL 인증

URL 인증을 사용하도록 설정한 경우:

* 구성에는 URL 인증에 포함된 접두사(예: `adl://`)가 포함됩니다.
* 이 URL에 대한 액세스 권한이 있는 경우 Ranger는 사용자가 허용 목록에 있는지 확인합니다.
* Ranger는 세분화된 정책을 확인하지 않습니다.

## <a name="resource-groups"></a>리소스 그룹

클러스터 리소스 간을 구분할 수 있도록 각 클러스터에 대해 새 리소스 그룹을 사용합니다.

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSG, 방화벽 및 내부 게이트웨이

* NSG(네트워크 보안 그룹)를 사용하여 가상 네트워크를 잠급니다.
* 방화벽을 사용하여 아웃바운드 액세스 정책을 처리합니다.
* 퍼블릭 인터넷에 열려 있지 않은 내부 게이트웨이를 사용합니다.

## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD([Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md))는 Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스입니다.

### <a name="policies"></a>정책

* IP 주소 기반 정책을 사용하여 조건부 액세스 정책을 사용하지 않도록 설정합니다. 이렇게 하려면 클러스터가 배포되는 VNET에서 서비스 엔드포인트를 사용하도록 설정해야 합니다. MFA에 외부 서비스(AAD 이외)를 사용하는 경우 IP 주소 기반 정책이 작동하지 않습니다.

* `AllowCloudPasswordValidation` 정책은 페더레이션된 사용자에 대해 필요합니다. HDInsight는 사용자 이름/암호를 직접 사용하여 Azure AD에서 토큰을 가져오기 때문에 모든 페더레이션된 사용자에 대해 이 정책을 사용하도록 설정해야 합니다.

* 신뢰할 수 있는 IP를 사용하는 조건부 액세스 무시가 필요한 경우 서비스 엔드포인트를 사용하도록 설정합니다.

### <a name="groups"></a>그룹

* 항상 그룹을 사용하여 클러스터를 배포합니다.
* Azure AD를 사용하여 그룹 멤버 자격을 관리합니다(클러스터의 개별 서비스를 관리하는 것보다 더 간편함).

### <a name="user-accounts"></a>사용자 계정

* 각 시나리오에 고유한 사용자 계정을 사용합니다. 예를 들어, 가져오기를 위해 한 계정을 사용하고, 쿼리 또는 다른 처리 작업을 위해 다른 계정을 사용합니다.
* 개별 정책 대신 그룹 기반 Ranger 정책을 사용합니다.
* 더 이상 클러스터에 대한 액세스 권한이 없는 사용자를 관리하는 방법에 대한 계획을 세워야 합니다.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

Azure AD DS([Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md))는 Windows Server Active Directory와 완전히 호환되는 도메인 조인, 그룹 정책, LDAP(Lightweight Directory Access Protocol) 및 Kerberos/NTLM 인증과 같은 관리되는 도메인 서비스를 제공합니다.

Azure AD DS는 보안 클러스터에서 도메인에 조인하는 데 필요합니다.
HDInsight는 너무 많은 오류 요소, 자격 증명 공유, DNS 권한 등을 도입하므로 온-프레미스 도메인 컨트롤러 또는 사용자 지정 도메인 컨트롤러에 종속되지 않습니다. 자세한 내용은 [Azure AD DS FAQ](../../active-directory-domain-services/faqs.md)를 참조하세요.

### <a name="azure-ad-ds-instance"></a>Azure AD DS 인스턴스

* `.onmicrosoft.com domain`을 사용하여 인스턴스를 만듭니다. 이러한 방식으로 도메인을 지원하는 DNS 서버는 많지 않을 것입니다.
* LDAPS에 대해 자체 서명된 인증서를 만들고 Azure AD DS에 업로드합니다.
* 클러스터를 배포하는 데 피어링된 가상 네트워크를 사용합니다. HDInsight ESP 클러스터를 배포하는 많은 팀이 있는 경우에 유용합니다. 이렇게 하면 도메인 컨트롤러를 사용하여 가상 네트워크에서 포트(NSG)를 열 필요가 없습니다.
* 가상 네트워크에 대한 DNS를 적절히 구성합니다. Azure AD DS 도메인 이름은 hosts 파일 항목 없이 확인되어야 합니다.
* 아웃바운드 트래픽을 제한하는 경우 [HDInsight의 방화벽 지원](../hdinsight-restrict-outbound-traffic.md)을 확인하세요.

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Azure AD에서 Azure AD DS로 속성이 동기화됨

* Azure AD Connect는 온-프레미스에서 Azure AD로 동기화합니다.
* Azure AD DS는 Azure AD에서 동기화합니다.

Azure AD DS는 Azure AD의 개체를 주기적으로 동기화합니다. Azure Portal의 Azure AD DS 블레이드에 동기화 상태가 표시됩니다. 동기화의 각 스테이지에서 고유한 속성이 충돌하고 이름이 바뀔 수 있습니다. Azure AD와 Azure AD DS 간의 속성 매핑에 유의합니다.

자세한 내용은 [Azure AD UserPrincipalName 채우기](../../active-directory/hybrid/plan-connect-userprincipalname.md) 및 [Azure AD DS 동기화 작동 방식](../../active-directory-domain-services/synchronization.md)을 참조하세요.

### <a name="password-hash-sync"></a>암호 해시 동기화

* 암호는 다른 개체 유형과 다르게 동기화됩니다. 복구할 수 없는 암호 해시만 Azure AD 및 Azure AD DS에서 동기화됩니다.
* 온-프레미스에서 Azure AD로의 동기화는 AD Connect를 통해 사용하도록 설정해야 합니다.
* Azure AD에서 Azure AD DS로의 동기화는 자동으로 진행됩니다(대기 시간은 20분 미만임).
* 암호 해시는 암호가 변경된 경우에만 동기화됩니다. 암호 해시 동기화를 사용하도록 설정하면 기존 암호는 복구 불가능하게 저장되므로 자동으로 동기화되지 않습니다. 암호를 변경하면 암호 해시가 동기화됩니다.

### <a name="computer-objects-location"></a>컴퓨터 개체 위치

각 클러스터는 단일 OU와 연결됩니다. 내부 사용자는 OU에 프로비저닝됩니다. 모든 노드는 동일한 OU의 도메인에 조인됩니다.

### <a name="active-directory-administrative-tools"></a>Active Directory 관리 도구

Windows Server VM에 Active Directory 관리 도구를 설치하는 방법에 대한 단계는 [관리 도구 설치](../../active-directory-domain-services/tutorial-create-management-vm.md)를 참조하세요.

## <a name="troubleshooting"></a>문제 해결

### <a name="cluster-creation-fails-repeatedly"></a>클러스터 만들기가 반복적으로 실패합니다.

가장 일반적인 이유는 다음과 같습니다.

* DNS 구성이 올바르지 않습니다. 클러스터 노드의 도메인 조인이 실패합니다.
* NSG가 너무 제한적이어서 도메인 조인을 방지합니다.
* 관리 ID에 충분한 권한이 없습니다.
* 클러스터 이름의 처음 6자가 고유하지 않습니다(다른 라이브 클러스터 또는 삭제된 클러스터를 사용하는 경우).

## <a name="authentication-setup-and-configuration"></a>인증 설정 및 구성

### <a name="user-principal-name-upn"></a>UPN(사용자 계정 이름)

* 모든 서비스에 대해 소문자를 사용하세요. UPN은 ESP 클러스터에서 대/소문자를 구분하지 않습니다.
* UPN 접두사는 Azure AD-DS의 두 SAMAccountName 모두와 일치해야 합니다. 메일 필드와 일치하지 않아도 됩니다.

### <a name="ldap-properties-in-ambari-configuration"></a>Ambari 구성의 LDAP 속성

HDInsight 클러스터 구성에 영향을 주는 Ambari 속성의 전체 목록은 [AMBARI LDAP 인증 설정](https://ambari.apache.org/1.2.1/installing-hadoop-using-ambari/content/ambari-chap2-4.html)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 Azure Active Directory Domain Services를 사용하는 Enterprise Security Package 구성](./apache-domain-joined-configure-using-azure-adds.md)

* [Azure Active Directory 사용자를 HDInsight 클러스터와 동기화](../hdinsight-sync-aad-users-to-cluster.md)
