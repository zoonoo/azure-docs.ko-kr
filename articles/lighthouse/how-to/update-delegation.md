---
title: 위임 업데이트
description: 이전에 Azure Lighthouse에 등록 한 고객에 대 한 위임을 업데이트 하는 방법에 대해 알아봅니다.
ms.date: 12/03/2020
ms.topic: how-to
ms.openlocfilehash: 90827281ac9b05105700298494af0b60b0fa511f
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96610903"
---
# <a name="update-a-delegation"></a>위임 업데이트

구독 (또는 리소스 그룹)을 Azure Lighthouse에 등록 한 후에는 변경 해야 할 수 있습니다. 예를 들어 고객이 다른 Azure 기본 제공 역할이 필요한 추가 관리 작업을 수행 하거나 고객 구독이 위임 되는 테 넌 트를 변경 해야 할 수 있습니다.

> [!TIP]
> 이 항목의 서비스 공급자 및 고객을 참조 하지만 [여러 테 넌 트를 관리](../concepts/enterprise.md) 하는 기업은 동일한 프로세스를 사용 하 여 Azure Lighthouse를 설정 하 고 관리 환경을 통합할 수 있습니다.

[ARM (Azure Resource Manager) 템플릿을 통해 고객을 등록](onboard-customer.md)하는 경우 해당 고객에 대 한 새 배포를 수행 해야 합니다. 변경 하는 내용에 따라 원래 제안을 업데이트 하거나 원래 제품을 제거 하 고 새 제품을 만들 수 있습니다.

- **권한 부여를 변경 하는 경우**: ARM 템플릿의 **권한 부여** 섹션만 변경 하 여 위임을 업데이트할 수 있습니다.
- **관리 테 넌 트를 변경 하는 경우**: 이전 제품과 다른 **mspOfferName** 을 사용 하 여 새 ARM 템플릿을 만들어야 합니다.

## <a name="update-your-arm-template"></a>ARM 템플릿 업데이트

위임을 업데이트 하려면 원하는 변경 내용을 포함 하는 ARM 템플릿을 배포 해야 합니다.

이전에 포함 된 역할을 사용 하 여 새 사용자 그룹을 추가 하거나 기존 사용자에 대 한 역할을 변경 하는 것과 같이 권한 부여만 업데이트 하는 경우에는 이전 위임에 사용한 [ARM 템플릿과](onboard-customer.md#create-an-azure-resource-manager-template) 동일한 **mspOfferName** 를 사용할 수 있습니다. 이전 템플릿을 시작 지점으로 사용할 수 있습니다. 그런 다음 Azure 기본 제공 역할을 다른 역할로 바꾸거나 템플릿에 완전히 새로운 권한 부여를 추가 하는 등 필요한 사항을 변경 합니다.

**MspOfferName** 를 변경 하는 경우이는 별도의 새로운 제품으로 간주 됩니다. 이는 관리 테 넌 트를 변경 하는 경우에 필요 합니다.

관리 테 넌 트가 동일 하 게 유지 되는 경우에는 **mspOfferName** 를 변경할 필요가 없습니다. 대부분의 경우 동일한 고객에 게 하나의 **mspOfferName** 를 사용 하 고 테 넌 트를 관리 하는 것이 좋습니다. 변경 하도록 선택 하는 경우에는 새 항목을 배포 하기 전에 고객의 이전 위임이 제거 되었는지를 먼저 제거 해야 합니다.

## <a name="remove-the-previous-delegation"></a>이전 위임 제거

새 배포를 수행 하기 전에 [이전 위임에 대 한 액세스 권한을 제거](remove-delegation.md)하는 것이 좋습니다. 이렇게 하면 앞으로 적용 해야 하는 정확한 사용자/그룹 및 역할로 정리를 시작할 수 있도록 모든 이전 권한이 제거 됩니다.

> [!IMPORTANT]
> 새 **mspOfferName** 을 사용 하 고 동일한 **principalid** 값을 유지 하는 경우 새 제품을 배포 하기 전에 이전 위임에 대 한 액세스 권한을 제거 해야 합니다. 제안을 먼저 제거 하지 않으면 이전에 사용 권한을 부여 받은 사용자는 충돌 하는 할당으로 인해 완전히 액세스할 수 없게 됩니다.

관리 테 넌 트를 변경 하는 경우 두 테 넌 트 모두에 계속 액세스 하려면 이전 제안을 그대로 둘 수 있습니다. 새 관리 테 넌 트에만 액세스할 수 있도록 하려면 이전 제품을 제거 해야 합니다. 새 제품을 등록 하기 전이나 후에이 작업을 수행할 수 있습니다.

제품을 업데이트 하 여 인증만을 조정 하 고 동일한 **mspOfferName** 를 유지 하는 경우에는 이전 위임을 제거할 필요가 없습니다. 새 배포가 이전 위임을 대체 하 고 최신 템플릿의 권한 부여만 적용 됩니다.

:::image type="content" source="../media/update-delegation.jpg" alt-text="MspOfferName를 변경 하 고 이전 위임을 제거 하는 경우를 보여 주는 다이어그램입니다.":::

위임에 대 한 액세스를 제거 하는 작업은 원래 위임에서 [관리 되는 서비스 등록 할당 삭제 역할이](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) 부여 된 관리 테 넌 트의 모든 사용자가 수행할 수 있습니다. 관리 테 넌 트에이 역할이 있는 사용자가 없는 경우 고객에 게 [Azure Portal 제품에 대 한 액세스 권한을 제거](view-manage-service-providers.md#add-or-remove-service-provider-offers)하도록 요청할 수 있습니다.

> [!TIP]
> 위의 단계를 수행 하 여 이전 위임을 제거 하 고 여전히 새 ARM 템플릿을 배포할 수 없는 경우 [등록 정의를 완전히 제거](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition)해야 할 수 있습니다. 고객 테 넌 트에서 소유자 역할을 하는 모든 사용자가이 작업을 수행할 수 있습니다.  

## <a name="deploy-the-arm-template"></a>ARM 템플릿 배포

사용자는 이전에 수행한 것과 동일한 방식으로 [업데이트 된 템플릿을 배포할](onboard-customer.md#deploy-the-azure-resource-manager-templates) 수 있습니다. Azure Portal에서 PowerShell을 사용 하거나 Azure CLI를 사용 하 여 배포할 수 있습니다.

배포가 완료 된 후에 성공 했는지 [확인](onboard-customer.md#confirm-successful-onboarding)합니다. 업데이트 된 권한 부여는 고객이 위임한 구독 또는 리소스 그룹에 적용 됩니다.

## <a name="updating-managed-service-offers"></a>관리 서비스 제공 업데이트

Azure Marketplace에 게시 된 관리 서비스 제품을 통해 고객에 게 등록 인증을 업데이트 하려는 경우 해당 고객에 대 한 계획에서 업데이트를 사용할 [권한 부여](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#authorization) 와 함께 [새 버전의 제품을 게시](../../marketplace/partner-center-portal/update-existing-offer.md) 하 여 위임을 업데이트할 수 있습니다. 그러면 고객은 Azure Portal에서 최신 버전으로 업데이트할 수 있습니다.

관리 테 넌 트를 변경 하려는 경우 고객이 허용할 [새 관리 서비스 제품을 만들고 게시](../../marketplace/partner-center-portal/create-new-managed-service-offer.md) 해야 합니다.

> [!TIP]
> 앞서 설명한 것 처럼 동일한 고객 및 테 넌 트 관리 간에 여러 다른 제안을 사용 하지 않는 것이 좋습니다. 동일 하 게 관리 되는 테 넌 트를 사용 하는 동일한 고객에 대 한 새 제품을 게시 하는 경우 고객이 최신 제품을 수락 하기 전에 이전 제품이 제거 되었는지 확인해 두어야 합니다.

## <a name="next-steps"></a>다음 단계

- Azure Portal의 **내 고객** 으로 이동하여 [고객을 보고 관리](view-manage-customers.md)합니다.
- 이전에 등록된 [위임에 대한 액세스 권한 제거](remove-delegation.md) 방법을 알아봅니다.
