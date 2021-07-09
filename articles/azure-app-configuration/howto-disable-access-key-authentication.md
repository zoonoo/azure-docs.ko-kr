---
title: Azure App Configuration 인스턴스에 대한 액세스 키 인증 사용 안 함(미리 보기)
titleSuffix: Azure App Configuration
description: Azure App Configuration 인스턴스에 대한 액세스 키 인증을 사용하지 않도록 설정하는 방법 알아보기(미리 보기)
ms.service: azure-app-configuration
author: jimmyca15
ms.author: jimmyca
ms.topic: how-to
ms.date: 5/14/2021
ms.openlocfilehash: 451d9701b62cf46fe81bdd17f4eded63a38d1dce
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483489"
---
# <a name="disable-access-key-authentication-for-an-azure-app-configuration-instance-preview"></a>Azure App Configuration 인스턴스에 대한 액세스 키 인증 사용 안 함(미리 보기)

Azure App Configuration 리소스에 대한 모든 요청은 인증되어야 합니다. 기본적으로 요청은 Azure AD(Azure Active Directory) 자격 증명 또는 액세스 키를 사용하여 인증할 수 있습니다. 이 두가지 유형의 인증 체계 중에서 Azure AD는 액세스 키에 대해 우수한 보안과 사용 편의성을 제공하며 Microsoft에서 권장합니다. 클라이언트가 Azure AD를 사용하여 요청을 인증하도록 요구하려면 Azure App Configuration 리소스에 대한 액세스 키 사용을 사용하지 않도록 설정할 수 있습니다.

Azure App Configuration 리소스에 대한 액세스 키 인증을 사용하지 않도록 설정하면 해당 리소스에 대한 모든 기존 액세스 키가 삭제됩니다. 기존 액세스 키를 사용하는 리소스에 대한 후속 요청은 거부됩니다. Azure AD를 사용하여 인증된 요청만 성공합니다. Azure AD 사용에 대한 자세한 내용은 [Azure Active Directory를 사용하여 Azure App Configuration에 대한 액세스 권한 부여](./concept-enable-rbac.md)를 참조하세요.

## <a name="disable-access-key-authentication"></a>액세스 키 인증 사용 안 함

액세스 키 인증을 사용하지 않도록 하면 모든 액세스 키가 삭제됩니다. 실행 중인 애플리케이션이 인증에 액세스 키를 사용하는 경우 액세스 키 인증을 사용하지 않도록 설정하면 실패하기 시작합니다. 액세스 키 인증을 다시 사용하도록 설정하면 새 액세스 키 집합이 생성되고 이전 액세스 키를 사용하려는 모든 애플리케이션이 계속 실패합니다.

> [!WARNING]
> 클라이언트가 현재 액세스 키를 사용하여 Azure 앱 구성 리소스의 데이터에 액세스하는 경우 액세스 키 인증을 사용하지 않도록 설정하기 전에 해당 클라이언트를 [Azure AD](./concept-enable-rbac.md)로 마이그레이션하는 것이 좋습니다.
> 또한 아래의 [제한 사항](#limitations) 섹션을 읽고 제한 사항이 리소스의 의도된 사용에 영향을 미치지 않는지 확인하는 것이 좋습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal Azure App Configuration 리소스에 대한 액세스 키 인증을 허용되지 않는 경우 다음 단계를 수행합니다.

1. Azure Portal에서 Azure 앱 구성 리소스로 이동합니다.
2. **설정** 에서 **액세스 키** 설정을 찾습니다.

    :::image type="content" border="true" source="./media/access-keys-blade.png" alt-text="Azure 앱 구성 리소스 액세스 키 블레이드에 액세스하는 방법을 보여 주는 스크린샷":::

3. **액세스 키 사용** 토글을 **사용 안함** 으로 설정합니다.

    :::image type="content" border="true" source="./media/disable-access-keys.png" alt-text="Azure 앱 구성에 대한 액세스 키 인증을 비활성화하는 방법을 보여 주는 스크린샷":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 사용하여 액세스 키 인증을 사용하지 않도록 설정하는 기능은 개발 중입니다.

---

### <a name="verify-that-access-key-authentication-is-disabled"></a>액세스 키 인증을 사용할 수 없는지 확인

액세스 키 인증이 더 이상 허용되지 않는지 확인하기 위해 Azure 앱 구성 리소스에 대한 액세스 키를 나열하는 요청을 수행할 수 있습니다. 액세스 키 인증을 사용하지 않도록 설정한 경우에는 액세스 키가 없으며 목록 작업에서 빈 목록이 반환됩니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 Azure 앱 구성 리소스에 대한 액세스 키 인증을 사용하지 않도록 설정되었는지 확인하려면 다음 단계를 수행합니다.

1. Azure Portal에서 Azure 앱 구성 리소스로 이동합니다.
2. **설정** 에서 **액세스 키** 설정을 찾습니다.

    :::image type="content" border="true" source="./media/access-keys-blade.png" alt-text="Azure 앱 구성 리소스 액세스 키 블레이드에 액세스하는 방법을 보여 주는 스크린샷":::

3. 액세스 키가 표시되지 않고 **액세스 키 사용** 이 **사용 안함** 으로 전환되었는지 확인합니다.

    :::image type="content" border="true" source="./media/access-keys-disabled-portal.png" alt-text="Azure 앱 구성 리소스에 대해 비활성화되는 액세스 키를 보여 주는 스크린샷":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Portal에서 Azure 앱 구성 리소스에 대한 액세스 키 인증을 사용하지 않는지 확인하려면 다음 명령을 사용합니다. 이 명령은 Azure 앱 구성 리소스에 대한 액세스 키를 나열하고, 액세스 키 인증을 사용하지 않도록 설정된 경우 목록이 비어 있게 됩니다.

```azurecli-interactive
az appconfig credential list \
    --name <app-configuration-name> \
    --resource-group <resource-group>
```

액세스 키 인증을 사용하지 않도록 설정하면 빈 목록이 반환됩니다.

```
C:\Users\User>az appconfig credential list -g <resource-group> -n <app-configuration-name>
[]
```

---

## <a name="permissions-for-allowing-or-disallowing-access-key-authentication"></a>액세스 키 인증을 허용하거나 허용하지 않는 권한

Azure 앱 구성 리소스에 대한 액세스 키 인증의 상태를 수정하려면 사용자에게 Azure 앱 구성 리소스를 만들고 관리할 수 있는 권한이 있어야 합니다. 이러한 권한을 제공하는 Azure RBAC(Azure 역할 기반 액세스 제어) 역할에는 **Microsoft.AppConfiguration/configurationStores/write** 또는 **Microsoft.AppConfiguration/configurationStores/\*** 작업이 포함됩니다. 이 작업이 포함된 기본 제공 역할은 다음과 같습니다.

- Azure Resource Manager [소유자](../role-based-access-control/built-in-roles.md#owner) 역할
- Azure Resource Manager [기여자](../role-based-access-control/built-in-roles.md#contributor) 역할

이러한 역할은 Azure AD(Azure Active Directory)를 통해 Azure 앱 구성 리소스의 데이터에 대한 액세스를 제공하지 않습니다. 그러나 여기에는 리소스의 액세스 키에 대한 액세스 권한을 부여하는 **Microsoft.AppConfiguration/configurationStores/listKeys/action** 작업 권한이 포함됩니다. 이 권한이 있는 사용자는 액세스 키를 사용하여 리소스의 모든 데이터에 액세스할 수 있습니다.

사용자가 리소스에 대한 액세스 키 인증을 허용하거나 허용하지 않도록 허용하려면 역할 할당의 범위를 Azure 앱 구성 리소스 수준 이상으로 지정해야 합니다. 역할 범위에 대한 자세한 내용은 [Azure RBAC의 범위 이해](../role-based-access-control/scope-overview.md)를 참조하세요.

이러한 역할은 App Configuration 리소스를 만들거나 해당 속성을 업데이트하는 기능이 필요한 사용자에게만 제한적으로 할당해야 합니다. 최소 권한의 원칙을 사용하여 사용자에게 작업을 수행하는 데 필요한 최소 권한을 부여합니다. Azure RBAC를 사용하여 액세스를 관리하는 방법에 대한 자세한 내용은 [Azure RBAC 모범 사례](../role-based-access-control/best-practices.md)를 참조하세요.

> [!NOTE]
> 클래식 구독 관리자 역할인 서비스 관리자 및 공동 관리자에는 Azure Resource Manager [소유자](../role-based-access-control/built-in-roles.md#owner) 역할에 해당하는 항목이 포함됩니다. **소유자** 역할은 모든 작업을 포함하므로 이러한 관리 역할 중 하나가 있는 사용자는 App Configuration 리소스를 만들고 관리할 수도 있습니다. 자세한 내용은 [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 관리자 역할](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)을 참조하세요.

## <a name="limitations"></a>제한 사항

액세스 키 인증을 사용하지 않도록 설정하는 기능은 미리 보기로 제공됩니다. 현재 적용 중인 제한 사항은 다음과 같습니다.

### <a name="arm-template-access"></a>ARM 템플릿 액세스

액세스 키 인증이 비활성화되면 [ARM 템플릿](./quickstart-resource-manager.md)의 키-값 읽기/쓰기 기능도 비활성화됩니다. ARM 템플릿에 사용되는 Microsoft AppConfiguration/configurationStores/keyValues 리소스에 액세스하려면 기여자 또는 소유자와 같은 Azure Resource Manager 역할이 필요하기 때문입니다. 액세스 키 인증이 비활성화되면 리소스에 대한 액세스에는 Azure 앱 구성 [데이터 영역 역할](concept-enable-rbac.md) 중 하나가 필요하므로 ARM 템플릿 액세스가 거부됩니다.

## <a name="next-steps"></a>다음 단계

- [고객 관리 키를 사용하여 앱 구성 데이터 암호화](concept-customer-managed-keys.md)
- [Azure App Configuration의 프라이빗 엔드포인트 사용](concept-private-endpoint.md)