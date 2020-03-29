---
title: 클래식 가상 네트워크에서 Azure AD 도메인 서비스 마이그레이션 | 마이크로 소프트 문서
description: 기존 Azure AD 도메인 서비스 관리 되는 도메인 인스턴스를 클래식 가상 네트워크 모델에서 리소스 관리자 기반 가상 네트워크로 마이그레이션 하는 방법을 알아봅니다.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: e7caacf23cb489da6f9f85748ae839bc4babff8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917308"
---
# <a name="migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Azure AD 도메인 서비스를 클래식 가상 네트워크 모델에서 리소스 관리자로 마이그레이션

Azure Active Directory 도메인 서비스(AD DS)는 현재 클래식 가상 네트워크 모델을 사용하는 고객을 리소스 관리자 가상 네트워크 모델로 일회성 이동을 지원합니다. Resource Manager 배포 모델을 사용하는 Azure AD DS 관리 도메인은 세분화된 암호 정책, 감사 로그 및 계정 잠금 보호와 같은 추가 기능을 제공합니다.

이 문서에서는 마이그레이션의 이점과 고려 사항을 간략하게 설명한 다음 기존 Azure AD DS 인스턴스를 성공적으로 마이그레이션하는 데 필요한 단계를 설명합니다.

> [!NOTE]
> 2017년에 Azure AD 도메인 서비스를 Azure 리소스 관리자 네트워크에서 호스트할 수 있게 되었습니다. 그 이후로 Azure Resource Manager의 최신 기능을 사용하여 보다 안전한 서비스를 빌드할 수 있었습니다. Azure 리소스 관리자 배포는 클래식 배포를 완전히 대체하므로 Azure AD DS 클래식 가상 네트워크 배포는 2023년 3월 1일에 사용 중지됩니다.
>
> 자세한 내용은 공식 [사용 중단 공지를](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/) 참조하십시오.

## <a name="overview-of-the-migration-process"></a>마이그레이션 프로세스 개요

마이그레이션 프로세스는 클래식 가상 네트워크에서 실행되는 기존 Azure AD DS 인스턴스를 사용하여 기존 리소스 관리자 가상 네트워크로 이동합니다. 마이그레이션은 PowerShell을 사용하여 수행되며 *준비* *및*마이그레이션이라는 두 가지 주요 실행 단계가 있습니다.

![Azure AD DS에 대한 마이그레이션 프로세스 개요](media/migrate-from-classic-vnet/migration-overview.png)

*준비* 단계에서 Azure AD DS는 도메인을 백업하여 관리되는 도메인에 동기화된 사용자, 그룹 및 암호의 최신 스냅숏을 가져옵니다. 그런 다음 동기화가 비활성화되고 Azure AD DS 관리 도메인을 호스팅하는 클라우드 서비스가 삭제됩니다. 준비 단계에서 Azure AD DS 관리 도메인은 사용자를 인증할 수 없습니다.

![Azure AD DS 마이그레이션준비 단계](media/migrate-from-classic-vnet/migration-preparation.png)

*마이그레이션* 단계에서는 클래식 Azure AD DS 관리 도메인의 도메인 컨트롤러에 대한 기본 가상 디스크가 복사되어 리소스 관리자 배포 모델을 사용하여 VM을 만듭니다. 그런 다음 LDAPS 및 DNS 구성을 포함하는 Azure AD DS 관리 도메인이 다시 만들어집니다. Azure AD에 대한 동기화가 다시 시작되고 LDAP 인증서가 복원됩니다. Azure AD DS 관리 도메인에 컴퓨터를 다시 조인할 필요가 없습니다.

![Azure AD DS 마이그레이션](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>마이그레이션 혜택

이 마이그레이션 프로세스를 사용하여 Azure AD DS 관리 도메인을 이동하는 경우 관리되는 도메인에 컴퓨터를 다시 조인하거나 Azure AD DS 인스턴스를 삭제하고 처음부터 다시 만들 필요가 없습니다. VM은 마이그레이션 프로세스가 끝날 때 Azure AD DS 관리 도메인에 계속 조인됩니다.

마이그레이션 후 Azure AD DS는 다음과 같은 Resource Manager 가상 네트워크를 사용하는 도메인에서만 사용할 수 있는 많은 기능을 제공합니다.

* 세분화된 암호 정책 지원.
* AD 계정 잠금 보호.
* Azure AD DS 관리 도메인의 경고에 대한 전자 메일 알림입니다.
* Azure 모니터를 사용하여 로그를 감사합니다.
* Azure 파일 통합
* HD 인사이트 통합

리소스 관리자 가상 네트워크를 사용하는 Azure AD DS 관리 도메인을 사용하면 최신 기능을 최신 상태로 유지할 수 있습니다. 클래식 가상 네트워크를 사용하는 Azure AD DS에 대한 지원은 나중에 더 이상 사용되지 않습니다.

## <a name="example-scenarios-for-migration"></a>마이그레이션을 위한 예제 시나리오

Azure AD DS 관리 도메인을 마이그레이션하기 위한 몇 가지 일반적인 시나리오에는 다음 예제가 포함됩니다.

> [!NOTE]
> 성공적인 마이그레이션을 확인할 때까지 Classic 가상 네트워크를 변환하지 마십시오. 가상 네트워크를 변환하면 마이그레이션 및 확인 단계에서 문제가 있는 경우 Azure AD DS 관리 도메인을 롤백하거나 복원하는 옵션이 제거됩니다.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Azure AD DS를 기존 리소스 관리자 가상 네트워크로 마이그레이션하는 것이 좋습니다(권장).

일반적인 시나리오는 이미 다른 기존 클래식 리소스를 리소스 관리자 배포 모델 및 가상 네트워크로 이동한 경우입니다. 그런 다음 피어링은 리소스 관리자 가상 네트워크에서 Azure AD DS를 계속 실행하는 클래식 가상 네트워크로 사용됩니다. 이 방법을 사용하면 Resource Manager 응용 프로그램 및 서비스가 클래식 가상 네트워크에서 Azure AD DS 관리 도메인의 인증 및 관리 기능을 사용할 수 있습니다. 마이그레이션된 후에는 리소스 관리자 배포 모델 및 가상 네트워크를 사용하여 모든 리소스가 실행됩니다.

![Azure AD DS를 기존 리소스 관리자 가상 네트워크로 마이그레이션](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

이 예제 마이그레이션 시나리오와 관련된 상위 단계는 다음과 같습니다.

1. Classic 가상 네트워크에서 구성된 기존 VPN 게이트웨이 또는 가상 네트워크 피어링을 제거합니다.
1. 이 문서에서 설명하는 단계를 사용하여 Azure AD DS 관리 도메인을 마이그레이션합니다.
1. 성공적인 마이그레이션을 테스트하고 확인한 다음 Classic 가상 네트워크를 삭제합니다.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Azure AD DS를 포함한 여러 리소스 마이그레이션

이 예제 시나리오에서는 Azure AD DS 및 기타 관련 리소스를 클래식 배포 모델에서 리소스 관리자 배포 모델로 마이그레이션합니다. Azure AD DS 관리 도메인과 함께 클래식 가상 네트워크에서 일부 리소스가 계속 실행되는 경우 리소스 관리자 배포 모델로 마이그레이션하면 모두 이점을 얻을 수 있습니다.

![여러 리소스를 리소스 관리자 배포 모델로 마이그레이션](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

이 예제 마이그레이션 시나리오와 관련된 상위 단계는 다음과 같습니다.

1. Classic 가상 네트워크에서 구성된 기존 VPN 게이트웨이 또는 가상 네트워크 피어링을 제거합니다.
1. 이 문서에서 설명하는 단계를 사용하여 Azure AD DS 관리 도메인을 마이그레이션합니다.
1. 클래식 가상 네트워크와 리소스 관리자 네트워크 간에 가상 네트워크 피어링을 설정합니다.
1. 성공적인 마이그레이션을 테스트하고 확인합니다.
1. [VM과 같은 추가 클래식 리소스를 이동합니다.][migrate-iaas]

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Azure AD DS를 마이그레이션하지만 클래식 가상 네트워크에 다른 리소스를 유지

이 예제 시나리오를 사용하면 한 세션에서 최소 가동 중지 시간이 있습니다. Azure AD DS를 리소스 관리자 가상 네트워크로만 마이그레이션하고 기존 리소스를 클래식 배포 모델 및 가상 네트워크에 유지합니다. 다음 유지 관리 기간에는 원하는 대로 클래식 배포 모델 및 가상 네트워크에서 추가 리소스를 마이그레이션할 수 있습니다.

![Azure AD DS만 리소스 관리자 배포 모델로 마이그레이션](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

이 예제 마이그레이션 시나리오와 관련된 상위 단계는 다음과 같습니다.

1. Classic 가상 네트워크에서 구성된 기존 VPN 게이트웨이 또는 가상 네트워크 피어링을 제거합니다.
1. 이 문서에서 설명하는 단계를 사용하여 Azure AD DS 관리 도메인을 마이그레이션합니다.
1. 클래식 가상 네트워크와 새 Resource Manager 가상 네트워크 간에 가상 네트워크 피어링을 설정합니다.
1. 나중에 필요에 따라 Classic 가상 네트워크에서 [추가 리소스를 마이그레이션합니다.][migrate-iaas]

## <a name="before-you-begin"></a>시작하기 전에

Azure AD DS 관리 도메인을 준비하고 마이그레이션할 때 인증 및 관리 서비스의 가용성과 관련된 몇 가지 고려 사항이 있습니다. Azure AD DS 관리 도메인은 마이그레이션 하는 동안 동안 사용할 수 없습니다. Azure AD DS를 기반으로 하는 응용 프로그램 및 서비스는 마이그레이션 하는 동안 가동 중지 시간이 발생 합니다.

> [!IMPORTANT]
> 마이그레이션 프로세스를 시작하기 전에 이 마이그레이션 문서와 지침을 모두 읽어보십시오. 마이그레이션 프로세스는 기간 동안 Azure AD DS 도메인 컨트롤러의 가용성에 영향을 줍니다. 사용자, 서비스 및 응용 프로그램은 마이그레이션 프로세스 중에 관리되는 도메인에 대해 인증할 수 없습니다.

### <a name="ip-addresses"></a>IP 주소

마이그레이션 후 Azure AD DS 관리 도메인 변경에 대 한 도메인 컨트롤러 IP 주소입니다. 이 변경에는 보안 LDAP 끝점에 대한 공용 IP 주소가 포함됩니다. 새 IP 주소는 리소스 관리자 가상 네트워크의 새 서브넷의 주소 범위 내에 있습니다.

롤백의 경우 롤백 후 IP 주소가 변경될 수 있습니다.

Azure AD DS는 일반적으로 주소 범위에서 사용 가능한 처음 두 IP 주소를 사용하지만 보장되지는 않습니다. 마이그레이션 후 사용할 IP 주소를 현재 지정할 수 없습니다.

### <a name="downtime"></a>가동 중지 시간

마이그레이션 프로세스에는 도메인 컨트롤러가 잠시 동안 오프라인 상태가 됩니다. Azure AD DS가 리소스 관리자 배포 모델 및 가상 네트워크로 마이그레이션되는 동안 도메인 컨트롤러에 액세스할 수 없습니다. 평균적으로 가동 중지 시간은 약 1~3시간입니다. 이 기간은 도메인 컨트롤러가 오프라인 상태가 되는 시점부터 첫 번째 도메인 컨트롤러가 다시 온라인 상태가 되는 시점까지입니다. 이 평균에는 두 번째 도메인 컨트롤러가 복제하는 데 걸리는 시간 또는 추가 리소스를 리소스 관리자 배포 모델로 마이그레이션하는 데 걸리는 시간이 포함되지 않습니다.

### <a name="account-lockout"></a>계정 잠금

클래식 가상 네트워크에서 실행되는 Azure AD DS 관리 도메인에는 AD 계정 잠금 정책이 없습니다. VM이 인터넷에 노출되면 공격자는 암호 스프레이 방법을 사용하여 계정에 무차별 대입할 수 있습니다. 이러한 시도를 중지하는 계정 잠금 정책은 없습니다. 리소스 관리자 배포 모델 및 가상 네트워크를 사용하는 Azure AD DS 관리 도메인의 경우 AD 계정 잠금 정책은 이러한 암호 스프레이 공격으로부터 보호합니다.

기본적으로 2분 동안 5번의 잘못된 암호 시도가 30분 동안 계정을 잠급전지 않습니다.

잠긴 계정은 로그인하는 데 사용할 수 없으며, 이로 인해 Azure AD DS 관리 도메인 또는 계정에서 관리하는 응용 프로그램을 관리하는 기능이 방해될 수 있습니다. Azure AD DS 관리 도메인을 마이그레이션한 후 반복적으로 로그인하지 못해 영구적인 잠금이 발생하는 것을 경험할 수 있습니다. 마이그레이션 후 두 가지 일반적인 시나리오는 다음과 같습니다.

* 만료된 암호를 사용하는 서비스 계정입니다.
    * 서비스 계정은 만료된 암호로 반복적으로 로그인을 시도하여 계정을 잠그습니다. 이 문제를 해결하려면 만료된 자격 증명이 있는 응용 프로그램 또는 VM을 찾아 암호를 업데이트합니다.
* 악의적인 엔터티가 무차별 암호 대입 시도를 사용하여 계정에 로그인합니다.
    * VM이 인터넷에 노출되면 공격자는 서명을 시도할 때 일반적인 사용자 이름과 암호 조합을 시도하는 경우가 많습니다. 이러한 반복된 실패한 로그인 시도는 계정을 잠글 수 있습니다. 관리자 *또는* *관리자와*같은 일반 이름이 있는 관리자 계정을 사용하여 관리 계정이 잠기지 않도록 하는 것은 권장되지 않습니다.
    * 인터넷에 노출되는 VM 수를 최소화합니다. [Azure 요새를][azure-bastion] 사용하여 Azure 포털을 사용하여 VM에 안전하게 연결할 수 있습니다.

마이그레이션 후 일부 계정이 잠길 수 있다고 의심되는 경우 최종 마이그레이션 단계에서는 감사를 사용하도록 설정하거나 세분화된 암호 정책 설정을 변경하는 방법을 간략하게 설명합니다.

### <a name="roll-back-and-restore"></a>롤백 및 복원

마이그레이션에 성공하지 못하면 Azure AD DS 관리 도메인을 롤백하거나 복원하는 프로세스가 있습니다. 롤백은 마이그레이션을 시도하기 전에 관리되는 도메인의 상태를 즉시 반환하는 셀프 서비스 옵션입니다. Azure 지원 엔지니어는 백업에서 관리되는 도메인을 마지막 수단으로 복원할 수도 있습니다. 자세한 내용은 [실패한 마이그레이션에서 롤백하거나 복원하는 방법을](#roll-back-and-restore-from-migration)참조하세요.

### <a name="restrictions-on-available-virtual-networks"></a>사용 가능한 가상 네트워크에 대한 제한 사항

Azure AD DS 관리 도메인을 마이그레이션할 수 있는 가상 네트워크에는 몇 가지 제한 사항이 있습니다. 대상 리소스 관리자 가상 네트워크는 다음 요구 사항을 충족해야 합니다.

* 리소스 관리자 가상 네트워크는 Azure AD DS가 현재 배포중인 클래식 가상 네트워크와 동일한 Azure 구독에 있어야 합니다.
* 리소스 관리자 가상 네트워크는 Azure AD DS가 현재 배포중인 클래식 가상 네트워크와 동일한 지역에 있어야 합니다.
* 리소스 관리자 가상 네트워크의 서브넷에는 사용 가능한 IP 주소가 3~5개 이상 있어야 합니다.
* 리소스 관리자 가상 네트워크의 서브넷은 Azure AD DS전용 서브넷이어야 하며 다른 워크로드를 호스팅해서는 안 됩니다.

가상 네트워크 요구 사항에 대한 자세한 내용은 [가상 네트워크 디자인 고려 사항 및 구성 옵션을][network-considerations]참조하십시오.

## <a name="migration-steps"></a>마이그레이션 단계

리소스 관리자 배포 모델 및 가상 네트워크로의 마이그레이션은 5가지 주요 단계로 나뉩습니다.

| 단계    | 를 통해 수행  | 예상 시간  | 가동 중지 시간  | 롤백/복원? |
|---------|--------------------|-----------------|-----------|-------------------|
| [1단계 - 새 가상 네트워크를 업데이트하고 찾습니다.](#update-and-verify-virtual-network-settings) | Azure portal | 15분 | 가동 중지 시간 필요 없음 | 해당 없음 |
| [2단계 - 마이그레이션을 위해 Azure AD DS 관리 도메인 준비](#prepare-the-managed-domain-for-migration) | PowerShell | 평균 15 – 30분 | Azure AD DS의 가동 중지 시간은 이 명령이 완료된 후 시작됩니다. | 롤백 및 복원 사용 가능. |
| [3단계 - Azure AD DS 관리 도메인을 기존 가상 네트워크로 이동](#migrate-the-managed-domain) | PowerShell | 평균 1 – 3시간 | 이 명령이 완료되면 하나의 도메인 컨트롤러를 사용할 수 있으며 가동 중지 시간이 종료됩니다. | 오류가 발생할 경우 롤백(셀프 서비스) 및 복원을 모두 사용할 수 있습니다. |
| [4단계 - 복제본 도메인 컨트롤러를 테스트하고 기다립니다.](#test-and-verify-connectivity-after-the-migration)| 파워쉘 및 Azure 포털 | 테스트 수에 따라 1시간 이상 | 두 도메인 컨트롤러를 모두 사용할 수 있으며 정상적으로 작동해야 합니다. | 해당 없음. 첫 번째 VM이 성공적으로 마이그레이션되면 롤백 또는 복원 옵션이 없습니다. |
| [5단계 - 선택적 구성 단계](#optional-post-migration-configuration-steps) | Azure 포털 및 VM | 해당 없음 | 가동 중지 시간 필요 없음 | 해당 없음 |

> [!IMPORTANT]
> 추가 가동 중지 시간을 방지하려면 마이그레이션 프로세스를 시작하기 전에 이 마이그레이션 문서 및 지침을 모두 읽어보십시오. 마이그레이션 프로세스는 기간 동안 Azure AD DS 도메인 컨트롤러의 가용성에 영향을 줍니다. 사용자, 서비스 및 응용 프로그램은 마이그레이션 프로세스 중에 관리되는 도메인에 대해 인증할 수 없습니다.

## <a name="update-and-verify-virtual-network-settings"></a>가상 네트워크 설정 업데이트 및 확인

마이그레이션 프로세스를 시작하기 전에 다음 초기 검사 및 업데이트를 완료합니다. 이러한 단계는 마이그레이션 전에 언제든지 발생할 수 있으며 Azure AD DS 관리 도메인의 작업에 영향을 주지 않습니다.

1. 로컬 Azure PowerShell 환경을 최신 버전으로 업데이트합니다. 마이그레이션 단계를 완료하려면 적어도 버전 *2.3.2가*필요합니다.

    PowerShell 버전을 확인하고 업데이트하는 방법에 대한 자세한 내용은 [Azure PowerShell 개요를][azure-powershell]참조하십시오.

1. 기존 리소스 관리자 가상 네트워크를 만들거나 선택합니다.

    네트워크 설정이 Azure AD DS에 필요한 포트를 차단하지 않도록 합니다. 포트는 클래식 가상 네트워크와 리소스 관리자 가상 네트워크 모두에서 열려 있어야 합니다. 이러한 설정에는 경로 테이블(경로 테이블을 사용하지 않는 것이 좋지만) 및 네트워크 보안 그룹이 포함됩니다.

    필요한 포트를 보려면 [네트워크 보안 그룹 및 필수 포트를][network-ports]참조하십시오. 네트워크 통신 문제를 최소화하려면 마이그레이션이 성공적으로 완료된 후 네트워크 보안 그룹 또는 경로 테이블을 Resource Manager 가상 네트워크에 적용하는 것이 좋습니다.

    이 대상 리소스 그룹을 기록하고, 가상 네트워크를 대상으로 하고, 가상 네트워크 서브넷을 대상으로 합니다. 이러한 리소스 이름은 마이그레이션 프로세스 중에 사용됩니다.

1. Azure 포털에서 Azure AD DS 관리 도메인 상태를 확인합니다. 관리되는 도메인에 대한 경고가 있는 경우 마이그레이션 프로세스를 시작하기 전에 이를 해결합니다.
1. 선택적으로 다른 리소스를 Resource Manager 배포 모델 및 가상 네트워크로 이동하려는 경우 해당 리소스를 마이그레이션할 수 있는지 확인합니다. 자세한 내용은 [클래식에서 리소스 관리자로의 IaaS 리소스의 플랫폼 지원 마이그레이션을][migrate-iaas]참조하십시오.

    > [!NOTE]
    > 클래식 가상 네트워크를 리소스 관리자 가상 네트워크로 변환하지 마십시오. 이렇게 하면 Azure AD DS 관리 도메인을 롤백하거나 복원할 수 있는 옵션이 없습니다.

## <a name="prepare-the-managed-domain-for-migration"></a>마이그레이션을 위해 관리되는 도메인 준비

Azure PowerShell은 마이그레이션을 위해 Azure AD DS 관리 도메인을 준비하는 데 사용됩니다. 이러한 단계에는 백업 수행, 동기화 일시 중지 및 Azure AD DS를 호스트하는 클라우드 서비스 삭제가 포함됩니다. 이 단계가 완료되면 Azure AD DS는 잠시 동안 오프라인 상태가 됩니다. 준비 단계가 실패하면 [이전 상태로 롤백할](#roll-back)수 있습니다.

Azure AD DS 관리 도메인마이그레이션을 준비하려면 다음 단계를 완료하십시오.

1. `Migrate-Aaads` [PowerShell 갤러리에서][powershell-script]스크립트를 설치합니다. 이 PowerShell 마이그레이션 스크립트는 Azure AD 엔지니어링 팀에서 디지털 서명한 스크립트입니다.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. [Get-Credential][get-credential] cmdlet을 사용하여 마이그레이션 스크립트에 의해 자격 증명을 보유하는 변수를 만듭니다.

    지정한 사용자 계정에는 Azure AD DS를 사용하도록 설정한 다음 Azure 구독에서 *기여자* 권한을 사용하여 필요한 Azure AD DS 리소스를 만들려면 Azure AD 테넌트에서 *글로벌 관리자* 권한이 필요합니다.

    메시지가 표시되면 적절한 사용자 계정과 암호를 입력합니다.

    ```powershell
    $creds = Get-Credential
    ```

1. 이제 `Migrate-Aadds` *-Prepare* 매개 변수를 사용하여 cmdlet을 실행합니다. *aaddscontoso.com*와 같은 사용자 고유의 Azure AD DS 관리 도메인에 *대해 -ManagedDomainFqdn을* 제공합니다.

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>관리되는 도메인 마이그레이션

Azure AD DS 관리 도메인을 준비하고 백업하면 도메인을 마이그레이션할 수 있습니다. 이 단계에서는 리소스 관리자 배포 모델을 사용하여 Azure AD 도메인 서비스 도메인 컨트롤러 VM을 다시 만듭니다. 이 단계를 완료하는 데 1~3시간이 걸릴 수 있습니다.

`Migrate-Aadds` *-Commit* 매개 변수를 사용하여 cmdlet을 실행합니다. *aaddscontoso.com*와 같은 이전 섹션에서 준비된 사용자 고유의 Azure AD DS 관리 도메인에 *대해 -ManagedDomainFqdn을* 제공합니다.

*myResourceGroup*과 같이 Azure AD DS로 마이그레이션할 가상 네트워크를 포함하는 대상 리소스 그룹을 지정합니다. *myVnet*및 *도메인 서비스*와 같은 서브넷과 같은 대상 가상 네트워크를 제공합니다.

이 명령을 실행한 후에는 롤백할 수 없습니다.

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

스크립트가 관리되는 도메인이 마이그레이션할 준비가 되어 있는지 확인한 후 *Y를* 입력하여 마이그레이션 프로세스를 시작합니다.

> [!IMPORTANT]
> 마이그레이션 프로세스 중에 클래식 가상 네트워크를 리소스 관리자 가상 네트워크로 변환하지 마십시오. 가상 네트워크를 변환하는 경우 원래 가상 네트워크가 더 이상 존재하지 않기 때문에 Azure AD DS 관리 도메인을 롤백하거나 복원할 수 없습니다.

마이그레이션 프로세스 중 2분마다 진행률 표시기는 다음 예제 출력과 같이 현재 상태를 보고합니다.

![Azure AD DS 마이그레이션의 진행률 표시기](media/migrate-from-classic-vnet/powershell-migration-status.png)

PowerShell 스크립트를 닫아도 마이그레이션 프로세스가 계속 실행됩니다. Azure 포털에서 관리되는 도메인의 상태가 *마이그레이션으로*보고합니다.

마이그레이션이 성공적으로 완료되면 Azure 포털 또는 Azure PowerShell을 통해 첫 번째 도메인 컨트롤러의 IP 주소를 볼 수 있습니다. 사용 가능한 두 번째 도메인 컨트롤러에 대한 시간 예측도 표시됩니다.

이 단계에서는 클래식 배포 모델 및 가상 네트워크에서 다른 기존 리소스를 선택적으로 이동할 수 있습니다. 또는 Azure AD DS 마이그레이션이 완료된 후 클래식 배포 모델의 리소스를 유지하고 가상 네트워크를 서로 피어로 피어낼 수 있습니다.

## <a name="test-and-verify-connectivity-after-the-migration"></a>마이그레이션 후 연결 테스트 및 확인

두 번째 도메인 컨트롤러가 성공적으로 배포되고 Azure AD DS 관리 도메인에서 사용할 수 있도록 하는 데 다소 시간이 걸릴 수 있습니다.

리소스 관리자 배포 모델을 사용하면 Azure AD DS 관리 도메인의 네트워크 리소스가 Azure Portal 또는 Azure PowerShell에 표시됩니다. 이러한 네트워크 리소스의 현재 와 수행 에 대한 자세한 내용은 [Azure AD DS에서 사용하는 네트워크 리소스를][network-resources]참조하십시오.

하나 이상의 도메인 컨트롤러를 사용할 수 있는 경우 VM과의 네트워크 연결에 대한 다음 구성 단계를 완료합니다.

* **DNS 서버 설정 업데이트** Resource Manager 가상 네트워크의 다른 리소스가 Azure AD DS 관리 도메인을 해결하고 사용하도록 하려면 새 도메인 컨트롤러의 IP 주소로 DNS 설정을 업데이트합니다. Azure 포털은 이러한 설정을 자동으로 구성할 수 있습니다. Resource Manager 가상 네트워크를 구성하는 방법에 대한 자세한 내용은 [Azure 가상 네트워크에 대한 DNS 업데이트 설정을][update-dns]참조하십시오.
* **도메인 에 가입한 VM 다시 시작** - Azure AD DS 도메인 컨트롤러의 DNS 서버 IP 주소가 변경되면 도메인에 가입한 VM을 다시 시작하여 새 DNS 서버 설정을 사용합니다. 응용 프로그램 또는 VM이 DNS 설정을 수동으로 구성한 경우 Azure 포털에 표시되는 도메인 컨트롤러의 새 DNS 서버 IP 주소로 수동으로 업데이트합니다.

이제 가상 네트워크 연결 및 이름 확인을 테스트합니다. Resource Manager 가상 네트워크에 연결되거나 피어인 VM에서 다음 네트워크 통신 테스트를 시도합니다.

1. 도메인 컨트롤러 중 하나의 IP 주소를 ping할 수 있는지 확인합니다.`ping 10.1.0.4`
    * 도메인 컨트롤러의 IP 주소는 Azure 포털의 Azure AD DS 관리 도메인의 **속성** 페이지에 표시됩니다.
1. 다음과 같이 관리되는 도메인의 이름 확인 확인`nslookup aaddscontoso.com`
    * DNS 설정이 올바른지 확인하고 해결하려면 자체 Azure AD DS 관리 도메인에 대한 DNS 이름을 지정합니다.

두 번째 도메인 컨트롤러는 마이그레이션 cmdlet이 완료된 후 1-2시간 후에 사용할 수 있어야 합니다. 두 번째 도메인 컨트롤러를 사용할 수 있는지 확인하려면 Azure 포털에서 Azure AD DS 관리 도메인의 **속성** 페이지를 확인합니다. 두 개의 IP 주소가 표시되면 두 번째 도메인 컨트롤러가 준비된 것입니다.

## <a name="optional-post-migration-configuration-steps"></a>마이그레이션 후 구성 단계 옵션

마이그레이션 프로세스가 성공적으로 완료되면 일부 선택적 구성 단계에는 감사 로그 또는 전자 메일 알림을 사용하도록 설정하거나 세분화된 암호 정책을 업데이트하는 것이 포함됩니다.

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Azure 모니터를 사용하여 감사 로그 구독

Azure AD DS는 도메인 컨트롤러에서 이벤트를 해결하고 볼 수 있도록 감사 로그를 노출합니다. 자세한 내용은 [감사 로그 사용 및 사용을][security-audits]참조하십시오.

템플릿을 사용하여 로그에 노출된 중요한 정보를 모니터링할 수 있습니다. 예를 들어 감사 로그 통합 문서 템플릿은 Azure AD DS 관리 도메인에서 가능한 계정 잠금을 모니터링할 수 있습니다.

### <a name="configure-azure-ad-domain-services-email-notifications"></a>Azure AD 도메인 서비스 전자 메일 알림 구성

Azure AD DS 관리 도메인에서 문제가 감지되면 알림을 받으면 Azure 포털에서 전자 메일 알림 설정을 업데이트합니다. 자세한 내용은 [알림 설정 구성을][notifications]참조하십시오.

### <a name="update-fine-grained-password-policy"></a>세분화된 암호 정책 업데이트

필요한 경우 세분화된 암호 정책을 기본 구성보다 덜 제한적으로 업데이트할 수 있습니다. 감사 로그를 사용하여 덜 제한적인 설정이 적합한지 결정한 다음 필요에 따라 정책을 구성할 수 있습니다. 다음 상위 수준 단계를 사용하여 마이그레이션 후 반복적으로 잠긴 계정의 정책 설정을 검토하고 업데이트합니다.

1. Azure AD DS 관리 도메인에 대한 제한 을 줄이면 [암호 정책을 구성하고][password-policy] 감사 로그의 이벤트를 관찰합니다.
1. 서비스 계정이 감사 로그에서 식별된 만료된 암호를 사용하는 경우 해당 계정을 올바른 암호로 업데이트합니다.
1. VM이 인터넷에 노출되면 *관리자,* *사용자*또는 로그인 시도가 많은 *게스트와* 같은 일반 계정 이름을 검토합니다. 가능한 경우 이러한 VM을 업데이트하여 일반적으로 명명된 계정을 덜 사용합니다.
1. VM의 네트워크 추적을 사용하여 공격의 원인을 찾고 이러한 IP 주소가 로그인을 시도하지 못하도록 차단합니다.
1. 최소한의 잠금 문제가 있는 경우 필요에 따라 세분화된 암호 정책을 제한적으로 업데이트합니다.

### <a name="creating-a-network-security-group"></a>네트워크 보안 그룹 만들기

Azure AD DS는 관리되는 도메인에 필요한 포트를 보호하고 다른 모든 들어오는 트래픽을 차단하기 위해 네트워크 보안 그룹이 필요합니다. 이 네트워크 보안 그룹은 관리되는 도메인에 대한 액세스를 잠그는 추가 보호 계층역할을 하며 자동으로 생성되지 않습니다. 네트워크 보안 그룹을 만들고 필요한 포트를 열려면 다음 단계를 검토하십시오.

1. Azure 포털에서 Azure AD DS 리소스를 선택합니다. 개요 페이지에서 Azure AD 도메인 서비스와 연관된 것이 없는 경우 네트워크 보안 그룹을 만드는 단추가 표시됩니다.
1. 보안 LDAP를 사용하는 경우 네트워크 보안 그룹에 규칙을 추가하여 *TCP* 포트 *636에*대한 들어오는 트래픽을 허용합니다. 자세한 내용은 [보안 LDAP 구성을][secure-ldap]참조하십시오.

## <a name="roll-back-and-restore-from-migration"></a>마이그레이션에서 롤백 및 복원

마이그레이션 프로세스의 특정 지점까지 Azure AD DS 관리 도메인을 롤백하거나 복원하도록 선택할 수 있습니다.

### <a name="roll-back"></a>롤백할

PowerShell cmdlet을 실행하여 2단계에서 마이그레이션을 준비하거나 3단계에서 마이그레이션 자체에 대한 오류가 있는 경우 Azure AD DS 관리 도메인이 원래 구성으로 롤백할 수 있습니다. 이 롤백에는 원래 Classic 가상 네트워크가 필요합니다. IP 주소는 롤백 후에도 변경될 수 있습니다.

`Migrate-Aadds` *-Abort* 매개 변수를 사용하여 cmdlet을 실행합니다. *aaddscontoso.com*및 *myClassicVnet과*같은 클래식 가상 네트워크 이름과 같은 이전 섹션에서 준비된 Azure AD DS 관리 도메인에 *대해 -ManagedDomainFqdn을* 제공합니다.

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds
```

### <a name="restore"></a>복원

마지막 수단으로 Azure AD 도메인 서비스는 사용 가능한 마지막 백업에서 복원할 수 있습니다. 마이그레이션의 1단계에서 백업을 수행하여 최신 백업을 사용할 수 있는지 확인합니다. 이 백업은 30일 동안 저장됩니다.

백업에서 Azure AD DS 관리 도메인을 복원하려면 [Azure 포털을 사용하여 지원 사례 티켓을 엽니다.][azure-support] 디렉터리 ID, 도메인 이름 및 복원 이유를 제공합니다. 지원 및 복원 프로세스를 완료하는 데 며칠이 걸릴 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

Resource Manager 배포 모델로 마이그레이션한 후 문제가 있는 경우 다음과 같은 일반적인 문제 해결 영역 중 일부를 검토하십시오.

* [도메인 가입 문제 해결][troubleshoot-domain-join]
* [계정 잠금 문제 해결][troubleshoot-account-lockout]
* [계정 로그인 문제 해결][troubleshoot-sign-in]
* [안전한 LDAP 연결 문제 해결][tshoot-ldaps]

## <a name="next-steps"></a>다음 단계

Azure AD DS 관리 도메인이 리소스 관리자 배포 모델로 마이그레이션된 경우 [Windows VM을 만들고 도메인에 참여한][join-windows] 다음 [관리 도구를 설치합니다.][tutorial-create-management-vm]

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/overview
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/windows/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
