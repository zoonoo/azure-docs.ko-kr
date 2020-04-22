---
title: 테넌트 간 관리 환경
description: Azure 위임 리소스 관리를 통해 테넌트 간 관리 환경을 사용하도록 설정할 수 있습니다.
ms.date: 04/20/2020
ms.topic: conceptual
ms.openlocfilehash: 107350cdab90751e918d4c7845776464b43abbdd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682824"
---
# <a name="cross-tenant-management-experiences"></a>테넌트 간 관리 환경

서비스 공급자는 [Azure 위임 리소스 관리](../concepts/azure-delegated-resource-management.md)를 사용하여 [Azure Portal](https://portal.azure.com)의 고유한 테넌트 내에서 여러 고객에 대한 Azure 리소스를 관리할 수 있습니다. 대부분의 작업 및 서비스는 관리형 테넌트의 위임된 Azure 리소스에서 수행할 수 있습니다. 이 문서에서는 Azure 위임 리소스 관리를 효과적으로 수행할 수 있는 고급 시나리오 중 일부에 대해 설명합니다.

> [!NOTE]
> Azure 위임된 리소스 관리는 [자체의 여러 Azure AD 테넌트가 있는 엔터프라이즈 내에서](enterprise.md) 도 사용할 수 있어 테넌트 간 관리를 단순화할 수 있습니다.

## <a name="understanding-customer-tenants"></a>고객 테넌트 이해

Azure AD(Azure Active Directory) 테넌트는 조직을 나타냅니다. Azure, Microsoft 365 또는 기타 서비스에 가입하여 Microsoft와의 관계를 만들 때 조직이 받는 Azure AD의 전용 인스턴스입니다. 각 Azure AD 테넌트는 서로 전혀 다르고 다른 Azure AD 테넌트와 별개이며 고유한 테넌트 ID(GUID)가 있습니다. 자세한 내용은 [Azure Active Directory란?](../../active-directory/fundamentals/active-directory-whatis.md)을 참조하세요.

일반적으로 고객의 Azure 리소스를 관리하기 위해 서비스 공급자는 고객의 테넌트와 연결된 계정을 사용하여 Azure Portal에 로그인해야 하며, 서비스 공급자의 사용자 계정을 만들고 관리하기 위해 고객 테넌트에 관리자가 필요합니다.

Azure 위임 리소스 관리를 통해 온보딩 프로세스는 고객 테넌트의 구독, 리소스 그룹 및 리소스를 액세스하고 관리할 수 있는 서비스 공급자의 테넌트 내의 사용자를 지정합니다. 그러면 이러한 사용자는 자신의 자격 증명을 사용하여 Azure Portal에 로그인할 수 있습니다. Azure Portal 내에서 액세스 권한이 있는 모든 고객에게 속한 리소스를 관리할 수 있습니다. 이 작업은 Azure Portal에서 [내 고객](../how-to/view-manage-customers.md) 페이지에서 수행하거나 Azure Portal 또는 API를 통해 해당 고객의 구독 컨텍스트 내에서 직접 수행할 수 있습니다.

Azure 위임 리소스 관리를 사용하면 다른 테넌트의 다른 계정에 로그인하지 않고도 여러 고객을 위한 리소스를 더욱 유연하게 관리할 수 있습니다. 예를 들어 서비스 공급자는 서로 다른 책임과 액세스 수준을 가진 두 고객을 가질 수 있습니다. 권한이 있는 사용자는 Azure 위임된 리소스 관리를 사용하여 서비스 공급자의 테넌트에 로그인하여 이러한 리소스에 액세스할 수 있습니다.

![하나의 서비스 공급자 테넌트를 통해 관리되는 고객 리소스](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>API 및 관리 도구 지원

포털에서 직접 또는 API 및 관리 도구(예: Azure CLI 및 Azure PowerShell)를 사용하여 위임된 리소스에서 관리 작업을 수행할 수 있습니다. 이 기능이 테넌트 간 관리에 대해 지원되고 사용자에게 적절 한 권한이 있는 한, 위임된 리소스를 사용할 때 기존의 모든 API를 사용할 수 있습니다.

Azure PowerShell [Get-AzSubscription cmdlet각](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzSubscription?view=azps-3.5.0) 구독에 대 한 **tenantID를** 표시 합니다., 반환 된 구독 서비스 공급자 테 넌 트 또는 관리 되는 고객 테넌트에 속하는지 여부를 식별할 수 있습니다.

마찬가지로 [az 계정 목록과](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list) 같은 Azure CLI 명령은 **homeTenantId** 및 **managedByTenants** 특성을 표시합니다.

> [!TIP]
> Azure CLI를 사용할 때 이러한 값이 표시되지 않으면 `az account clear` `az login --identity`다음을 실행하여 캐시를 지우십시오.

또한 Azure 위임리소스 관리 작업을 수행하는 것과 관련된 API도 제공합니다. 자세한 내용은 **참조** 섹션을 참조하세요.

## <a name="enhanced-services-and-scenarios"></a>고급 서비스 및 시나리오

대부분의 작업 및 서비스는 관리형 테넌트의 위임된 리소스에서 수행할 수 있습니다. 다음은 테넌트 간 관리를 효과적으로 수행할 수 있는 몇 가지 주요 시나리오입니다.

[서버용 Azure Arc(미리 보기)](../../azure-arc/servers/overview.md):

- [Azure 외부의 Windows Server 또는 Linux 컴퓨터를](../../azure-arc/servers/quickstart-onboard-portal.md) Azure의 위임 구독 및/또는 리소스 그룹에 연결
- Azure Policy 및 태그 지정과 같은 Azure 구문을 사용하여 연결된 컴퓨터 관리

[Azure 자동화](../../automation/index.yml):

- Automation 계정을 사용하여 위임된 고객 리소스를 액세스 및 사용합니다.

[Azure 백업](../../backup/index.yml):

- 고객 테넌트의 고객 데이터를 백업 및 복원합니다.
- 백업 [탐색기를](../../backup/monitor-azure-backup-with-backup-explorer.md) 사용하여 백업 항목의 운영 정보(아직 백업에 대해 구성되지 않은 Azure 리소스 포함) 및 위임된 구독에 대한 모니터링 정보(작업 및 경고)를 볼 수 있습니다. 백업 탐색기는 현재 Azure VM 데이터에만 사용할 수 있습니다.
- 위임된 구독에서 [백업 보고서를](../../backup/configure-reports.md) 사용하여 기록 추세를 추적하고, 백업 저장소 사용량을 분석하고, 백업 및 복원을 감사합니다.

[Azure Kubernetes 서비스 (AKS)](../../aks/index.yml):

- 호스트된 Kubernetes 환경 관리 및 고객 테넌트 내 컨테이너화된 애플리케이션을 배포 및 관리합니다.

[Azure 모니터](../../azure-monitor/index.yml):

- 모든 구독에서 경고를 볼 수 있는 기능을 사용하여 위임된 구독에 대한 경고를 봅니다.
- 위임된 구독의 활동 로그 세부 정보를 봅니다.
- 로그 분석: 여러 테넌트에서 원격 고객 작업 영역의 데이터 쿼리
- 웹 후크를 통해 서비스 공급자 테넌트에서 Azure Automation Runbook 또는 Azure Functions와 같은 자동화를 트리거하는 고객 테넌트에서 경고를 만듭니다.

[Azure 네트워킹](../../networking/networking-overview.md):

- 고객 테넌타 내에서 [Azure 가상 네트워크(VNet)](../../virtual-network/index.yml) 및 가상 네트워크 인터페이스 카드(vNI)를 배포하고 관리합니다.
- 고객의 가상 네트워크 리소스를 보호하기 위해 [Azure 방화벽](../../firewall/overview.md) 배포 및 구성
- 고객을 위한 Azure [가상 WAN,](../../virtual-wan/virtual-wan-about.md) [ExpressRoute](../../expressroute/expressroute-introduction.md)및 [VPN 게이트웨이와](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 같은 연결 서비스 관리
- Azure 등대를 사용하여 [Azure 네트워킹 MSP 프로그램의](../../networking/networking-partners-msp.md) 주요 시나리오를 지원합니다.


[Azure 정책](../../governance/policy/index.yml):

- 규정 준수 스냅샷에 위임된 구독 내에 할당된 정책에 대한 세부 정보를 표시합니다.
- 위임된 구독 내에서 정책 정의를 만들고 편집합니다.
- 위임된 구독 내에서 고객 정의 정책 정의를 할당합니다.
- 고객은 자신이 직접 작성한 정책과 함께 서비스 공급자가 작성한 정책을 볼 수 있습니다.
- [deployIfNotExists를 수정하거나 고객 테넌트 내에서 할당을 수정](../how-to/deploy-policy-remediation.md)할 수 있습니다.

[Azure 리소스 그래프](../../governance/resource-graph/index.yml):

- 이제 반환된 쿼리 결과에 테넌트 ID를 포함하여 구독이 고객 테넌트 또는 서비스 공급자 테넌트에 속하는지 여부를 식별할 수 있습니다.

[Azure 보안 센터](../../security-center/index.yml):

- 테넌트 간 표시 유형
  - 보안 정책 준수 를 모니터링하고 모든 테넌트의 리소스에 대한 보안 적용 범위 보장
  - 단일 보기에서 여러 고객에 대한 지속적인 규정 준수 모니터링
  - 보안 점수 계산을 통해 실행 가능한 보안 권장 사항을 모니터링하고, 심사하고, 우선 순위를 지정합니다.
- 테넌트 간 보안 상태 관리
  - 보안 정책 관리
  - 실행 가능한 보안 권장 사항을 준수하지 않는 리소스에 대해 조치를 취합니다.
  - 보안 관련 데이터를 수집 및 저장합니다.
- 테넌트 간 위협 검색 및 보호
  - 테넌트의 리소스 전반에 걸친 위협 탐지
  - JIT(Just-In-Time) VM 액세스와 같은 지능형 위협 방지 제어를 적용합니다.
  - 적응형 네트워크 강화로 네트워크 보안 그룹 구성을 강화합니다.
  - 적응형 애플리케이션 제어를 사용하여 서버가 필요한 애플리케이션 및 프로세스만 실행하는지 확인합니다.
  - FIM(파일 무결성 모니터링)을 사용하여 중요한 파일 및 레지스트리 항목의 변경 내용을 모니터링합니다.

[푸른 파수꾼](../../sentinel/multiple-tenants-service-providers.md):

- [고객 테넌에서](../../sentinel/multiple-tenants-service-providers.md) Azure Sentinel 리소스 관리
- [여러 고객 테넌트에서 공격 추적 및 보안 경고 보기](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)

[Azure 서비스 상태](../../service-health/index.yml):

- Azure Resource Health를 사용하여 고객 리소스 상태를 모니터링합니다.
- 고객이 사용하는 Azure 서비스의 상태를 추적합니다.

[Azure 사이트 복구](../../site-recovery/index.yml):

- 고객 테넌트의 Azure Virtual Machines에 대해 재해 복구 옵션을 관리합니다(실행 계정을 사용하여 VM 확장을 복사할 수 없음).

[Azure 가상 컴퓨터](../../virtual-machines/index.yml):

- 가상 머신 확장을 사용하여 고객 테넌트의 Azure VM에서 배포 후 구성 및 자동화 작업을 제공합니다.
- 부팅 진단을 사용하여 고객 테넌트의 Azure VM 문제를 해결합니다.
- 고객 테넌트에서 직렬 콘솔을 사용하여 VM에 액세스합니다.
- VM으로의 원격 로그인에 Azure Active Directory를 사용할 수 없으며, 디스크 암호화용 암호, 비밀 또는 암호화 키를 위해 VM을 Key Vault와 통합할 수 없습니다.

지원 요청:

- Azure Portal의 **도움말 + 지원** 블레이드에서 위임된 리소스에 대한 지원 요청을 엽니다(위임된 범위에서 사용할 수 있는 지원 플랜 선택).

## <a name="current-limitations"></a>현재 제한 사항
모든 시나리오에서 다음과 같은 현재 제한 사항을 알고 있어야 합니다.

- Azure Resource Manager에서 처리되는 요청은 Azure 위임 리소스 관리를 사용하여 수행할 수 있습니다. 이러한 요청에 대한 작업 URI는 `https://management.azure.com`으로 시작합니다. 그러나 리소스 유형의 인스턴스에서 처리되는 요청(예: KeyVault 암호 암호 액세스 또는 저장소 데이터 액세스)은 Azure 위임리소스 관리에서 지원되지 않습니다. 이러한 요청에 대한 작업 URI는 일반적으로 `https://myaccount.blob.core.windows.net` 또는 `https://mykeyvault.vault.azure.net/`과 같이 사용자 인스턴스에 고유한 주소로 시작합니다. 후자는 일반적으로 관리 작업이 아니라 데이터 작업입니다. 
- 역할 할당은 RBAC(역할 기반 액세스 제어) [기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 사용해야 합니다. 현재, 소유자 또는 [DataActions](../../role-based-access-control/role-definitions.md#dataactions) 권한이 있는 기본 제공 역할을 제외한 모든 기본 제공 역할이 Azure 위임 리소스 관리에서 지원됩니다. 사용자 액세스 관리자 역할은 [관리 ID에 역할 할당](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)에서 제한된 용도로만 지원됩니다.  사용자 지정 역할 및 [클래식 구독 관리자 역할](../../role-based-access-control/classic-administrators.md)은 지원되지 않습니다.
- Azure Databricks를 사용하는 구독을 온보딩할 수 있지만 관리 테넌트의 사용자는 현재 위임된 구독에서 Azure Databricks 작업 영역을 시작할 수 없습니다.
- 리소스 잠금이 있는 Azure 위임 리소스 관리에 대한 구독 및 리소스 그룹을 온보드할 수 있지만 이러한 잠금으로 인해 관리 테넌트의 사용자가 작업을 수행할 수 없습니다. Azure 관리 응용 프로그램 또는 Azure Blueprint(시스템 할당된 거부 할당)에서 만든 리소스와 같이 시스템 관리 되는 리소스를 보호하는 [거부 할당은](../../role-based-access-control/deny-assignments.md) 관리 테넌트의 사용자가 해당 리소스에 대해 작동하지 못하도록 합니다. 그러나 현재 고객 테넌트의 사용자는 자체 거부 할당(사용자 할당거부 할당)을 만들 수 없습니다.
- 관리 테넌트의 사용자는 일반적으로 액세스를 허용하는 기본 제공 역할이 있더라도 위임된 고객 구독에 대한 청구 정보를 볼 수 없습니다. 이는 청구 정보에 액세스하려면 현재 동일한 테넌트 내의 사용자에 대해서만 지원되는 추가 단계가 필요하기 때문입니다.

## <a name="next-steps"></a>다음 단계

- [Azure Resource Manager 템플릿을 사용](../how-to/onboard-customer.md)하거나 [개인 또는 공용 관리형 서비스 제품을 Azure Marketplace에 게시](../how-to/publish-managed-services-offers.md)하여 Azure 위임 리소스 관리에 고객을 온보딩합니다.
- Azure Portal의 **내 고객**으로 이동하여 [고객을 보고 관리](../how-to/view-manage-customers.md)합니다.
