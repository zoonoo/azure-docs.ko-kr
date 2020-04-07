---
title: 청사진 배포 단계
description: Blueprint 할당을 만드는 동안 Azure Blueprints 서비스가 수행하는 보안 및 아티팩트 관련 단계를 알아봅니다.
ms.date: 11/13/2019
ms.topic: conceptual
ms.openlocfilehash: 61d19c84cd659b9df3a272c5c2743944e51df06e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677324"
---
# <a name="stages-of-a-blueprint-deployment"></a>청사진 배포 단계

Blueprint가 배포되면 Azure Blueprints 서비스에서 일련의 작업을 수행하여 Blueprint에 정의된 리소스를 배포합니다. 이 문서에서는 각 단계와 관련된 사항에 대한 세부 정보를 제공합니다.

Blueprint 배포는 구독에 Blueprint를 할당하거나 [기존 할당을 업데이트하여](../how-to/update-existing-assignments.md)트리거됩니다. 배포 하는 동안 Azure Blueprints는 다음과 같은 상위 수준 단계를 수행합니다.

> [!div class="checklist"]
> - Azure Blueprint부여 소유자 권한
> - Blueprint 할당 개체가 작성됩니다.
> - 선택 사항 - Azure Blueprints는 **시스템 할당된** 관리되는 ID를 만듭니다.
> - 관리되는 ID는 Blueprint 아티팩트를 배포합니다.
> - Azure Blueprints 서비스 및 **시스템 할당 관리되는** ID 권한이 해지됩니다.

## <a name="azure-blueprints-granted-owner-rights"></a>Azure Blueprint부여 소유자 권한

Azure Blueprints 서비스 주체는 시스템 할당된 [관리되는 ID](../../../active-directory/managed-identities-azure-resources/overview.md) 관리 ID를 사용할 때 할당된 구독 또는 구독에 대한 소유자 권한을 부여합니다. 부여된 역할을 통해 Azure Blueprints를 만들고 나중에 **시스템에서 할당된** 관리되는 ID를 해지할 수 있습니다. **사용자 할당된** 관리 되는 ID를 사용 하는 경우 Azure Blueprints 서비스 주체는 받지 않으며 구독에 대 한 소유자 권한이 필요 하지 않습니다.

포털을 통해 할당이 완료되면 권한이 자동으로 부여됩니다. 그러나 REST API를 통해 할당이 수행되는 경우 별도의 API 호출을 사용하여 권한을 부여해야 합니다. Azure Blueprints AppId는 에서이지만 서비스 주체는 `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`테넌트에 따라 다릅니다. [Azure Active Directory 그래프 API](../../../active-directory/develop/active-directory-graph-api.md) 및 REST 엔드포인트 [서비스Principals를](/graph/api/resources/serviceprincipal) 사용하여 서비스 주체를 가져옵니다. 그런 다음 [포털,](../../../role-based-access-control/role-assignments-portal.md) [Azure CLI,](../../../role-based-access-control/role-assignments-cli.md) [Azure PowerShell,](../../../role-based-access-control/role-assignments-powershell.md) [REST API](../../../role-based-access-control/role-assignments-rest.md)또는 리소스 [관리자 템플릿을](../../../role-based-access-control/role-assignments-template.md)통해 Azure Blueprint에 _소유자_ 역할을 부여합니다.

Azure Blueprints 서비스는 리소스를 직접 배포하지 않습니다.

## <a name="the-blueprint-assignment-object-is-created"></a>Blueprint 할당 개체가 작성됩니다.

사용자, 그룹 또는 서비스 주체는 구독에 Blueprint를 할당합니다. 할당 개체는 Blueprint가 할당된 구독 수준에 있습니다. 배포에서 만든 리소스는 배포 엔터티의 컨텍스트에서 수행되지 않습니다.

Blueprint 할당을 만드는 동안 [관리되는 ID의](../../../active-directory/managed-identities-azure-resources/overview.md) 유형이 선택됩니다. 기본값은 **시스템에서 할당된** 관리 ID입니다. **사용자 할당된** 관리 ID를 선택할 수 있습니다. **사용자 지정** 관리 ID를 사용하는 경우 Blueprint 할당을 생성하기 전에 사용자 지정 관리 ID를 정의하고 권한을 부여해야 합니다. [소유자](../../../role-based-access-control/built-in-roles.md#owner) 및 [Blueprint 연산자](../../../role-based-access-control/built-in-roles.md#blueprint-operator) 기본 제공 `blueprintAssignment/write` 역할에는 **사용자가 할당한** 관리되는 ID를 사용하는 할당을 만드는 데 필요한 권한이 있습니다.

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>선택 사항 - Azure Blueprints는 시스템 할당된 관리되는 ID를 만듭니다.

할당 중에 [시스템 할당된 관리 ID를](../../../active-directory/managed-identities-azure-resources/overview.md) 선택하면 Azure Blueprints는 ID를 만들고 관리되는 ID를 [소유자](../../../role-based-access-control/built-in-roles.md#owner) 역할로 부여합니다. 기존 [할당이 업그레이드된](../how-to/update-existing-assignments.md)경우 Azure Blueprint는 이전에 만든 관리되는 ID를 사용합니다.

Blueprint 할당과 관련된 관리되는 ID는 Blueprint에 정의된 리소스를 배포하거나 다시 배포하는 데 사용됩니다. 이 디자인은 할당이 실수로 서로 간섭하는 것을 방지합니다.
이 디자인은 또한 Blueprint에서 배포된 각 리소스의 보안을 제어하여 [리소스 잠금](./resource-locking.md) 기능을 지원합니다.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>관리되는 ID는 Blueprint 아티팩트를 배포합니다.

그런 다음 관리되는 ID는 정의된 [시퀀싱 순서로](./sequencing-order.md)Blueprint 내의 아티팩트의 리소스 관리자 배포를 트리거합니다. 다른 아티팩트에 종속된 아티팩트가 올바른 순서로 배포되도록 순서를 조정할 수 있습니다.

배포에 의한 액세스 실패는 관리되는 ID에 부여된 액세스 수준의 결과인 경우가 많습니다. Azure Blueprints 서비스는 **시스템에서 할당된** 관리되는 ID의 보안 수명 주기를 관리합니다. 그러나 사용자는 **사용자가 할당한** 관리 ID의 권한 및 수명 주기를 관리할 책임이 있습니다.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Blueprint 서비스 및 시스템 할당 관리 ID 권한이 취소됩니다.

배포가 완료되면 Azure Blueprints는 구독에서 **시스템 할당된** 관리 ID의 권한을 취소합니다. 그런 다음 Azure Blueprints 서비스는 구독에서 해당 권한을 취소합니다. 권한 제거는 Azure Blueprint가 구독에서 영구 소유자가 되는 것을 방지합니다.

## <a name="next-steps"></a>다음 단계

- [정적 및 동적 매개 변수](parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결
