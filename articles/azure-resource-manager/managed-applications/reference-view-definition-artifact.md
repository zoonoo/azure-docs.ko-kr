---
title: 정의 아티팩트 참조 보기
description: Azure 관리 되는 응용 프로그램에 대 한 보기 정의 아티팩트의 예제를 제공 합니다. 파일 이름은 viewDefinition.json입니다.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 5173db54abef132a4a4d5d117881352ca37d6b23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651203"
---
# <a name="reference-view-definition-artifact"></a>참조: 정의 아티팩트 보기

이 문서는 Azure 관리 응용 프로그램의 *viewDefinition.json* 아티팩트에 대한 참조입니다. 뷰 구성 작성에 대한 자세한 내용은 [정의 아티팩트 보기를](concepts-view-definition.md)참조하십시오.

## <a name="view-definition"></a>정의 보기

다음 JSON은 Azure 관리 응용 프로그램에 대한 *viewDefinition.json* 파일의 예를 보여 주며 다음과 같은 예입니다.

```json
{
  "views": [{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  },
  {
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
        "parameters": {
          "steps": [{
            "name": "add",
            "label": "Add user",
            "elements": [{
              "name": "name",
              "label": "User's Full Name",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a full user name.",
              "constraints": { "required": true }
            },
            {
              "name": "location",
              "label": "User's Location",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a Location.",
              "constraints": { "required": true }
            }]
          }],
          "outputs": {
            "name": "[steps('add').name]",
            "properties": {
              "FullName": "[steps('add').name]",
              "Location": "[steps('add').location]"
            }
          }
        }
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }]
}
```

## <a name="next-steps"></a>다음 단계

- [자습서: 사용자 지정 작업 및 리소스를 사용 하 고 관리 되는 응용 프로그램 만들기](tutorial-create-managed-app-with-custom-provider.md)
- [참조: 사용자 인터페이스 요소 아티팩트](reference-createuidefinition-artifact.md)
- [참조: 배포 템플릿 아티팩트](reference-main-template-artifact.md)