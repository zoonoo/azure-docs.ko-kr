---
title: Azure Managed Applications |의 사용자 지정 역할 정의 개요 Microsoft Docs
description: 관리 되는 응용 프로그램에 대 한 사용자 지정 역할 정의를 만드는 개념을 설명 합니다.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7f4371bea467d6d4c99a776e03cdf13070d77ac6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818385"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Azure Managed Applications의 사용자 지정 역할 정의 아티팩트

사용자 지정 역할 정의는 관리 되는 응용 프로그램의 선택적 아티팩트입니다. 관리 되는 응용 프로그램에서 해당 기능을 수행 하는 데 필요한 권한을 결정 하는 데 사용 됩니다.

이 문서에서는 사용자 지정 역할 정의 아티팩트 및 해당 기능에 대 한 개요를 제공 합니다.

## <a name="custom-role-definition-artifact"></a>사용자 지정 역할 정의 아티팩트

사용자 지정 역할 정의 아티팩트 customRoleDefinition. json의 이름을 지정 해야 합니다. 관리 되는 응용 프로그램 정의를 만드는 .zip 패키지의 createUiDefinition. json 및 Maintemplate.json와 동일한 수준에 놓습니다. .Zip 패키지를 만들고 관리 되는 응용 프로그램 정의를 게시 하는 방법을 알아보려면 [관리 되는 응용 프로그램 정의 게시](publish-managed-app-definition-quickstart.md) 를 참조 하세요.

## <a name="custom-role-definition-schema"></a>사용자 지정 역할 정의 스키마

CustomRoleDefinition. json 파일에는 역할의 배열인 최상위 `roles` 속성이 있습니다. 이러한 역할은 관리 되는 응용 프로그램이 작동 하는 데 필요한 권한입니다. 현재 기본 제공 역할만 허용 되지만 여러 역할을 지정할 수 있습니다. 역할은 역할 정의의 ID 또는 역할 이름으로 참조할 수 있습니다.

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

역할은 `$.properties.roleName` 또는 `id`구성 됩니다.

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> `id` 또는 `roleName` 필드 중 하나를 사용할 수 있습니다. 하나만 필요 합니다. 이러한 필드는 적용 해야 하는 역할 정의를 조회 하는 데 사용 됩니다. 둘 다 제공 되는 경우 `id` 필드가 사용 됩니다.

|속성|Required?|설명|
|---------|---------|---------|
|id|예|기본 제공 역할의 ID입니다. 전체 ID 또는 GUID만 사용할 수 있습니다.|
|RoleName|예|기본 제공 역할의 이름입니다.|