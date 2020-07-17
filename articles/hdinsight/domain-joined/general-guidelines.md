---
title: Azure HDInsight의 엔터프라이즈 보안 일반 지침
description: Enterprise Security Package 배포 및 관리를 용이 하 게 하는 몇 가지 모범 사례가 있습니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 142fdf27fde100385140baacdeba9249b2e7989b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84887887"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Azure HDInsight의 엔터프라이즈 보안 일반 정보 및 지침

보안 HDInsight 클러스터를 배포할 때 배포 및 클러스터를 보다 쉽게 관리할 수 있도록 하는 몇 가지 모범 사례가 있습니다. 몇 가지 일반적인 정보와 지침은 여기에 설명 되어 있습니다.

## <a name="use-of-secure-cluster"></a>보안 클러스터 사용

### <a name="recommended"></a>권장

* 동시에 여러 사용자가 클러스터를 사용 합니다.
* 사용자는 동일한 데이터에 대 한 다양 한 수준의 액세스 권한을 가집니다.

### <a name="not-necessary"></a>필요 없음

* 자동 작업 (예: 단일 사용자 계정)만 실행 하 고 표준 클러스터는 충분 합니다.
* 표준 클러스터를 사용 하 여 데이터 가져오기를 수행 하 고, 사용자가 분석 작업을 실행할 수 있는 다른 보안 클러스터에서 동일한 저장소 계정을 사용할 수 있습니다.

## <a name="use-of-local-account"></a>로컬 계정 사용

* 공유 사용자 계정 또는 로컬 계정을 사용 하는 경우 해당 계정을 사용 하 여 구성 또는 서비스를 변경한 사람을 식별 하기 어렵습니다.
* 사용자가 더 이상 조직의 일부가 아니면 로컬 계정을 사용 하는 것이 문제가 될 수 있습니다.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>정책

* 기본적으로 레인저는 정책으로 **Deny** 를 사용 합니다.

* 권한 부여를 사용 하는 서비스를 통해 데이터 액세스를 수행 하는 경우:
  * 요청 컨텍스트가 제공 되 면 레인저 권한 부여 플러그 인이 호출 됩니다.
  * 레인저는 서비스에 대해 구성 된 정책을 적용 합니다. 레인저 정책이 실패 하면 액세스 확인이 파일 시스템으로 지연 됩니다. MapReduce와 같은 일부 서비스는 요청을 제출 하는 동일한 사용자가 소유 하 고 있는 파일/폴더가 있는지만 확인 합니다. Hive와 같은 서비스는 소유권 일치 또는 적절 한 파일 시스템 권한 ( `rwx` )을 확인 합니다.

* Hive의 경우 생성/업데이트/삭제 권한을 수행할 수 있는 권한을 보유 하는 것 외에도 사용자에 게 `rwx` 저장소 및 모든 하위 디렉터리의 디렉터리에 대 한 권한이 있어야 합니다.

* 정책을 개인 대신 그룹 (권장)에 적용할 수 있습니다.

* 레인저 권한 부여자는 각 요청에 대해 해당 서비스에 대 한 모든 레인저 정책을 평가 합니다. 이 평가는 작업이 나 쿼리를 수락 하는 데 걸리는 시간에 영향을 미칠 수 있습니다.

### <a name="storage-access"></a>저장소 액세스

* 저장소 유형이 WASB 인 경우에는 OAuth 토큰이 포함 되지 않습니다.
* 레인저가 권한 부여를 수행 하는 경우 관리 되는 Id를 사용 하 여 저장소 액세스가 발생 합니다.
* 레인저가 권한 부여를 수행 하지 않은 경우 저장소 액세스는 사용자의 OAuth 토큰을 사용 하 여 수행 됩니다.

### <a name="hierarchical-name-space"></a>계층 구조 이름 공간

계층적 이름 공간을 사용할 수 없는 경우:

* 상속 된 사용 권한이 없습니다.
* 작업 하는 파일 시스템 권한만 **저장소 데이터 XXXX** RBAC 역할 이며 Azure Portal에서 직접 사용자에 게 할당 됩니다.

### <a name="default-hdfs-permissions"></a>기본 HDFS 사용 권한

* 기본적으로 사용자는 HDFS의 폴더에 대 한 액세스 권한이 없습니다 **/** . 액세스를 성공적으로 수행 하려면 저장소 blob 소유자 역할에 있어야 합니다.
* Mapreduce 및 기타에 대 한 준비 디렉터리의 경우 사용자 특정 디렉터리가 만들어지고 권한이 제공 됩니다 `sticky _wx` . 사용자는 아래에 파일과 폴더를 만들 수 있지만 다른 항목은 볼 수 없습니다.

### <a name="url-auth"></a>URL 인증

Url 인증을 사용 하는 경우:

* 구성에는 url 인증에 포함 된 접두사 (예:)가 포함 됩니다 `adl://` .
* 이 url에 대 한 액세스 권한이 있는 경우에는 사용자가 허용 목록에 있는지 확인 합니다.
* 레인저는 세분화 된 정책을 확인 하지 않습니다.

## <a name="resource-groups"></a>리소스 그룹

클러스터 리소스를 구분할 수 있도록 각 클러스터에 대해 새 리소스 그룹을 사용 합니다.

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSGs, 방화벽 및 내부 게이트웨이

* NSGs (네트워크 보안 그룹)를 사용 하 여 가상 네트워크를 잠급니다.
* 방화벽을 사용 하 여 아웃 바운드 액세스 정책을 처리 합니다.
* 공용 인터넷에 열려 있지 않은 내부 게이트웨이를 사용 합니다.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD)는 Microsoft의 클라우드 기반 id 및 액세스 관리 서비스입니다.

### <a name="policies"></a>정책

* IP 주소 기반 정책을 사용 하 여 조건부 액세스 정책을 사용 하지 않도록 설정 합니다. 이렇게 하려면 클러스터가 배포 되는 Vnet 서비스 끝점을 사용 하도록 설정 해야 합니다. MFA에 외부 서비스를 사용 하는 경우 (AAD 이외의) IP 주소 기반 정책이 작동 하지 않음

* `AllowCloudPasswordValidation`페더레이션된 사용자에 대 한 정책이 필요 합니다. HDInsight는 사용자 이름/암호를 직접 사용 하 여 Azure AD에서 토큰을 가져오기 때문에 모든 페더레이션된 사용자에 대해이 정책을 사용 하도록 설정 해야 합니다.

* 신뢰할 수 있는 Ip를 사용 하는 조건부 액세스 무시가 필요한 경우 서비스 끝점을 사용 하도록 설정 합니다.

### <a name="groups"></a>그룹

* 항상 그룹을 사용 하 여 클러스터를 배포 합니다.
* Azure AD를 사용 하 여 그룹 멤버 자격을 관리 합니다 (클러스터의 개별 서비스를 관리 하는 것 보다 간편해 진).

### <a name="user-accounts"></a>사용자 계정

* 각 시나리오에 고유한 사용자 계정을 사용 합니다. 예를 들어 가져오기에 대 한 계정을 사용 하 여 쿼리 또는 다른 처리 작업에 다른 계정을 사용 합니다.
* 개별 정책 대신 그룹 기반 레인저 정책을 사용 합니다.
* 더 이상 클러스터에 대 한 액세스 권한이 없어야 하는 사용자를 관리 하는 방법에 대 한 계획을 세워야 합니다.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS)는 도메인 가입, 그룹 정책, LDAP (lightweight Directory access protocol) 및 Windows Server Active Directory와 완전히 호환 되는 KERBEROS/NTLM 인증과 같은 관리 되는 도메인 서비스를 제공 합니다.

Azure AD DS는 보안 클러스터에서 도메인에 가입 하는 데 필요 합니다.
HDInsight는 너무 많은 오류 요소, 자격 증명 공유, DNS 권한 등을 도입 하므로 온-프레미스 도메인 컨트롤러 또는 사용자 지정 도메인 컨트롤러에 종속 되지 않습니다. 자세한 내용은 [Azure AD DS faq](../../active-directory-domain-services/faqs.md)를 참조 하세요.

### <a name="azure-ad-ds-instance"></a>Azure AD DS 인스턴스

* 을 사용 하 여 인스턴스를 만듭니다 `.onmicrosoft.com domain` . 이러한 방식으로 도메인을 처리 하는 여러 DNS 서버가 없습니다.
* LDAPS에 대해 자체 서명 된 인증서를 만들고 Azure AD DS에 업로드 합니다.
* 클러스터를 배포 하는 데 피어 링 가상 네트워크를 사용 합니다 (HDInsight ESP 클러스터를 배포 하는 많은 팀이 있는 경우 도움이 됨). 이렇게 하면 도메인 컨트롤러를 사용 하 여 가상 네트워크에서 포트 (NSGs)를 열 필요가 없습니다.
* 가상 네트워크에 대 한 DNS를 적절히 구성 합니다. 호스트 파일 항목 없이 Azure AD DS 도메인 이름이 확인 되어야 합니다.
* 아웃 바운드 트래픽을 제한 하는 경우 [HDInsight의 방화벽 지원](../hdinsight-restrict-outbound-traffic.md) 에 대 한 읽기 권한이 있는지 확인 합니다.

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Azure AD에서 Azure AD DS로 동기화 되는 속성

* Azure AD connect는 온-프레미스에서 Azure AD로 동기화 됩니다.
* Azure AD DS azure AD에서 동기화 됩니다.

Azure AD DS는 Azure AD의 개체를 주기적으로 동기화 합니다. Azure Portal의 Azure AD DS 블레이드에서 동기화 상태가 표시 됩니다. 동기화의 각 단계에서 고유 속성은 충돌이 발생 하 고 이름이 바뀔 수 있습니다. Azure AD에서 Azure AD DS로의 속성 매핑에 주의 합니다.

자세한 내용은 [AZURE AD UserPrincipalName 채우기](../../active-directory/hybrid/plan-connect-userprincipalname.md)및 [azure AD DS 동기화의 작동 방식](../../active-directory-domain-services/synchronization.md)을 참조 하세요.

### <a name="password-hash-sync"></a>암호 해시 동기화

* 암호는 다른 개체 유형과 다르게 동기화 됩니다. 되돌릴 수 없는 암호 해시만 Azure AD 및 Azure AD DS에서 동기화 됩니다.
* AD Connect를 통해 온-프레미스에서 Azure AD를 사용 하도록 설정 해야 합니다.
* Azure AD에서 Azure로의 AD DS 동기화는 자동으로 적용 됩니다 (대기 시간은 20 분 미만).
* 암호 해시는 암호가 변경 된 경우에만 동기화 됩니다. 암호 해시 동기화를 사용 하도록 설정 하면 기존 암호는 무효로 저장 될 때 자동으로 동기화 되지 않습니다. 암호를 변경 하면 암호 해시가 동기화 됩니다.

### <a name="computer-objects-location"></a>컴퓨터 개체 위치

각 클러스터는 단일 OU와 연결 됩니다. 내부 사용자는 OU에 프로 비전 됩니다. 모든 노드는 동일한 OU에 도메인 가입 됩니다.

### <a name="active-directory-administrative-tools"></a>Active Directory 관리 도구

Windows Server VM에 Active Directory 관리 도구를 설치 하는 방법에 대 한 단계는 [설치 관리 도구](../../active-directory-domain-services/tutorial-create-management-vm.md)를 참조 하세요.

## <a name="troubleshooting"></a>문제 해결

### <a name="cluster-creation-fails-repeatedly"></a>클러스터 만들기가 반복적으로 실패 함

가장 일반적인 이유는 다음과 같습니다.

* DNS 구성이 올바르지 않습니다. 클러스터 노드의 도메인 조인이 실패 합니다.
* NSGs가 너무 제한적 이어서 도메인 가입을 방지 합니다.
* 관리 Id에 충분 한 권한이 없습니다.
* 클러스터 이름은 처음 6 자 (다른 라이브 클러스터 또는 삭제 된 클러스터)에서 고유 하지 않습니다.

## <a name="authentication-setup-and-configuration"></a>인증 설정 및 구성

### <a name="user-principal-name-upn"></a>UPN(사용자 계정 이름)

* 모든 서비스에 대해 소문자를 사용 하십시오.-Upn은 ESP 클러스터에서 대/소문자를 구분 하지 않습니다.
* UPN 접두사는 Azure AD-DS에서 두 SAMAccountName 모두와 일치 해야 합니다. 메일 필드와 일치 하지 않아도 됩니다.

### <a name="ldap-properties-in-ambari-configuration"></a>Ambari 구성의 LDAP 속성

HDInsight 클러스터 구성에 영향을 주는 Ambari 속성의 전체 목록은 [AMBARI LDAP Authentication Setup](https://ambari.apache.org/1.2.1/installing-hadoop-using-ambari/content/ambari-chap2-4.html)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 Azure Active Directory Domain Services를 사용 하 여 Enterprise Security Package 구성](./apache-domain-joined-configure-using-azure-adds.md)

* [Azure Active Directory 사용자를 HDInsight 클러스터와 동기화](../hdinsight-sync-aad-users-to-cluster.md)합니다.
