---
title: Azure Lighthouse 샘플 및 템플릿
description: 이러한 샘플과 Azure Resource Manager 템플릿은 Azure가 위임한 리소스 관리를 위해 고객을 온보딩하고 Azure Lighthouse 시나리오를 지원하는 방법을 보여줍니다.
ms.topic: sample
ms.date: 10/17/2019
ms.openlocfilehash: 20c1fc62c83202f97ed9cff68fd285280d94a321
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132157"
---
# <a name="azure-lighthouse-samples"></a>Azure Lighthouse 샘플

다음 표에는 Azure Lighthouse용 주요 Azure Resource Manager 템플릿 링크가 포함되어 있습니다. [Azure Lighthouse 샘플 리포지토리](https://github.com/Azure/Azure-Lighthouse-samples/)에서 이러한 파일과 다른 파일을 확인할 수 있습니다.

## <a name="onboarding-customers-to-azure-delegated-resource-management"></a>Azure 위임 리소스 관리에 고객 온보딩

특정 온보딩 시나리오를 해결하기 위해 다른 템플릿을 제공합니다. 가장 적합한 옵션을 선택하고 사용자 환경에 맞게 매개 변수 파일을 수정해야 합니다. 배포에서 이러한 파일을 사용하는 방법에 대한 자세한 내용은 [Azure 위임 리소스 관리에 고객 등록](../how-to/onboard-customer.md) 을 참조하세요.

| **템플릿** | **설명** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Azure 위임 리소스 관리에 고객의 구독을 등록합니다. 각 구독에 대해 별도의 배포를 수행해야 합니다. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Azure 위임 리소스 관리에 하나 이상의 고객 리소스 그룹을 등록합니다. 단일 리소스 그룹에 **rgDelegatedResourceManagement** 를 사용하거나 동일한 구독에서 여러 리소스 그룹을 온보드하려면 **multipleRgDelegatedResourceManagement**를 사용합니다. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | [Azure Marketplace에 관리 서비스 제품을 게시](../how-to/publish-managed-services-offers.md)한 경우, 필요에 따라 이 템플릿을 사용하여 제품을 수락한 고객에 대한 리소스를 온보드할 수 있습니다. 매개 변수 파일의 마켓플레이스 값은 제품을 게시할 때 사용한 값과 일치해야 합니다. |

일반적으로 온보드되는 각 구독에 대해 별도의 배포가 필요하지만 여러 구독에 템플릿을 배포할 수도 있습니다.

| **템플릿** | **설명** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | 여러 구독에 Azure Resource Manager 템플릿을 배포합니다. |

## <a name="azure-policy"></a>Azure Policy

이러한 샘플은 Azure 위임 리소스 관리를 위해 온보드된 구독과 함께 Azure Policy를 사용하는 방법을 보여 줍니다.

| **템플릿** | **설명** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | 위임된 구독에 수정 효과를 사용하여 태그를 추가하거나 제거하는 정책을 할당합니다. 자세한 내용은 [위임된 구독 내에서 수정할 수 있는 정책 배포](../how-to/deploy-policy-remediation.md)를 참조하세요. |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | 위임 할당을 감사하는 정책을 할당합니다. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | deployIfNotExists 효과를 사용하여 위임된 구독에서 Azure Key Vault 리소스에 대한 진단을 사용하도록 설정하는 정책을 할당합니다. 자세한 내용은 [위임된 구독 내에서 수정할 수 있는 정책 배포](../how-to/deploy-policy-remediation.md)를 참조하세요. |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | 위임된 구독에서 진단을 사용하도록 여러 정책을 할당하고 모든 Windows 및 Linux VM을 정책에 의해 생성된 Log Analytics 작업 영역에 연결합니다. 자세한 내용은 [위임된 구독 내에서 수정할 수 있는 정책 배포](../how-to/deploy-policy-remediation.md)를 참조하세요. |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | 위임된 구독에 이니셔티브(여러 관련 정책 정의)를 적용합니다. |

## <a name="azure-monitor"></a>Azure Monitor

이러한 샘플은 Azure Monitor를 사용하여 Azure 위임 리소스 관리를 위해 온보드된 구독에 대한 경고를 만드는 방법을 보여 줍니다.

| **템플릿** | **설명** |
|---------|---------|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | 이 템플릿은 Azure 경고를 만들고 기존 작업 그룹에 연결합니다.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Kusto 쿼리를 기반으로 여러 로그 경고를 만듭니다.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | 사용자가 관리 테넌트에 구독을 위임하면 테넌트에 경고를 배포합니다.|

## <a name="additional-cross-tenant-scenarios"></a>추가 교차 테넌트 시나리오

이러한 샘플은 교차 테넌트 관리 시나리오에서 수행할 수 있는 다양한 작업을 보여 줍니다.

| **템플릿** | **설명** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | 스토리지 계정을 두 개의 서로 다른 리소스 그룹에 배포합니다.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Azure 관리 서비스를 만들고, 함께 연결하고, 추가 솔루션을 배포합니다. 엔드투엔드 배포의 경우 **rgWithAzureMgmt.json** 템플릿을 사용합니다. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | 대상 Azure 구독 내에서 Azure Security Center를 사용하도록 설정하고 구성합니다. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | 위임된 구독의 기존 Log Analytics 작업 영역에서 Azure Sentinel을 배포하고 사용하도록 설정합니다. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | 이러한 템플릿을 사용하여 Windows 및 Linux VM에 Log Analytics VM 확장을 배포하여 지정된 Log Analytics 작업 영역에 연결할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

- [Azure 위임 리소스 관리](../concepts/azure-delegated-resource-management.md)에 대해 알아봅니다.
- [Azure Lighthouse 샘플 리포지토리](https://github.com/Azure/Azure-Lighthouse-samples/)를 탐색합니다.
