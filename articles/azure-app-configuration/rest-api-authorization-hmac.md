---
title: Azure App Configuration REST API - HMAC 권한 부여
description: HMAC을 사용하여 REST API를 사용하는 Azure App Configuration에 대해 권한을 부여합니다.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 3746fcf06aea48c9c80696b634d6323b9cb21822
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932662"
---
# <a name="hmac-authorization---rest-api-reference"></a>HMAC 권한 부여 - REST API 참조

HMAC 인증을 사용하는 경우 작업은 읽기와 쓰기 두 범주 중 하나에 속합니다. 읽기/쓰기 액세스 키는 모든 작업을 호출할 권한을 부여합니다. 읽기 전용 액세스 키는 읽기 작업을 호출할 권한만 부여합니다. 액세스 키가 읽기 전용인지 또는 읽기/쓰기인지는 `readOnly` 속성에 따라 결정됩니다. 읽기 전용 액세스 키로 쓰기 요청을 하면 요청이 인증되지 않습니다.

## <a name="obtaining-access-keys"></a>액세스 키 가져오기

액세스 키 및 이를 얻는 데 사용되는 API를 설명하는 사양은 Azure App Configuration 리소스 공급자 사양에 자세히 설명되어 있으며 [여기에서](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2019-10-01/appconfiguration.json) 확인할 수 있습니다. 액세스 키는 "ConfigurationStores_ListKeys" 작업을 통해 얻을 수 있습니다.

## <a name="errors"></a>오류

```http
HTTP/1.1 403 Forbidden
```

**이유:** 요청을 인증하는 데 사용되는 액세스 키는 요청된 작업을 수행하는 데 필요한 권한을 제공하지 않습니다.
**해결 방법:** 요청된 작업을 수행하고 요청을 인증하는 데 사용할 수 있는 권한을 제공하는 액세스 키를 가져옵니다.
