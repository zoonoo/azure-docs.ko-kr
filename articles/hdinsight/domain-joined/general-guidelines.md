---
title: Azure HDInsight의 엔터프라이즈 보안 일반 지침
description: 엔터프라이즈 보안 패키지 배포 및 관리를 더 쉽게 만들어야 하는 몇 가지 모범 사례입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: be6c1fdc5deb6d541656c198469822dae0a5f7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463207"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Azure HDInsight의 엔터프라이즈 보안 일반 정보 및 지침

보안 HDInsight 클러스터를 배포할 때 배포 및 클러스터 관리를 더 쉽게 만드는 몇 가지 모범 사례가 있습니다. 몇 가지 일반적인 정보와 지침은 여기에 설명되어 있습니다.

## <a name="use-of-secure-cluster"></a>보안 클러스터 사용

### <a name="recommended"></a>권장

* 클러스터는 여러 사용자가 동시에 사용합니다.
* 사용자는 동일한 데이터에 대해 서로 다른 수준의 액세스 권한을 갖습니다.

### <a name="not-necessary"></a>필요하지 않음

* 단일 사용자 계정과 같이 자동화된 작업만 실행하면 표준 클러스터가 충분합니다.
* 표준 클러스터를 사용하여 데이터 가져오기를 수행하고 사용자가 분석 작업을 실행할 수 있는 다른 보안 클러스터에서 동일한 저장소 계정을 사용할 수 있습니다.

## <a name="use-of-local-account"></a>로컬 계정 사용

* 공유 사용자 계정 또는 로컬 계정을 사용하는 경우 구성 또는 서비스를 변경하기 위해 계정을 사용한 사용자를 식별하기가 어렵습니다.
* 로컬 계정을 사용하는 것은 사용자가 더 이상 조직의 일부가 아니면 문제가 됩니다.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>정책

* 레인저는 기본적으로 **거부를** 정책으로 사용합니다.

* 권한 부여가 활성화된 서비스를 통해 데이터 액세스가 이루어지는 경우:
  * 레인저 권한 부여 플러그인이 호출되고 요청의 컨텍스트가 제공됩니다.
  * 레인저는 서비스에 대해 구성된 정책을 적용합니다. 레인저 정책이 실패하면 액세스 검사가 파일 시스템으로 지연됩니다. MapReduce와 같은 일부 서비스는 요청을 제출하는 동일한 사용자가 소유중인 파일 / 폴더만 확인합니다. Hive와 같은 서비스는 소유권 일치 또는 적절한 파일`rwx`시스템 사용 권한()을 확인합니다.

* Hive의 경우 권한 만들기/ 업데이트/삭제 권한을 가질 수 있는 권한 `rwx`외에도 사용자는 저장소 및 모든 하위 디렉터리에서 디렉터리에 대한 사용 권한을 가져야 합니다.

* 정책은 개인이 아닌 그룹(바람직)에 적용할 수 있습니다.

* 레인저 권한 부여자는 각 요청에 대한 해당 서비스에 대한 모든 레인저 정책을 평가합니다. 이 평가는 작업 또는 쿼리를 수락하는 데 걸리는 시간에 영향을 줄 수 있습니다.

### <a name="storage-access"></a>스토리지 액세스

* 저장소 유형이 WASB인 경우 OAuth 토큰이 관련되지 않습니다.
* 레인저가 권한 부여를 수행한 경우 저장소 액세스는 관리되는 ID를 사용하여 발생합니다.
* 레인저가 권한을 부여하지 않은 경우, 저장 액세스는 사용자의 OAuth 토큰을 사용하여 발생합니다.

### <a name="hierarchical-name-space"></a>계층적 이름 공간

계층 적 이름 공간을 사용할 수 없는 경우:

* 상속된 권한이 없습니다.
* 작동하는 파일 시스템 권한만 Azure Portal에서 사용자에게 직접 할당되는 **저장소 데이터 XXXX** RBAC 역할입니다.

### <a name="default-hdfs-permissions"></a>기본 HDFS 권한

* 기본적으로 사용자는 HDFS의 **/** 폴더에 액세스할 수 없습니다(성공하려면 저장소 Blob 소유자 역할에 있어야 합니다).
* mapreduce 및 기타 에 대한 스테이징 디렉터리에 대해 사용자 `sticky _wx` 별 디렉터리가 만들어지고 사용 권한이 제공됩니다. 사용자는 아래에 파일 및 폴더를 만들 수 있지만 다른 항목은 볼 수 없습니다.

### <a name="url-auth"></a>URL 인증

URL 인증이 활성화된 경우:

* 구성에는 URL 인증(예:)에서 `adl://`다루는 접두사가 포함됩니다.
* 이 URL에 대한 액세스가 있는 경우 레인저는 사용자가 허용 목록에 있는지 확인합니다.
* 레인저는 미세한 정책을 확인하지 않습니다.

## <a name="resource-groups"></a>리소스 그룹

클러스터 리소스를 구분할 수 있도록 각 클러스터에 대해 새 리소스 그룹을 사용합니다.

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSG, 방화벽 및 내부 게이트웨이

* NSG(네트워크 보안 그룹)를 사용하여 가상 네트워크를 잠급니다.
* 방화벽을 사용하여 아웃바운드 액세스 정책을 처리합니다.
* 공용 인터넷에 열려 있지 않은 내부 게이트웨이를 사용합니다.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory(Azure](../../active-directory/fundamentals/active-directory-whatis.md) AD)는 Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스입니다.

### <a name="policies"></a>정책

* IP 주소 기반 정책을 사용하여 조건부 액세스 정책을 사용하지 않도록 설정합니다. 이렇게 하려면 클러스터가 배포되는 VNET에서 서비스 끝점을 사용하도록 설정해야 합니다. MFA(AAD 이외의 서비스)에 외부 서비스를 사용하는 경우 IP 주소 기반 정책이 작동하지 않습니다.

* `AllowCloudPasswordValidation`페더레이션된 사용자에게는 정책이 필요합니다. HDInsight는 Azure AD에서 토큰을 얻기 위해 사용자 이름/암호를 직접 사용하므로 페더레이션된 모든 사용자에 대해 이 정책을 사용하도록 설정해야 합니다.

* 신뢰할 수 있는 IP를 사용하여 조건부 액세스 바이패스가 필요한 경우 서비스 끝점을 활성화합니다.

### <a name="groups"></a>그룹

* 항상 그룹으로 클러스터를 배포합니다.
* Azure AD를 사용하여 그룹 구성원 자격(클러스터의 개별 서비스를 관리하는 것보다 더 쉽습니다)을 관리합니다.

### <a name="user-accounts"></a>사용자 계정

* 각 시나리오에 대해 고유한 사용자 계정을 사용합니다. 예를 들어 가져오기위해 계정을 사용하고, 쿼리 또는 기타 처리 작업에 다른 계정을 사용합니다.
* 개별 정책 대신 그룹 기반 레인저 정책을 사용합니다.
* 클러스터에 더 이상 액세스할 수 없는 사용자를 관리하는 방법에 대한 계획을 세워야 합니다.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure Active Directory 도메인 서비스(Azure](../../active-directory-domain-services/overview.md) AD DS)는 Windows 서버 Active Directory와 완벽하게 호환되는 도메인 가입, 그룹 정책, LDAP(경량 디렉터리 액세스 프로토콜) 및 Kerberos/NTLM 인증과 같은 관리되는 도메인 서비스를 제공합니다.

보안 클러스터가 도메인에 가입하려면 Azure AD DS가 필요합니다.
HDInsight는 너무 많은 오류 지점, 자격 증명 공유, DNS 권한 등을 도입하므로 온-프레미스 도메인 컨트롤러 또는 사용자 지정 도메인 컨트롤러에 의존할 수 없습니다. 자세한 내용은 [Azure AD DS FAQ를](../../active-directory-domain-services/faqs.md)참조하십시오.

### <a name="azure-ad-ds-instance"></a>Azure AD DS 인스턴스

* 을 사용하여 인스턴스를 `.onmicrosoft.com domain`만듭니다. 이렇게 하면 도메인을 제공하는 DNS 서버가 여러 개 없습니다.
* LDAPS에 대해 자체 서명된 인증서를 만들고 Azure AD DS에 업로드합니다.
* 클러스터를 배포하기 위해 피어링된 가상 네트워크를 사용하십시오(HDInsight ESP 클러스터를 배포하는 팀이 여러 개 있는 경우 도움이 됩니다). 이렇게 하면 도메인 컨트롤러를 사용하여 가상 네트워크에서 NSG(포트)를 열 필요가 없습니다.
* 가상 네트워크에 대한 DNS를 올바르게 구성합니다(Azure AD DS 도메인 이름은 호스트 파일 항목 없이 해결해야 합니다).
* 아웃바운드 트래픽을 제한하는 경우 [HDInsight의 방화벽 지원을](../hdinsight-restrict-outbound-traffic.md) 읽었는지 확인하십시오.

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Azure AD에서 Azure AD DS로 동기화된 속성

* Azure AD 연결은 온-프레미스에서 Azure AD로 동기화됩니다.
* Azure AD DS는 Azure AD에서 동기화됩니다.

Azure AD DS는 Azure AD의 개체를 주기적으로 동기화합니다. Azure 포털의 Azure AD DS 블레이드에 동기화 상태가 표시됩니다. 동기화의 각 단계에서 고유한 속성이 충돌하여 이름이 바뀌어 지을 수 있습니다. Azure AD에서 Azure AD DS에 이르는 속성 매핑에 주의하십시오.

자세한 내용은 [Azure AD UserPrincipalName 채우기](../../active-directory/hybrid/plan-connect-userprincipalname.md)및 Azure [AD DS 동기화 작동 방식을](../../active-directory-domain-services/synchronization.md)참조하십시오.

### <a name="password-hash-sync"></a>암호 해시 동기화

* 암호는 다른 개체 유형과 다르게 동기화됩니다. 되돌릴 수 없는 암호 해시만 Azure AD 및 Azure AD DS에서 동기화됩니다.
* AD 연결을 통해 Azure AD에 대한 온프레미스를 사용하도록 설정해야 합니다.
* Azure AD에서 Azure AD DS 동기화는 자동으로 수행됩니다(대기 시간 20분 미만).
* 암호 해시는 변경된 암호가 있는 경우에만 동기화됩니다. 암호 해시 동기화를 사용하도록 설정하면 기존 암호가 돌이킬 수 없을 정도로 저장되기 때문에 모든 기존 암호가 자동으로 동기화되지 않습니다. 암호를 변경하면 암호 해시가 동기화됩니다.

### <a name="computer-objects-location"></a>컴퓨터 개체 위치

각 클러스터는 단일 OU와 연결됩니다. 내부 사용자는 OU에 프로비전됩니다. 모든 노드는 동일한 OU에 도메인으로 조인됩니다.

### <a name="active-directory-administrative-tools"></a>Active Directory 관리 도구

Windows Server VM에 Active Directory 관리 도구를 설치하는 방법에 대한 단계는 [관리 도구 설치를](../../active-directory-domain-services/tutorial-create-management-vm.md)참조하십시오.

## <a name="troubleshooting"></a>문제 해결

### <a name="cluster-creation-fails-repeatedly"></a>클러스터 생성이 반복적으로 실패

가장 일반적인 이유:

* DNS 구성이 올바르지 않으면 클러스터 노드의 도메인 조인이 실패합니다.
* NSG가 너무 제한적이어서 도메인 가입을 방지할 수 있습니다.
* 관리되는 ID에는 충분한 사용 권한이 없습니다.
* 클러스터 이름은 처음 6개의 문자(다른 라이브 클러스터 또는 삭제된 클러스터)에서 고유하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight의 Azure Active Directory 도메인 서비스를 갖춘 엔터프라이즈 보안 패키지 구성](./apache-domain-joined-configure-using-azure-adds.md)

* [Azure Active Directory 사용자를 HDInsight 클러스터에 동기화합니다.](../hdinsight-sync-aad-users-to-cluster.md)
