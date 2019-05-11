---
title: Azure 사용자 지정 공급자 미리 보기의 개요
description: Azure Resource Manager를 사용 하 여 사용자 지정 리소스 공급자를 만들기 위한 개념을 설명 합니다.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: bbfb10f612690af0f4fd3683e0f58986a21048d8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159857"
---
# <a name="azure-custom-providers-preview-overview"></a>Azure 사용자 지정 공급자 미리 보기 개요

Azure 사용자 지정 공급자를 사용 하 여 Azure 서비스와 작동 하도록 확장할 수 있습니다. 사용자 지정된 리소스 형식 및 작업을 비롯 하 여 사용자 고유의 리소스 공급자 만들기 사용자 지정 공급자는 Azure Resource Manager와 통합 되어 있습니다. 배포 및 서비스 보호 템플릿 배포 및 역할 기반 access control과 같은 리소스 관리자의 기능을 사용할 수 있습니다.

이 문서에는 사용자 지정 공급자 및 해당 기능에 대해 간략하게를 설명합니다. 다음 이미지에는 리소스 및 사용자 지정 공급자에 정의 된 작업에 대 한 워크플로를 보여 줍니다.

![사용자 지정 공급자 개요](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> 사용자 지정 공급자는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="define-your-custom-provider"></a>사용자 지정 공급자를 정의 합니다.

먼저 Azure Resource Manager 사용자 지정 공급자에 대 한 알 수 있도록 합니다. 리소스 종류를 사용 하는 사용자 지정 공급자 리소스를 Azure에 배포 **Microsoft.CustomProviders/resourceProviders**합니다. 해당 리소스 정의 리소스와 작업 서비스에 대 한 합니다.

예를 들어 서비스 필요한 명명 된 리소스 종류 **사용자**, 사용자 지정 공급자 정의에 해당 리소스 종류를 포함 합니다. 각 리소스 종류에 대 한 해당 리소스 종류에 대 한 REST 작업 (PUT, GET, DELETE)를 제공 하는 끝점을 제공 합니다. 모든 환경에서 끝점을 호스팅할 수 있습니다 하 고 서비스 리소스 종류에 대 한 작업을 처리 하는 방법에 대 한 논리를 포함 합니다.

또한 리소스 공급자에 대 한 사용자 지정 작업을 정의할 수 있습니다. 작업 후 작업을 나타냅니다. 시작, 중지 또는 다시 시작 등의 작업에 대 한 작업을 사용 합니다. 요청을 처리 하는 끝점을 제공 합니다.

다음 예제에서는 작업 및 리소스 종류를 사용 하 여 사용자 지정 공급자를 정의 하는 방법을 보여 줍니다.

```json
{
  "apiVersion": "2018-09-01-preview",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[parameters('funcName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ]
  }
},
```

에 대 한 **routingType**, 사용 가능한 값은 `Proxy` 고 `Cache`입니다. 프록시 리소스 형식에 대 한 요청을 의미 하거나 작업 끝점에서 처리 됩니다. 캐시 설정은 리소스 유형의 경우 작업 안 함만 지원 됩니다. 캐시를 지정 하려면 프록시도 지정 해야 합니다. 캐시 응답 끝점에서 읽기 작업을 최적화 하기 위해 저장 됨을 의미 합니다. 캐시 설정을 사용 하 여 쉽게 다른 Resource Manager 서비스를 사용 하 여 일관 되 고 규정을 준수 하는 API를 구현 합니다.

## <a name="deploy-your-resource-types"></a>리소스 형식 배포

사용자 지정 공급자를 정의한 후에 사용자 지정 된 리소스 종류를 배포할 수 있습니다. 다음 예제에서는 사용자 지정 공급자에 대 한 리소스 종류를 배포 하려면 템플릿에 포함 된 JSON을 보여 줍니다. 이 리소스 종류를 다른 Azure 리소스와 동일한 템플릿에 배포할 수 있습니다.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.CustomProviders/resourceProviders/users",
    "name": "[concat(parameters('rpname'), '/santa')]",
    "location": "[parameters('location')]",
    "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole"
    }
}
```

## <a name="manage-access"></a>액세스 관리

Azure를 사용 하 여 [역할 기반 액세스 제어](../role-based-access-control/overview.md) 리소스 공급자에 대 한 액세스를 관리할 수 있습니다. 할당할 수 있습니다 [기본 제공 역할](../role-based-access-control/built-in-roles.md) 소유자, 참가자 또는 독자에 게 등입니다. 또는 정의할 수 있습니다 [사용자 지정 역할](../role-based-access-control/custom-roles.md) 리소스 공급자의 작업에 관련 되어 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자 지정 공급자에 대해 알아보았습니다. 사용자 지정 공급자를 만들려면 다음 문서를 이동 합니다.

> [!div class="nextstepaction"]
> [자습서: 사용자 지정 공급자를 만들고 사용자 지정 리소스를 배포 합니다.](create-custom-provider.md)
