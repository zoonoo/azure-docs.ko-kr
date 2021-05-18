---
title: 사용자 지정 역할 정의 개요
description: 관리되는 애플리케이션에 대한 사용자 지정 역할 정의를 만드는 개념을 설명합니다.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7b7fff913c177703f959bfa103c8e310d01059e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "81391836"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Azure Managed Applications의 사용자 지정 역할 정의 아티팩트

사용자 지정 역할 정의는 관리되는 애플리케이션의 선택적 아티팩트입니다. 관리되는 애플리케이션에서 해당 기능을 수행하는 데 필요한 권한을 결정하는 데 사용됩니다.

해당 문서에서는 사용자 지정 역할 정의 아티팩트 및 해당 기능에 대한 개요를 제공합니다.

## <a name="custom-role-definition-artifact"></a>사용자 지정 역할 정의 아티팩트

사용자 지정 역할 정의 아티팩트의 이름을 customRoleDefinition.json으로 지정해야 합니다. createUiDefinition.json 및 관리되는 애플리케이션 정의를 만드는 .zip 패키지의 mainTemplate.json과 동일한 수준으로 배치합니다. .zip 패키지를 만드는 방법과 관리되는 애플리케이션 정의를 게시하는 방법을 알아보려면 [Azure 관리되는 애플리케이션 정의 게시](publish-service-catalog-app.md)를 참조하세요.

## <a name="custom-role-definition-schema"></a>사용자 지정 역할 정의 스키마

customRoleDefinition.json 파일에는 역할 배열인 최상위 `roles` 속성이 있습니다. 이러한 역할은 관리되는 애플리케이션이 작동하는 데 필요한 권한입니다. 현재는 기본 제공 역할만 허용되지만 여러 역할을 지정할 수 있습니다. 역할은 역할 정의의 ID 또는 역할 이름으로 참조할 수 있습니다.

사용자 지정 역할 정의에 대한 샘플 JSON:

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="roles"></a>역할

역할은 `$.properties.roleName` 또는 `id`로 구성됩니다.

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> `id` 또는 `roleName` 필드 중 하나를 사용할 수 있습니다. 하나만 필요합니다. 이러한 필드는 적용해야 하는 역할 정의를 조회하는 데 사용됩니다. 둘 다 제공된 경우 `id` 필드가 사용됩니다.

|속성|필수 여부|Description|
|---------|---------|---------|
|id|예|기본 제공 역할의 ID입니다. 전체 ID 또는 GUID만 사용할 수 있습니다.|
|roleName|예|기본 제공 역할의 이름입니다.|