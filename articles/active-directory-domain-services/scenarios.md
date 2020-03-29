---
title: Azure AD 도메인 서비스에 대한 일반적인 배포 시나리오 | 마이크로 소프트 문서
description: Azure Active Directory 도메인 서비스의 몇 가지 일반적인 시나리오 및 사용 사례에 대해 알아서 가치를 제공하고 비즈니스 요구 사항을 충족합니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ac67ef64ca4850c6e805b5314ace856114d889a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917233"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Azure Active Directory 도메인 서비스에 대한 일반적인 사용 사례 및 시나리오

Azure Active Directory 도메인 서비스(Azure AD DS)는 도메인 조인, 그룹 정책, LDAP(경량 디렉터리 액세스 프로토콜) 및 Kerberos/NTLM 인증과 같은 관리되는 도메인 서비스를 제공합니다. Azure AD DS는 기존 Azure AD 테넌트와 통합되므로 사용자가 기존 자격 증명을 사용하여 로그인할 수 있습니다. 클라우드에서 도메인 컨트롤러를 배포, 관리 및 패치할 필요 없이 이러한 도메인 서비스를 사용하면 온-프레미스 리소스를 Azure에 보다 원활하게 리프트 앤 시프트할 수 있습니다.

이 문서에서는 Azure AD DS가 가치를 제공하고 이러한 요구 사항을 충족하는 몇 가지 일반적인 비즈니스 시나리오를 간략하게 설명합니다.

## <a name="secure-administration-of-azure-virtual-machines"></a>Azure 가상 시스템의 보안 관리

단일 AD 자격 증명 집합을 사용할 수 있도록 Azure 가상 시스템(VM)을 Azure AD DS 관리 도메인에 조인할 수 있습니다. 이 방법을 사용하면 각 VM에서 로컬 관리자 계정을 유지 관리하거나 환경 간에 별도의 계정과 암호를 유지 관리하는 등의 자격 증명 관리 문제가 줄어듭니다.

Azure AD DS 관리 도메인에 조인된 VM은 그룹 정책을 사용하여 관리및 보호할 수도 있습니다. VM에 필요한 보안 기준을 적용하여 회사 보안 지침에 따라 보안 기준을 잠글 수 있습니다. 예를 들어 그룹 정책 관리 기능을 사용하여 VM에서 시작할 수 있는 응용 프로그램 유형을 제한할 수 있습니다.

![Azure 가상 머신의 간소화된 관리](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

일반적인 예제 시나리오를 살펴보겠습니다. 서버 및 기타 인프라가 단종에 도달하면 Contoso는 현재 온프레미스에서 호스팅되는 응용 프로그램을 클라우드로 이전하려고 합니다. 현재 IT 표준에 따라 회사 응용 프로그램을 호스팅하는 서버는 그룹 정책을 사용하여 도메인에 가입되고 관리되어야 합니다. Contoso의 IT 관리자는 사용자가 회사 자격 증명을 사용하여 로그인할 수 있으므로 관리를 더 쉽게 하기 위해 Azure에 배포된 VM을 도메인에 가입하는 것을 선호합니다. 도메인 에 가입된 경우 그룹 정책 개체(GPO)를 사용하여 필요한 보안 기준을 준수하도록 VM을 구성할 수도 있습니다. Contoso는 Azure에서 자체 도메인 컨트롤러를 배포, 모니터링 및 관리하지 않는 것을 선호합니다.

Azure AD DS는 이 사용 사례에 적합합니다. Azure AD DS 관리 도메인을 사용하면 VM을 도메인에 가입하고, 단일 자격 증명 집합을 사용하고, 그룹 정책을 적용할 수 있습니다. 관리되는 도메인은 도메인 컨트롤러를 직접 구성하고 유지 관리할 필요가 없습니다.

### <a name="deployment-notes"></a>배포 참고 사항

다음 배포 고려 사항은 이 예제 사용 사례에 적용됩니다.

* Azure AD DS 관리 도메인은 기본적으로 단일 평평한 조직 단위(OU) 구조를 사용합니다. 도메인에 가입한 모든 VM은 단일 OU에 있습니다. 원하는 경우 사용자 지정 OUs를 만들 수 있습니다.
* Azure AD DS는 사용자 및 컴퓨터 컨테이너에 대해 각각 기본 제공 GPO를 사용합니다. 추가 제어를 위해 사용자 지정 GPO를 만들고 사용자 지정 OUs에 지정할 수 있습니다.
* Azure AD DS는 기본 AD 컴퓨터 개체 스키마를 지원합니다. 컴퓨터 개체의 스키마를 확장할 수 없습니다.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>LDAP 바인딩 인증을 사용하는 리프트 앤 시프트 온-프레미스 애플리케이션

샘플 시나리오에서 Contoso에는 수년 전에 ISV에서 구입한 온-프레미스 응용 프로그램이 있습니다. 응용 프로그램은 현재 ISV에 의해 유지 관리 모드에 있으며 응용 프로그램에 대한 변경을 요청하는 것은 엄청나게 비용이 많이 듭니다. 이 응용 프로그램에는 웹 양식을 사용하여 사용자 자격 증명을 수집한 다음 온-프레미스 AD DS 환경에 LDAP 바인딩을 수행하여 사용자를 인증하는 웹 기반 프런트 엔드가 있습니다.

![LDAP 바인딩](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso는 이 응용 프로그램을 Azure로 마이그레이션하려고 합니다. 응용 프로그램은 변경 이 필요 없이 있는 대로 계속 작동해야 합니다. 또한 사용자는 추가 교육 없이 기존 회사 자격 증명을 사용하여 인증할 수 있어야 합니다. 응용 프로그램이 실행 중인 최종 사용자에게는 투명해야 합니다.

이 시나리오에서 Azure AD DS를 사용하면 응용 프로그램에서 인증 프로세스의 일부로 LDAP 바인딩을 수행할 수 있습니다. 레거시 온-프레미스 응용 프로그램은 Azure로 리프트 앤 시프트할 수 있으며 구성 또는 사용자 환경의 변경 없이 사용자를 원활하게 인증할 수 있습니다.

### <a name="deployment-notes"></a>배포 참고 사항

다음 배포 고려 사항은 이 예제 사용 사례에 적용됩니다.

* 응용 프로그램이 디렉터리로 수정/쓸 필요가 없는지 확인합니다. LDAP는 Azure AD DS 관리 도메인에 대한 쓰기 액세스가 지원되지 않습니다.
* Azure AD DS 관리 도메인에 대해 직접 암호를 변경할 수 없습니다. 최종 사용자가 Azure AD의 셀프 서비스 암호 변경 메커니즘을 사용하거나 온-프레미스 디렉터리에 대해 암호를 바꿀 수 있습니다. 그런 다음 이러한 변경 내용은 자동으로 동기화되고 Azure AD DS 관리 도메인에서 사용할 수 있습니다.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>LDAP 읽기를 사용하여 디렉터리에 액세스하는 리프트 앤 시프트 온-프레미스 애플리케이션

이전 예제 시나리오와 마찬가지로 Contoso에 거의 10년 전에 개발된 LOB(온-프레미스-LOB) 응용 프로그램이 있다고 가정해 보겠습니다. 이 응용 프로그램은 디렉토리 인식이며, AD DS에서 사용자에 대한 정보 / 속성을 읽을 LDAP를 사용하도록 설계되었습니다. 응용 프로그램은 특성을 수정하거나 디렉터리에 쓰지 않습니다.

Contoso는 이 응용 프로그램을 Azure로 마이그레이션하고 현재 이 응용 프로그램을 호스팅하는 에이징 온-프레미스 하드웨어를 폐기하려고 합니다. REST 기반 Microsoft 그래프 API와 같은 최신 디렉터리 API를 사용하도록 응용 프로그램을 다시 작성할 수 없습니다. 코드를 수정하거나 응용 프로그램을 다시 작성하지 않고 클라우드에서 실행되도록 응용 프로그램을 마이그레이션할 수 있는 리프트 앤 시프트 옵션이 필요합니다.

이 시나리오를 돕기 위해 Azure AD DS를 사용하면 응용 프로그램에서 관리되는 도메인에 대해 LDAP 읽기를 수행하여 필요한 특성 정보를 얻을 수 있습니다. 응용 프로그램을 다시 작성할 필요가 없으므로 Azure로 리프트 앤 시프트하면 사용자가 실행 위치가 변경된다는 것을 깨닫지 못하고 앱을 계속 사용할 수 있습니다.

### <a name="deployment-notes"></a>배포 참고 사항

다음 배포 고려 사항은 이 예제 사용 사례에 적용됩니다.

* 응용 프로그램이 디렉터리로 수정/쓸 필요가 없는지 확인합니다. LDAP는 Azure AD DS 관리 도메인에 대한 쓰기 액세스가 지원되지 않습니다.
* 응용 프로그램에 사용자 지정/확장된 Active Directory 스키마가 필요하지 않은지 확인합니다. 스키마 확장Azure AD DS에서 지원되지 않습니다.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>온-프레미스 서비스 또는 데몬 응용 프로그램을 Azure로 마이그레이션

일부 응용 프로그램에는 계층 중 하나가 데이터베이스와 같은 백 엔드 계층에 대한 인증된 호출을 수행해야 하는 여러 계층이 포함됩니다. AD 서비스 계정은 이러한 시나리오에서 일반적으로 사용됩니다. 응용 프로그램을 Azure로 리프트 앤 시프트하면 Azure AD DS를 사용하면 동일한 방식으로 서비스 계정을 계속 사용할 수 있습니다. 온-프레미스 디렉터리에서 Azure AD로 동기화된 동일한 서비스 계정을 사용하거나 사용자 지정 OU를 만든 다음 해당 OU에서 별도의 서비스 계정을 만들 수 있습니다. 두 방법 모두 응용 프로그램이 다른 계층 및 서비스에 대한 인증된 호출을 만드는 동일한 방식으로 계속 작동합니다.

![WIA를 사용하는 서비스 계정](./media/active-directory-domain-services-scenarios/wia-service-account.png)

이 예제 시나리오에서 Contoso에는 웹 프런트 엔드, SQL 서버 및 백엔드 FTP 서버가 포함된 사용자 지정 소프트웨어 볼트 응용 프로그램이 있습니다. 서비스 계정을 사용하는 Windows 통합 인증은 FTP 서버에 대한 웹 프런트 엔드를 인증합니다. 웹 프런트 엔드는 서비스 계정으로 실행되도록 설정됩니다. 백 엔드 서버는 웹 프런트 엔드에 대한 서비스 계정에서 액세스 권한을 부여하도록 구성됩니다. Contoso는 이 응용 프로그램을 Azure로 이동하기 위해 클라우드에서 자체 도메인 컨트롤러 VM을 배포하고 관리하기를 원하지 않습니다.

이 시나리오에서는 웹 프런트 엔드, SQL 서버 및 FTP 서버를 호스팅하는 서버를 Azure VM으로 마이그레이션하고 Azure AD DS 관리 도메인에 조인할 수 있습니다. 그런 다음 VM은 Azure AD Connect를 사용하여 Azure AD를 통해 동기화되는 앱의 인증 목적을 위해 온-프레미스 디렉터리에서 동일한 서비스 계정을 사용할 수 있습니다.

### <a name="deployment-notes"></a>배포 참고 사항

다음 배포 고려 사항은 이 예제 사용 사례에 적용됩니다.

* 응용 프로그램이 인증을 위해 사용자 이름과 암호를 사용해야 하는지 확인합니다. 인증서 또는 스마트 카드 기반 인증Azure AD DS에서 지원되지 않습니다.
* Azure AD DS 관리 도메인에 대해 직접 암호를 변경할 수 없습니다. 최종 사용자가 Azure AD의 셀프 서비스 암호 변경 메커니즘을 사용하거나 온-프레미스 디렉터리에 대해 암호를 바꿀 수 있습니다. 그런 다음 이러한 변경 내용은 자동으로 동기화되고 Azure AD DS 관리 도메인에서 사용할 수 있습니다.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Azure에서 Windows Server 원격 데스크톱 서비스 배포

Azure AD DS를 사용하여 Azure에 배포된 원격 데스크톱 서버에 관리되는 도메인 서비스를 제공할 수 있습니다. 이 배포 시나리오에 대한 자세한 내용은 [Azure AD 도메인 서비스를 RDS 배포와 통합하는 방법을 참조하세요.][windows-rds]

## <a name="domain-joined-hdinsight-clusters"></a>도메인에 가입된 HDInsight 클러스터

아파치 레인저를 사용하도록 설정한 Azure AD DS 관리 도메인에 가입된 Azure HDInsight 클러스터를 설정할 수 있습니다. 아파치 레인저를 통해 Hive 정책을 만들고 적용할 수 있으며 데이터 과학자와 같은 사용자가 Excel 또는 Tableau와 같은 ODBC 기반 도구를 사용하여 Hive에 연결할 수 있습니다. HBase, 스파크, 스톰 과 같은 다른 워크로드를 도메인에 가입한 HDInsight에 추가하기 위해 계속 노력하고 있습니다.

이 배포 시나리오에 대한 자세한 내용은 [도메인 에 가입한 HDInsight 클러스터를 구성하는 방법을 참조하세요.][hdinsight]

## <a name="next-steps"></a>다음 단계

시작하려면 [Azure Active Directory 도메인 서비스 인스턴스를 만들고 구성합니다.][tutorial-create-instance]

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
