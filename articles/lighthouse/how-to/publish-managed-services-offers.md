---
title: Azure 마켓플레이스에 관리되는 서비스 오퍼 게시
description: 고객이 Azure 위임 리소스 관리에 고객을 온보딩하는 관리형 서비스 제품을 게시하는 방법을 알아봅니다.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 922e78a0715ce5c758ca7d068a38af43e214a524
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673944"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Azure 마켓플레이스에 관리되는 서비스 오퍼 게시

이 문서에서는 [파트너 센터의](https://partner.microsoft.com/) [상용 마켓플레이스](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) 프로그램을 사용하여 [Azure 마켓플레이스에](https://azuremarketplace.microsoft.com) 공개 또는 비공개 관리 형 서비스 오퍼를 게시하는 방법을 알아봅니다. 그러면 제품을 구입한 고객은 [Azure 위임 리소스 관리를](../concepts/azure-delegated-resource-management.md)위해 구독 및 리소스 그룹을 온보딩할 수 있습니다.

## <a name="publishing-requirements"></a>게시 요구 사항

오퍼를 만들고 게시하려면 [파트너 센터에](../../marketplace/partner-center-portal/create-account.md) 유효한 계정이 있어야 합니다. 계정이 아직 없는 경우 [가입 프로세스를](https://aka.ms/joinmarketplace) 통해 파트너 센터에서 계정을 만들고 상업용 마켓플레이스 프로그램에 등록하는 단계를 안내합니다.

[관리형 서비스 제공 인증 요구 사항에](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)따라 실버 또는 골드 클라우드 플랫폼 역량 [수준이](https://docs.microsoft.com/partner-center/learn-about-competencies) 있거나 관리형 서비스 오퍼를 게시하려면 Azure [전문가 MSP여야](https://partner.microsoft.com/membership/azure-expert-msp) 합니다.

MPN(Microsoft 파트너 네트워크) ID는 게시하는 제품에 [자동으로 연결되어](../../billing/billing-partner-admin-link-started.md) 고객 계약에 미치는 영향을 추적합니다.

> [!NOTE]
> Azure Marketplace에 제품을 게시하지 않으려는 경우 Azure Resource Manager 템플릿을 사용하여 수동으로 고객을 온보딩할 수 있습니다. 자세한 내용은 [Azure 위임 리소스 관리에 고객 온보딩](onboard-customer.md)을 참조하세요.

## <a name="create-your-offer"></a>제품 만들기

제공해야 하는 모든 정보와 자산을 포함하여 쿠폰을 만드는 방법에 대한 자세한 지침은 [새 관리형 서비스 오퍼 만들기를](../../marketplace/partner-center-portal/create-new-managed-service-offer.md)참조하십시오.

일반 게시 프로세스에 대한 자세한 내용은 [Azure 마켓플레이스 및 앱소스 게시 가이드를](../../marketplace/marketplace-publishers-guide.md)참조하십시오. [상업 마켓플레이스 인증 정책](https://docs.microsoft.com/legal/marketplace/certification-policies), 특히 [관리 서비스](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services) 섹션도 검토해야 합니다.

고객이 제안을 추가하면 하나 이상의 구독 또는 리소스 그룹을 위임할 수 있으며, 이 구독은 [Azure 위임된 리소스 관리에 대해 온보딩됩니다.](#the-customer-onboarding-process)

> [!IMPORTANT]
> 관리되는 서비스 오퍼의 각 계획에는 **매니페스트 세부 정보** 섹션이 포함되어 있으며, 테넌트에서 해당 계획을 구입한 고객을 위해 위임된 리소스 그룹 및/또는 구독에 액세스할 수 있는 Azure Active Directory(Azure AD) 엔터티를 정의합니다. 포함된 모든 그룹(또는 사용자 또는 서비스 주체)에는 플랜을 구매하는 모든 고객에게 동일한 사용 권한이 있다는 점에 유의해야 합니다. 각 고객과 함께 작업할 다른 그룹을 할당하려면 각 고객에게만 할당된 별도의 [개인 요금제를](../../marketplace/private-offers.md) 게시해야 합니다.

## <a name="publish-your-offer"></a>제품 게시

모든 섹션을 완료한 후, 다음 단계는 Azure Marketplace에 제품을 게시하는 것입니다. **게시** 단추를 선택하여 제품을 라이브로 전환하는 프로세스를 시작합니다. 이 프로세스에 대한 자세한 내용은 [여기에서](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish)찾을 수 있습니다.

언제든지 [제품의 업데이트된 버전을 게시](../../marketplace/partner-center-portal/update-existing-offer.md)할 수 있습니다. 예를 들어 이전에 게시된 제품에 새 역할 정의를 추가하려고 할 수 있습니다. 이렇게 하면 제품을 이미 추가한 고객은 Azure Portal의 [**서비스 공급자**](view-manage-service-providers.md) 페이지에 업데이트를 사용할 수 있음을 알려 주는 아이콘이 표시됩니다. 각 고객은 [변경 내용을 검토](view-manage-service-providers.md#update-service-provider-offers)하고 새 버전으로 업데이트할지 여부를 결정할 수 있습니다. 

## <a name="the-customer-onboarding-process"></a>고객 온보딩 프로세스

고객이 제품을 추가하면 하나 이상의 특정 구독 또는 리소스 그룹을 위임할 수 있습니다. 그러면 [Azure 위임 리소스 관리에 온보딩](view-manage-service-providers.md#delegate-resources)됩니다. 고객이 제품을 수락했지만 아직 리소스를 위임하지 않은 경우 Azure Portal에서 [**서비스 공급자**](view-manage-service-providers.md) 페이지의 **공급자 제품** 섹션 맨 위에 메모가 표시됩니다.

> [!IMPORTANT]
> 위임은 온보온중인 구독에 대한 소유자 기본 제공 [역할이](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) 있는 고객의 테넌트에 게스트가 아닌 계정(또는 온보온 중인 리소스 그룹을 포함)에 의해 수행되어야 합니다. 구독을 위임할 수 있는 모든 사용자를 보기 위해 고객 테넌트의 사용자는 Azure Portal에서 구독을 선택하고, **IAM(액세스 제어)** 을 열고, [소유자 역할이 있는 모든 소유자를 볼 수 있습니다](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

고객이 구독(또는 구독 내의 하나 이상의 리소스 그룹)을 위임하면 **Microsoft.ManagedServices** 리소스 공급자가 해당 구독에 등록되고 테넌트의 사용자는 오퍼의 권한 부여에 따라 위임된 리소스에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [상업 시장에](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)대해 알아보십시오.
- [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)에 대해 알아봅니다.
- Azure Portal의 **내 고객**으로 이동하여 [고객을 보고 관리](view-manage-customers.md)합니다.
