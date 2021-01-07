---
title: 사용자 지정 역할 정의 개요
description: 관리 되는 응용 프로그램에 대 한 사용자 지정 역할 정의를 만드는 개념을 설명 합니다.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7b7fff913c177703f959bfa103c8e310d01059e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81391836"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Azure Managed Applications의 사용자 지정 역할 정의 아티팩트

사용자 지정 역할 정의는 관리 되는 응용 프로그램의 선택적 아티팩트입니다. 관리 되는 응용 프로그램에서 해당 기능을 수행 하는 데 필요한 권한을 결정 하는 데 사용 됩니다.

이 문서에서는 사용자 지정 역할 정의 아티팩트 및 해당 기능에 대 한 개요를 제공 합니다.

## <a name="custom-role-definition-artifact"></a>사용자 지정 역할 정의 아티팩트

customRoleDefinition.js사용자 지정 역할 정의 아티팩트의 이름을 지정 해야 합니다. createUiDefinition.js와 동일한 수준으로 저장 하 고, 관리 되는 응용 프로그램 정의를 만드는 .zip 패키지에서 mainTemplate.js합니다. .Zip 패키지를 만들고 관리 되는 응용 프로그램 정의를 게시 하는 방법을 알아보려면 [관리 되는 응용 프로그램 정의 게시](publish-service-catalog-app.md) 를 참조 하세요.

## <a name="custom-role-definition-schema"></a>사용자 지정 역할 정의 스키마

파일의 customRoleDefinition.js에는 `roles` 역할 배열인 최상위 속성이 있습니다. 이러한 역할은 관리 되는 응용 프로그램이 작동 하는 데 필요한 권한입니다. 현재 기본 제공 역할만 허용 되지만 여러 역할을 지정할 수 있습니다. 역할은 역할 정의의 ID 또는 역할 이름으로 참조할 수 있습니다.

사용자 지정 역할 정의에 대 한 샘플 JSON:

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

역할은 또는로 구성 됩니다 `$.properties.roleName` `id` .

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> 또는 필드 중 하나를 사용할 수 있습니다 `id` `roleName` . 하나만 필요 합니다. 이러한 필드는 적용 해야 하는 역할 정의를 조회 하는 데 사용 됩니다. 둘 다 제공 된 경우 `id` 필드가 사용 됩니다.

|속성|필수 여부|Description|
|---------|---------|---------|
|id|예|기본 제공 역할의 ID입니다. 전체 ID 또는 GUID만 사용할 수 있습니다.|
|roleName|예|기본 제공 역할의 이름입니다.|