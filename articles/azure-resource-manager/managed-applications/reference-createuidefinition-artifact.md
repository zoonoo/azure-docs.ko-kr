---
title: CreateUiDefinition 아티팩트
description: Azure Managed Applications에 대해 createUiDefinition 아티팩트를 만드는 방법을 보여 줍니다. 파일 이름은 createUiDefinition.json입니다.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 47b515d2a65908ab0149c2ee28261a5f0edadf1d
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110083713"
---
# <a name="reference-user-interface-elements-artifact"></a>참조: 사용자 인터페이스 요소 아티팩트

이 문서는 Azure Managed Applications의 *createUiDefinition.js* 아티팩트에 대한 참조입니다. 사용자 인터페이스 요소를 작성하는 방법에 대한 자세한 내용은 [사용자 인터페이스 요소 만들기](create-uidefinition-elements.md)를 참조하세요.

## <a name="user-interface-elements"></a>사용자 인터페이스 요소

다음 JSON은 Azure Managed Applications에 대한 *createUiDefinition.json* 파일의 예를 보여 줍니다.

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
            "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.customproviders/custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
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

- [자습서: 사용자 지정 작업 및 리소스가 있는 관리형 애플리케이션 만들기](tutorial-create-managed-app-with-custom-provider.md)
- [참조: 배포 템플릿 아티팩트](reference-main-template-artifact.md)
- [참조: 보기 정의 아티팩트](reference-view-definition-artifact.md)