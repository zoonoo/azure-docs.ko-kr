---
title: 일반적인 Azure AD Domain Services용 배포 시나리오 | Microsoft Docs
description: 가치를 제공하고 비즈니스 요구를 충족하는 Azure Active Directory Domain Services에 대한 몇 가지 일반적인 시나리오 및 사용 사례에 대해 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: justinha
ms.openlocfilehash: d33698ed2f9ac53aae3c836acd54f19a4b72ceef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619031"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services에 대한 일반적인 사용 사례 및 시나리오

Azure AD DS(Azure Active Directory Domain Services)는 도메인 조인, 그룹 정책, LDAP(Lightweight Directory Access Protocol) 및 Kerberos/NTLM 인증과 같은 관리되는 도메인 서비스를 제공합니다. Azure AD DS는 기존 Azure AD 테넌트와 통합되므로 사용자가 기존 자격 증명을 사용하여 로그인할 수 있습니다. 클라우드에서 도메인 컨트롤러를 배포, 관리 및 패치하지 않고도 이러한 도메인 서비스를 사용할 수 있습니다. 이는 온-프레미스 리소스를 Azure로 더 원활하게 리프트 앤 시프트하는 기능을 제공합니다.

이 문서에서는 Azure AD DS가 가치를 제공하고 이러한 요구를 충족하는 일반적인 비즈니스 시나리오를 간략하게 설명합니다.

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>클라우드에서 ID 솔루션을 제공하는 일반적인 방법

기존 워크로드를 클라우드로 마이그레이션하는 경우 디렉터리 인식 애플리케이션에서 온-프레미스 AD DS 디렉터리에 대한 읽기 또는 쓰기 액세스에 LDAP을 사용할 수 있습니다. Windows Server에서 실행되는 애플리케이션은 일반적으로 도메인에 가입된 VM(가상 머신)에 배포되므로 그룹 정책을 사용하여 안전하게 관리할 수 있습니다. 최종 사용자를 인증하기 위해 애플리케이션에서 Kerberos 또는 NTLM 인증과 같은 Windows 통합 인증을 사용할 수도 있습니다.

IT 관리자는 다음 솔루션 중 하나를 사용하여 ID 서비스를 Azure에서 실행되는 애플리케이션에 제공하는 경우가 많습니다.

* Azure에서 실행되는 워크로드와 온-프레미스 AD DS 환경 간에 사이트 간 VPN 연결을 구성합니다.
    * 그러면 온-프레미스 도메인 컨트롤러에서 VPN 연결을 통해 인증을 제공합니다.
* 온-프레미스에서 AD DS 도메인/포리스트를 확장하기 위해 Azure VM(가상 머신)을 사용하여 복제본 도메인 컨트롤러를 만듭니다.
    * Azure VM에서 실행되는 도메인 컨트롤러는 인증을 제공하고, 온-프레미스 AD DS 환경 간에 디렉터리 정보를 복제합니다.
* Azure VM에서 실행되는 도메인 컨트롤러를 사용하여 Azure에서 독립 실행형 AD DS 환경을 배포합니다.
    * Azure VM에서 실행되는 도메인 컨트롤러는 인증을 제공하지만, 온-프레미스 AD DS 환경에서 복제되는 디렉터리 정보가 없습니다.

이러한 방법을 사용하면 온-프레미스 디렉터리에 대한 VPN 연결로 인해 애플리케이션이 일시적인 네트워크 결함 또는 중단에 취약해질 수 있습니다. Azure에서 VM을 사용하여 도메인 컨트롤러를 배포하는 경우 IT 팀에서 VM을 관리하고 이러한 컨트롤러를 보호, 패치, 모니터링, 백업 및 문제를 해결해야 합니다.

Azure AD DS는 ID 서비스를 제공하기 위해 온-프레미스 AD DS 환경에 대한 VPN 연결을 다시 만들거나 Azure에서 VM을 실행하고 관리해야 하는 대안을 제공합니다. 관리형 서비스인 Azure AD DS는 하이브리드 환경과 클라우드 전용 환경 모두에 대한 통합 ID 솔루션을 만들 때의 복잡성을 줄입니다.

> [!div class="nextstepaction"]
> [Azure AD DS와 Azure VM 또는 온 프레미스에서 Azure AD 및 자체 관리 AD DS를 비교하세요.][compare]

## <a name="azure-ad-ds-for-hybrid-organizations"></a>하이브리드 조직을 위한 Azure AD DS

많은 조직에서는 클라우드 및 온-프레미스 애플리케이션 워크로드가 모두 포함된 하이브리드 인프라를 실행합니다. 리프트 및 시프트 전략의 일환으로 Azure로 마이그레이션된 레거시 애플리케이션은 기존 LDAP 연결을 사용하여 ID 정보를 제공할 수 있습니다. 이 하이브리드 인프라를 지원하기 위해 온-프레미스 AD DS 환경의 ID 정보는 Azure AD 테넌트에 동기화될 수 있습니다. 그러면 Azure AD DS에서 온-프레미스 디렉터리 서비스에 대한 애플리케이션 연결을 다시 구성하고 관리할 필요 없이 Azure에서 ID 원본을 이러한 레거시 애플리케이션에 제공합니다.

온-프레미스 리소스와 Azure 리소스를 모두 실행하는 하이브리드 조직인 Litware Corporation의 예를 살펴보겠습니다.

![하이브리드 조직을 위한 Azure Active Directory Domain Services(온-프레미스 동기화 포함)](./media/overview/synced-tenant.png)

* 도메인 서비스가 필요한 애플리케이션 및 서버 워크로드가 Azure의 가상 네트워크에 배포됩니다.
    * 여기에는 리프트 및 시프트 전략의 일환으로 Azure로 마이그레이션된 레거시 애플리케이션이 포함될 수 있습니다.
* ID 정보를 온-프레미스 디렉터리에서 Azure AD 테넌트로 동기화하기 위해 Litware Corporation에서 [Azure AD Connect][azure-ad-connect]를 배포합니다.
    * 동기화되는 ID 정보에는 사용자 계정 및 그룹 멤버 자격이 포함됩니다.
* Litware의 IT 팀에서 이 가상 네트워크 또는 피어링된 가상 네트워크에서 해당 Azure AD 테넌트에 Azure AD DS를 사용하도록 설정합니다.
* 그러면 Azure 가상 네트워크에 배포된 애플리케이션과 VM에서 도메인 조인, LDAP 읽기, LDAP 바인딩, NTLM/Kerberos 인증 및 그룹 정책과 같은 Azure AD DS 기능을 사용할 수 있습니다.

> [!IMPORTANT]
> Azure AD Connect는 온-프레미스 AD DS 환경과의 동기화를 위해서만 설치되고 구성되어야 합니다. 개체를 Azure AD로 개체를 다시 동기화하기 위해 관리되는 도메인에 Azure AD Connect를 설치하는 것은 지원되지 않습니다.

## <a name="azure-ad-ds-for-cloud-only-organizations"></a>클라우드 전용 조직을 위한 Azure AD DS

클라우드 전용 Azure AD 테넌트에는 온-프레미스 ID 원본이 없습니다. 예를 들어 사용자 계정 및 그룹 멤버 자격은 Azure AD에서 직접 만들고 관리합니다.

이제 ID에 Azure AD를 사용하는 클라우드 전용 조직인 Contoso의 예를 살펴보겠습니다. 모든 사용자 ID, 해당 자격 증명 및 그룹 멤버 자격은 Azure AD에서 만들고 관리합니다. 온-프레미스에서 ID 정보를 동기화하기 위한 Azure AD Connect의 추가 구성이 없습니다.

![클라우드 전용 조직을 위한 Azure Active Directory Domain Services(온-프레미스 동기화 없음)](./media/overview/cloud-only-tenant.png)

* 도메인 서비스가 필요한 애플리케이션 및 서버 워크로드가 Azure의 가상 네트워크에 배포됩니다.
* Contoso의 IT 팀에서 이 가상 네트워크 또는 피어링된 가상 네트워크에서 해당 Azure AD 테넌트에 Azure AD DS를 사용하도록 설정합니다.
* 그러면 Azure 가상 네트워크에 배포된 애플리케이션과 VM에서 도메인 조인, LDAP 읽기, LDAP 바인딩, NTLM/Kerberos 인증 및 그룹 정책과 같은 Azure AD DS 기능을 사용할 수 있습니다.

## <a name="secure-administration-of-azure-virtual-machines"></a>Azure 가상 머신의 안전한 관리

단일 AD 자격 증명 집합을 사용할 수 있도록 Azure VM(가상 머신)이 Azure AD DS 관리되는 도메인에 가입될 수 있습니다. 해당 접근 방식은 각 VM의 로컬 관리자 계정의 유지 관리 또는 환경마다 다른 계정 및 암호와 같은 자격 증명 관리 문제를 줄여 줍니다.

관리되는 도메인에 가입된 VM은 그룹 정책을 사용하여 관리 및 보호될 수도 있습니다. 또한 VM에 필요한 보안 기준을 적용할 수 있으며 회사 보안 지침에 따라 잠글 수 있습니다. 예를 들어 그룹 정책 관리 기능을 사용하여 이러한 VM에서 시작될 수 있는 애플리케이션의 종류를 제한할 수 있습니다.

![Azure 가상 머신의 간소화된 관리](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

일반적인 예제 시나리오를 살펴보겠습니다. 서버 및 기타 인프라가 수명 종료에 도달하여 Contoso는 현재 온-프레미스에서 호스트되는 애플리케이션을 클라우드로 이동하려 합니다. 현재 해당 IT 표준은 기업 애플리케이션을 호스트하는 서버가 그룹 정책을 사용하여 도메인에 가입되고 관리되어야 한다고 규정합니다.

Contoso의 IT 관리자는 사용자가 회사 자격 증명을 사용하여 로그인할 수 있으므로 관리를 용이하게 하기 위해 Azure에 배포된 VM의 도메인 가입을 선호합니다. 도메인에 가입되는 경우 GPO(그룹 정책 개체)를 사용하여 필요한 보안 기준을 준수하도록 VM을 구성할 수도 있습니다. Contoso는 Azure에서 자체 도메인 컨트롤러를 배포, 모니터링 및 관리하지 않는 편을 선호합니다.

Azure AD DS는 이 사용 사례에 매우 적합합니다. 관리되는 도메인을 사용하여 VM을 도메인에 가입시키고, 단일 자격 증명 집합을 사용하고, 그룹 정책을 적용할 수 있습니다. 또한 관리되는 도메인이므로 도메인 컨트롤러를 직접 구성하고 유지 관리할 필요가 없습니다.

### <a name="deployment-notes"></a>배포 참고 사항

이 예제 사용 사례에 적용되는 배포 고려 사항은 다음과 같습니다.

* 관리되는 도메인은 기본적으로 단일 플랫 OU(조직 구성 단위) 구조를 사용합니다. 모든 도메인 조인 VM은 단일 OU에 있습니다. 원하는 경우 [사용자 지정 OU][custom-ou]를 만들 수 있습니다.
* Azure AD DS는 개별 사용자 및 컴퓨터 컨테이너에 기본 제공 GPO를 사용합니다. 추가 제어를 위해 [사용자 지정 GPO를 만들고][create-gpo] 이를 사용자 지정 OU의 대상으로 지정할 수 있습니다.
* Azure AD DS는 기본 AD 컴퓨터 개체 스키마를 지원합니다. 컴퓨터 개체의 스키마를 확장할 수 없습니다.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>LDAP 바인딩 인증을 사용하는 온-프레미스 애플리케이션 리프트 앤 시프트

샘플 시나리오에서 Contoso에는 ISV에서 몇 년 전에 구입한 온-프레미스 애플리케이션이 있습니다. 애플리케이션은 현재 ISV에서 유지 관리 모드에 있으며 애플리케이션에 대한 변경 요청은 엄청나게 많은 비용이 듭니다. 애플리케이션은 웹 양식을 사용하여 사용자 자격 증명을 수집하는 웹 기반 프런트 엔드를 가지고 온-프레미스 AD DS 환경에 LDAP 바인딩을 수행하여 사용자를 인증합니다.

![LDAP 바인딩](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso는 Azure에 이 애플리케이션을 마이그레이션하려고 합니다. 애플리케이션은 변경이 필요 없이 계속해서 그대로 작동해야 합니다. 또한 사용자는 기존 회사 자격 증명을 사용하여 추가 교육 없이 인증할 수 있어야 합니다. 인증은 애플리케이션이 실행되는 최종 사용자에게 투명해야 합니다.

이 시나리오에서 Azure AD DS를 사용하면 애플리케이션에서 LDAP 바인딩을 인증 프로세스의 일부로 수행할 수 있습니다. 레거시 온-프레미스 애플리케이션을 Azure로 리프트 앤 시프트하면 구성 또는 사용자 환경을 변경하지 않고 계속해서 원활하게 사용자를 인증할 수 있습니다.

### <a name="deployment-notes"></a>배포 참고 사항

이 예제 사용 사례에 적용되는 배포 고려 사항은 다음과 같습니다.

* 애플리케이션이 디렉터리에 수정/쓰기를 수행하지 않아도 됩니다. 관리되는 도메인에 대한 LDAP 쓰기 액세스는 지원되지 않습니다.
* 사용자는 관리되는 도메인에 대해 직접 암호를 변경할 수 없습니다. 최종 사용자가 [Azure AD의 셀프 서비스 암호 변경 메커니즘][sspr]을 사용하거나 온-프레미스 디렉터리에 대해 암호를 바꿀 수 있습니다. 그러면 이러한 변경은 관리되는 도메인에서 자동으로 동기화되고 사용할 수 있습니다.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>LDAP 읽기를 사용하여 디렉터리에 액세스하는 온-프레미스 애플리케이션 리프트 앤 시프트

이전 예제 시나리오와 마찬가지로 Contoso가 거의 10년 전에 개발된 온-프레미스 LOB(기간 업무) 애플리케이션을 가지고 있다고 가정해 보겠습니다. 이 애플리케이션은 디렉터리를 인식하며 LDAP를 사용하여 AD DS에서 사용자에 대한 정보/특성을 읽도록 설계되었습니다. 애플리케이션은 특성을 수정하지 않고 또는 디렉터리에 쓰지 않습니다.

Contoso는 Azure에 해당 애플리케이션을 마이그레이션하고 현재 해당 애플리케이션을 호스트하는 오래된 온-프레미스 하드웨어의 사용을 중지하려고 합니다. 애플리케이션은 REST 기반 Microsoft Graph API와 같은 최신 디렉터리 API를 사용하기 위해 다시 작성할 수 없습니다. 코드를 수정하거나 애플리케이션을 다시 작성하지 않고 클라우드에서 실행하기 위해 애플리케이션을 마이그레이션할 수 있는 리프트 앤 시프트 옵션이 필요합니다.

이 시나리오에서 도움이 되도록 Azure AD DS를 사용하면 애플리케이션에서 관리되는 도메인에 LDAP 읽기를 수행하여 필요한 특성 정보를 가져올 수 있습니다. 애플리케이션을 다시 작성하지 않아도 되므로 Azure로 리프트 앤 시프트하면 앱의 실행 위치가 바뀌더라도 사용자가 앱을 계속 사용할 수 있습니다.

### <a name="deployment-notes"></a>배포 참고 사항

이 예제 사용 사례에 적용되는 배포 고려 사항은 다음과 같습니다.

* 애플리케이션이 디렉터리에 수정/쓰기를 수행하지 않아도 됩니다. 관리되는 도메인에 대한 LDAP 쓰기 액세스는 지원되지 않습니다.
* 애플리케이션에 사용자 지정/확장된 Active Directory 스키마가 필요하지 않습니다. Azure AD DS에서 스키마 확장이 지원되지 않습니다.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Azure에 온-프레미스 서비스 또는 데몬 애플리케이션 마이그레이션

일부 애플리케이션에는 계층 중 하나가 데이터베이스와 같은 백 엔드 계층에 대해 인증된 호출을 수행해야 하는 여러 계층이 포함되어 있습니다. 해당 시나리오에서는 AD 서비스 계정이 일반적으로 사용됩니다. 애플리케이션을 Azure로 리프트 앤 시프트하는 경우 Azure AD DS에서 동일한 방식으로 서비스 계정을 계속 사용할 수 있습니다. 온-프레미스 디렉터리에서 Azure AD로 동기화되는 동일한 서비스 계정을 사용하도록 선택하거나 사용자 지정 OU를 만든 다음 해당 OU에 별도의 서비스 계정을 만들 수 있습니다. 어느 방법을 사용하든 애플리케이션은 다른 계층 및 서비스에 대해 인증된 호출을 수행하기 위해 계속해서 동일한 방식으로 작동합니다.

![WIA를 사용하는 서비스 계정](./media/active-directory-domain-services-scenarios/wia-service-account.png)

이 예제 시나리오에서 Contoso는 웹 프런트 엔드, SQL Server 및 백 엔드 FTP 서버를 포함하는 사용자 지정 소프트웨어 자격 증명 모음 애플리케이션을 가지고 있습니다. 서비스 계정을 사용하는 Windows 통합된 인증이 FTP 서버에 웹 프런트 엔드를 인증하는 데 사용됩니다. 웹 프런트 엔드는 서비스 계정으로 실행되도록 설정됩니다. 백 엔드 서버는 웹 프런트 엔드에 대한 서비스 계정에서 액세스 권한을 부여하도록 구성됩니다. Contoso는 이 애플리케이션을 Azure로 이동하기 위해 클라우드에서 자체 도메인 컨트롤러 VM을 배포하고 관리하지 않으려고 합니다.

이 시나리오에서 웹 프런트 엔드, SQL Server 및 FTP 서버를 호스트하는 서버는 Azure VM으로 마이그레이션되고 관리되는 도메인에 가입될 수 있습니다. 그런 다음 VM은 앱의 인증 목적으로 온-프레미스 디렉터리에서 동일한 서비스 계정을 사용할 수 있습니다. 이는 Azure AD Connect을 사용하여 Azure AD를 통해 동기화됩니다.

### <a name="deployment-notes"></a>배포 참고 사항

이 예제 사용 사례에 적용되는 배포 고려 사항은 다음과 같습니다.

* 애플리케이션에서 인증을 위해 사용자 이름과 암호를 사용하는지 확인합니다. 인증서 또는 스마트 카드 기반 인증은 Azure AD DS에서 지원되지 않습니다.
* 사용자는 관리되는 도메인에 대해 직접 암호를 변경할 수 없습니다. 최종 사용자가 [Azure AD의 셀프 서비스 암호 변경 메커니즘][sspr]을 사용하거나 온-프레미스 디렉터리에 대해 암호를 바꿀 수 있습니다. 그러면 이러한 변경은 관리되는 도메인에서 자동으로 동기화되고 사용할 수 있습니다.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Azure에서 Windows Server 원격 데스크톱 서비스 배포

Azure AD DS를 사용하여 Azure에 배포된 원격 데스크톱 서버에 관리되는 도메인 서비스를 제공할 수 있습니다.

이 배포 시나리오에 대한 자세한 내용은 [RDS 배포와 Azure AD Domain Services를 통합하는 방법][windows-rds]을 참조하세요.

## <a name="domain-joined-hdinsight-clusters"></a>도메인에 가입된 HDInsight 클러스터

Apache Ranger를 사용하여 관리되는 도메인에 가입된 Azure HDInsight 클러스터를 설정할 수 있습니다. Apache Ranger를 통해 Hive 정책을 생성 및 적용하고, 사용자(예: 데이터 과학자)가 Excel 또는 Tableau 등의 ODBC 기반 도구를 사용하여 Hive에 연결하도록 허용할 수 있습니다. Microsoft는 HBase, Spark, Storm과 같은 다른 워크로드를 도메인에 가입된 HDInsight에 추가하는 작업을 계속하고 있습니다.

이 배포 시나리오에 대한 자세한 내용은 [도메인에 가입된 HDInsight 클러스터를 구성하는 방법][hdinsight]을 참조하세요.

## <a name="next-steps"></a>다음 단계

시작하려면 [Azure Active Directory Domain Services 관리되는 도메인 생성 및 구성][tutorial-create-instance]을 참조하세요.

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[tutorial-create-instance]: tutorial-create-instance.md
[custom-ou]: create-ou.md
[create-gpo]: manage-group-policy.md
[sspr]: ../active-directory/authentication/overview-authentication.md#self-service-password-reset
[compare]: compare-identity-solutions.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds