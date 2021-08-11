---
title: ServicePrincipalSelector UI 요소
description: Azure Portal용 Microsoft.Common.ServicePrincipalSelector UI 요소에 대해 설명합니다. 애플리케이션을 선택하는 컨트롤과 암호 또는 인증서 지문을 입력할 수 있는 텍스트 상자를 제공합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: tomfitz
ms.openlocfilehash: 2fdbbad467d8c762db485fc7935e9cef78313fd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184453"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Microsoft.Common.ServicePrincipalSelector UI element

사용자가 기존 [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 선택하거나 새 애플리케이션을 등록하는 데 사용할 수 있는 컨트롤입니다. **새로 만들기** 를 선택하는 경우, 단계에 따라 새 애플리케이션을 등록합니다. 기존 애플리케이션을 선택하는 경우 컨트롤은 암호나 인증서 지문을 입력하는 텍스트 상자를 제공합니다.

## <a name="ui-samples"></a>UI 샘플

기본 애플리케이션을 사용하거나 새 애플리케이션을 만들거나 기존 애플리케이션을 사용할 수 있습니다.

### <a name="use-default-application-or-create-new"></a>기본 애플리케이션 사용 또는 새로 만들기

기본 보기는 `defaultValue` 속성의 값에 따라 결정되고, **서비스 사용자 유형** 은 **새로 만들기** 로 설정됩니다. `principalId` 속성에 유효한 GUID(Globally Unique Identifier)가 포함된 경우 컨트롤은 애플리케이션의 `objectId`를 검색합니다. 사용자가 컨트롤에서 선택 영역을 만들지 않으면 기본값이 적용 됩니다.

새 애플리케이션을 등록하려면 **선택 영역 변경** 을 선택하고 **애플리케이션 등록** 대화 상자가 표시되도록 합니다. **이름**, **지원되는 계정 유형** 을 입력하고 **등록** 단추를 선택합니다.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-default.png" alt-text="Microsoft.Common.ServicePrincipalSelector 초기 뷰":::

새 애플리케이션을 등록한 후 **인증 유형** 을 사용하여 암호 또는 인증서 지문을 입력합니다.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-authenticate.png" alt-text="Microsoft.Common.ServicePrincipalSelector 인증":::

### <a name="use-existing-application"></a>기존 애플리케이션 사용

기존 애플리케이션을 사용하려면 **기존 항목 선택** 을 선택한 다음 **선택 영역 만들기** 를 선택 합니다. **애플리케이션 선택** 대화 상자를 사용하여 애플리케이션의 이름을 검색합니다. 결과에서 애플리케이션을 선택하고 **선택** 단추를 선택 합니다. 애플리케이션을 선택하면 컨트롤에 암호 또는 인증서 지문을 입력하는 **인증 유형** 이 표시됩니다.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-existing.png" alt-text="Microsoft.Common.ServicePrincipalSelector는 기존 애플리케이션을 선택합니다.":::

## <a name="schema"></a>스키마

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
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
  - `defaultValue`: 기본 `principalId` 및 `name`를 지정합니다.

- 선택적 속성은 다음과 같습니다.
  - `toolTip`: 각 레이블에 도구 설명 `infoBalloon`을 연결합니다.
  - `visible`: 컨트롤을 숨기거나 표시합니다.
  - `options`: 인증서 지문 옵션을 사용할 수 있는지 여부를 지정합니다.
  - `constraints`: 암호 유효성 검사를 위한 정규식 제약 조건입니다.

## <a name="example"></a>예제

다음은 `Microsoft.Common.ServicePrincipalSelector` 명령의 예입니다. `defaultValue` 속성은 `principalId`를 `<default guid>`을 기본 애플리케이션 식별자 GUID에 대한 자리 표시자로 설정합니다.

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
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
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

`appId`는 사용자가 선택하거나 만든 애플리케이션 등록의 ID입니다. `objectId`는 선택한 애플리케이션 등록에 대해 구성된 서비스 주체에 대한 개체 ID의 배열입니다.

컨트롤에 선택 영역이 없는 경우 `newOrExisting` 속성 값은 **새로운** 입니다.

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
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

`newOrExisting` 속성 값이 **기존** 인 컨트롤에서 **새로 만들기** 또는 기존 애플리케이션을 선택한 경우 다음과 같습니다.

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
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