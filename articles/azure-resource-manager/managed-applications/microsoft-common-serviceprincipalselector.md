---
title: ServicePrincipalSelector UI 요소
description: Azure Portal에 대 한 ServicePrincipalSelector UI 요소에 대해 설명 합니다. 응용 프로그램 식별자를 선택 하 고 암호 또는 인증서 지문을 입력 하는 입력란을 제공 합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: tomfitz
ms.openlocfilehash: 06fa5868cf0a907e268a347cfdfa2b5ee20908e3
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91571464"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>ServicePrincipalSelector UI 요소

사용자가 기존 서비스 주체를 선택 하거나 새 서비스 주체를 등록할 수 있도록 하는 컨트롤입니다. **새로 만들기**를 선택 하는 경우 새 응용 프로그램을 등록 하는 단계를 진행 합니다. 기존 응용 프로그램을 선택 하는 경우 컨트롤은 암호나 인증서 지문을 입력 하는 입력란을 제공 합니다.

## <a name="ui-sample"></a>UI 샘플

기본 보기는 속성의 값에 의해 결정 됩니다 `defaultValue` . 속성에 `principalId` 유효한 guid (globally unique identifier)가 포함 된 경우 컨트롤은 응용 프로그램의 개체 ID를 검색 합니다. 사용자가 드롭다운에서 선택 하지 않으면 기본값이 적용 됩니다.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-initial.png" alt-text="ServicePrincipalSelector 초기 뷰":::

드롭다운에서 새 응용 프로그램 식별자 또는 기존 응용 프로그램 식별자 **만들기** 를 선택 하면 텍스트 상자에 암호 또는 인증서 지문을 입력 하는 **인증 유형이** 표시 됩니다.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-selection.png" alt-text="ServicePrincipalSelector 초기 뷰":::

## <a name="schema"></a>스키마

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>설명

- 필요한 속성은 다음과 같습니다.
  - `name`
  - `type`
  - `label`
  - `defaultValue`: 기본 및을 지정 합니다 `principalId` `name` .

- 선택적 속성은 다음과 같습니다.
  - `toolTip`: 각 레이블에 도구 설명을 연결 `infoBalloon` 합니다.
  - `visible`: 컨트롤을 숨기 거 나 표시 합니다.
  - `options`: 인증서 손 도장 (thumbprint) 옵션을 사용할 수 있는지 여부를 지정 합니다.
  - `constraints`: 암호 유효성 검사를 위한 Regex 제약 조건입니다.

## <a name="example"></a>예제

다음은 컨트롤의 예제입니다 `Microsoft.Common.ServicePrincipalSelector` . `defaultValue`속성은 `principalId` 를 `<default guid>` 기본 응용 프로그램 식별자 GUID에 대 한 자리 표시자로 설정 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>예제 출력

는 `appId` 선택 하거나 만든 응용 프로그램 등록의 Id입니다. 는 `objectId` 선택한 응용 프로그램 등록에 대해 구성 된 서비스 사용자에 대 한 objectid의 배열입니다.

드롭다운에서 선택 항목이 없으면 `newOrExisting` 속성 값은 **new**입니다.

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": "<default guid>"
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

드롭다운 목록에서 **새로 만들기** 또는 기존 응용 프로그램 식별자를 선택 하면 `newOrExisting` **기존**속성 값은 다음과 같습니다.

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": "<guid>"
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>다음 단계

- UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
- UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
