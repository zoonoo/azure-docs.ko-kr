---
title: Azure 앱 Configuration REST API-HMAC 권한 부여
description: REST API를 사용 하 여 Azure 앱 구성에 대 한 권한 부여에 HMAC 사용
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 8d1f8a17f51711cc5c10567797e1224f96eb7630
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424159"
---
# <a name="hmac-authorization---rest-api-reference"></a>HMAC 권한 부여-REST API 참조

HMAC 인증을 사용 하는 경우 작업은 읽기 또는 쓰기의 두 범주 중 하나에 속합니다. 읽기/쓰기 액세스 키는 모든 작업을 호출할 수 있는 권한을 부여 합니다. 읽기 전용 액세스 키는 읽기 작업만 호출할 수 있는 권한을 부여 합니다. 액세스 키가 읽기 전용인 지 또는 읽기/쓰기가 가능한 지는 속성에 의해 결정 됩니다 `readOnly` . 읽기 전용 액세스 키를 사용 하 여 쓰기 요청을 수행 하려고 하면 요청이 권한 부여 되지 않습니다.

## <a name="obtaining-access-keys"></a>액세스 키 가져오기

액세스 키 및이를 얻는 데 사용 되는 API를 설명 하는 사양은 [여기](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2019-10-01/appconfiguration.json)Azure 앱 구성 리소스 공급자 사양에 자세히 설명 되어 있습니다. "ConfigurationStores_ListKeys" 작업을 통해 액세스 키를 가져옵니다.

## <a name="errors"></a>오류

```http
HTTP/1.1 403 Forbidden
```

**이유:** 요청을 인증 하는 데 사용 되는 액세스 키에서 요청 된 작업을 수행 하는 데 필요한 권한을 제공 하지 않습니다.
**해결 방법:** 요청 된 작업을 수행 하 고 요청을 인증 하는 데 사용할 수 있는 권한을 제공 하는 액세스 키를 가져옵니다.
