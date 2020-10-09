---
title: CreateUiDefinition 아티팩트
description: Azure 관리 되는 응용 프로그램에 대 한 createUiDefinition 아티팩트를 만드는 방법을 보여 줍니다. 파일의 이름은 createUiDefinition.js설정 되어 있습니다.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 9be1cb02c419314a33b9487bf183ef3659b2b549
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75651489"
---
# <a name="reference-user-interface-elements-artifact"></a>참조: 사용자 인터페이스 요소 아티팩트

이 문서는 Azure Managed Applications 아티팩트의 *createUiDefinition.js* 에 대 한 참조입니다. 사용자 인터페이스 요소를 작성 하는 방법에 대 한 자세한 내용은 [사용자 인터페이스 요소 만들기](create-uidefinition-elements.md)를 참조 하세요.

## <a name="user-interface-elements"></a>사용자 인터페이스 요소

다음 JSON은 Azure Managed Applications 파일 * 에createUiDefinition.js* 의 예를 보여 줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "funcname",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the function to be created",
            "toolTip": "Name of the function to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "storagename",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the storage to be created",
            "toolTip": "Name of the storage to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "zipFileBlobUri",
            "type": "Microsoft.Common.TextBox",
            "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
            "label": "The Uri to the uploaded function zip file",
            "toolTip": "The Uri to the uploaded function zip file",
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "funcname": "[steps('applicationSettings').funcname]",
      "storageName": "[steps('applicationSettings').storagename]",
      "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

- [자습서: 사용자 지정 작업 및 리소스를 사용 하 여 관리 되는 응용 프로그램 만들기](tutorial-create-managed-app-with-custom-provider.md)
- [참조: 배포 템플릿 아티팩트](reference-main-template-artifact.md)
- [참조: 뷰 정의 아티팩트](reference-view-definition-artifact.md)