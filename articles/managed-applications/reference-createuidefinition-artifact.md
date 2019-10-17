---
title: Azure 관리 되는 응용 프로그램 createUiDefinition 아티팩트 참조
description: Azure 관리 되는 응용 프로그램에 대 한 createUiDefinition 아티팩트를 만드는 방법을 보여 줍니다. 이 파일의 이름은 createUiDefinition. json입니다.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 09364a849926fc829a890bfcdc8b760c7c7e189c
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330189"
---
# <a name="reference-user-interface-elements-artifact"></a>참조: 사용자 인터페이스 요소 아티팩트

이 문서는 Azure Managed Applications의 *Createuidefinition. json* 아티팩트에 대 한 참조입니다. 사용자 인터페이스 요소를 작성 하는 방법에 대 한 자세한 내용은 [사용자 인터페이스 요소 만들기](create-uidefinition-elements.md)를 참조 하세요.

## <a name="user-interface-elements"></a>사용자 인터페이스 요소

다음 JSON에서는 Azure Managed Applications에 대 한 *Createuidefinition. json* 파일의 예를 보여 줍니다.

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