---
title: 사용자 지정 역할 정의 개요
description: 관리되는 응용 프로그램에 대한 사용자 지정 역할 정의를 만드는 개념을 설명합니다.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 88e42fd9626276f6c77b46b33c138407f91d06ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650761"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Azure 관리 응용 프로그램의 사용자 지정 역할 정의 아티팩트

사용자 지정 역할 정의는 관리되는 응용 프로그램의 선택적 아티팩트입니다. 관리되는 응용 프로그램이 해당 기능을 수행하는 데 필요한 권한을 결정하는 데 사용됩니다.

이 문서에서는 사용자 지정 역할 정의 아티팩트 및 해당 기능에 대한 개요를 제공합니다.

## <a name="custom-role-definition-artifact"></a>사용자 지정 역할 정의 아티팩트

사용자 지정 역할 정의 아티팩트 의 이름을 지정해야 합니다. 관리되는 응용 프로그램 정의를 만드는 .zip 패키지에서 createUiDefinition.json 및 mainTemplate.json과 동일한 수준에 배치합니다. .zip 패키지를 만들고 관리되는 응용 프로그램 정의를 게시하는 방법을 알아보려면 [관리되는 응용 프로그램 정의 게시를 참조하십시오.](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>사용자 지정 역할 정의 스키마

customRoleDefinition.json 파일에는 역할의 `roles` 배열인 최상위 속성이 있습니다. 이러한 역할은 관리되는 응용 프로그램이 작동해야 하는 권한입니다. 현재는 기본 제공 역할만 허용되지만 여러 역할을 지정할 수 있습니다. 역할은 역할 정의의 ID 또는 역할 이름으로 참조할 수 있습니다.

사용자 지정 역할 정의에 대한 JSON 샘플:

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

역할은 `$.properties.roleName` 다음 또는 다음 `id`으로 구성됩니다.

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> 또는 `roleName` 필드 중 `id` 하나를 사용할 수 있습니다. 하나만 필요합니다. 이러한 필드는 적용해야 하는 역할 정의를 찾는 데 사용됩니다. 둘 다 제공된 `id` 경우 필드가 사용됩니다.

|속성|필수 여부|설명|
|---------|---------|---------|
|id|yes|기본 제공 역할의 ID입니다. 전체 ID 또는 GUID만 사용할 수 있습니다.|
|Rolename|yes|기본 제공 역할의 이름입니다.|