---
title: 청사진 배포 단계
description: 청사진 할당을 만드는 동안 Azure Blueprints 서비스가 거치는 보안 및 아티팩트 관련 단계를 알아봅니다.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 473f8c7957994401ea6000ecc0d8023a89f8c349
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560151"
---
# <a name="stages-of-a-blueprint-deployment"></a>청사진 배포 단계

청사진을 배포할 때 Azure Blueprints 서비스에서 청사진에 정의된 리소스를 배포하기 위해 일련의 작업을 수행합니다. 이 문서에서는 각 단계에 대한 세부 정보를 제공합니다.

청사진 배포는 구독에 청사진을 할당하거나 [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방식으로 트리거됩니다. 배포하는 동안 Azure Blueprints는 다음과 같은 개략적인 단계를 수행합니다.

> [!div class="checklist"]
> - Azure Blueprints에서 소유자 권한을 부여합니다.
> - 청사진 할당 개체가 만들어집니다.
> - 선택 사항 - Azure Blueprints는 **시스템 할당** 관리 ID를 만듭니다.
> - 관리 ID가 청사진 아티팩트를 배포합니다.
> - Azure Blueprints 서비스 및 **시스템 할당** 관리 ID 권한이 해지됩니다.

## <a name="azure-blueprints-granted-owner-rights"></a>Azure Blueprints에서 소유자 권한을 부여합니다.

[시스템 할당 관리 ID](../../../active-directory/managed-identities-azure-resources/overview.md) 관리 ID를 사용하는 경우 Azure Blueprints 서비스 주체에는 할당된 구독 또는 구독에 대한 소유자 권한이 부여됩니다. 부여된 역할을 통해 Azure Blueprints는 **시스템 할당** 관리 ID를 만들고 나중에 해지할 수 있습니다. **사용자 할당** 관리 ID를 사용하는 경우 Azure Blueprints 서비스 주체는 구독에 대한 소유자 권한이 부여되지 않으며 필요하지도 않습니다.

포털을 통해 할당이 수행될 경우 권한이 자동으로 부여됩니다. 그러나 REST API를 통해 할당을 수행하는 경우 별도의 API 호출을 사용하여 권한을 부여해야 합니다. Azure Blueprints AppId는 `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`이지만 서비스 주체는 테넌트별로 다릅니다. [Azure Active Directory Graph API](/graph/migrate-azure-ad-graph-planning-checklist) 및 REST 엔드포인트 [servicePrincipals](/graph/api/resources/serviceprincipal)를 사용하여 서비스 주체를 가져옵니다. 그런 다음, [포털](../../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [REST API](../../../role-based-access-control/role-assignments-rest.md) 또는 [Azure Resource Manager 템플릿](../../../role-based-access-control/role-assignments-template.md)을 통해 Azure Blueprints에 _소유자_ 역할을 부여합니다.

Azure Blueprints 서비스는 리소스를 직접 배포하지 않습니다.

## <a name="the-blueprint-assignment-object-is-created"></a>청사진 할당 개체가 만들어집니다.

사용자, 그룹 또는 서비스 주체가 청사진을 구독에 할당합니다. 할당 개체는 청사진이 할당된 구독 수준에 있습니다. 배포를 통해 생성된 리소스는 배포하는 엔터티의 컨텍스트에서 수행되지 않습니다.

청사진 할당을 만드는 동안 [관리 ID](../../../active-directory/managed-identities-azure-resources/overview.md) 유형이 선택됩니다. 기본값은 **시스템 할당** 관리 ID입니다. **사용자 할당** 관리 ID를 선택할 수 있습니다. **사용자 할당** 관리 ID를 사용하는 경우 청사진 할당을 만들기 전에 해당 ID를 정의하고 권한을 부여해야 합니다. [소유자](../../../role-based-access-control/built-in-roles.md#owner) 및 [청사진 운영자](../../../role-based-access-control/built-in-roles.md#blueprint-operator) 기본 제공 역할에는 `blueprintAssignment/write` **사용자 할당** 관리 ID를 사용하는 할당을 만드는 데 필요한 권한이 있습니다.

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>선택 사항 - Azure Blueprints는 시스템 할당 관리 ID를 만듭니다.

할당 중에 [시스템 할당 관리 ID](../../../active-directory/managed-identities-azure-resources/overview.md)를 선택하면 Azure Blueprints에서 ID를 만들고 관리 ID에 [소유자](../../../role-based-access-control/built-in-roles.md#owner) 역할을 부여합니다. [기존 할당이 업그레이드되면](../how-to/update-existing-assignments.md) Azure Blueprints는 이전에 만든 관리 ID를 사용합니다.

청사진 할당과 관련된 관리 ID는 청사진에 정의된 리소스를 배포하거나 다시 배포하는 데 사용됩니다. 이 디자인은 실수로 서로 방해하는 할당을 방지합니다.
또한 이 디자인은 청사진에서 배포된 각 리소스의 보안을 제어하여 [리소스 잠금](./resource-locking.md) 기능을 지원합니다.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>관리 ID가 청사진 아티팩트를 배포합니다.

그런 다음, 관리 ID는 정의된 [시퀀싱 순서로](./sequencing-order.md) 청사진 내에 포함된 아티팩트의 Resource Manager 배포를 트리거합니다. 다른 아티팩트에 종속된 아티팩트를 올바른 순서로 배포하도록 순서를 조정할 수 있습니다.

배포 시의 액세스 오류는 관리 ID에 부여된 액세스 수준으로 인해 발생하는 경우가 많습니다. Azure Blueprints 서비스는 **시스템 할당** 관리 ID의 보안 수명 주기를 관리합니다. 그러나 사용자는 **사용자 할당** 관리 ID의 권한 및 수명 주기를 관리해야 합니다.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Blueprints 서비스 및 시스템 할당 관리 ID 권한이 해지됨

배포가 완료되면 Azure Blueprints는 구독에서 **시스템 할당** 관리 ID의 권한을 해지합니다. 그런 다음, Azure Blueprints 서비스는 구독에서 해당 권한을 해지합니다. 권한 제거를 통해 Azure Blueprints는 구독의 영구 소유자가 되지 못합니다.

## <a name="next-steps"></a>다음 단계

- [정적 및 동적 매개 변수](./parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](./sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](./resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결