---
title: Azure CLI를 사용 하 여 액세스 토큰 가져오기-FHIR 용 Azure API
description: 이 문서에서는 Azure CLI를 사용 하 여 Azure API 용 Azure API에 대 한 액세스 토큰을 가져오는 방법을 설명 합니다.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mihansen
ms.openlocfilehash: 4eb4c10a6c98aa847c9fa6c239aacde891db5aae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871143"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Azure CLI를 사용 하 여 Azure API 용 Azure API에 대 한 액세스 토큰 가져오기

이 문서에서는 Azure CLI를 사용 하 여 Azure API 용 Azure API에 대 한 액세스 토큰을 가져오는 방법에 대해 알아봅니다. [FHIR 용 AZURE API를 프로 비전](fhir-paas-portal-quickstart.md)할 때 서비스에 대 한 액세스 권한이 있는 사용자 또는 서비스 사용자 집합을 구성 합니다. 사용자 개체 ID가 허용 되는 개체 id 목록에 있는 경우 Azure CLI를 사용 하 여 가져온 토큰을 사용 하 여 서비스에 액세스할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-with-azure-cli"></a>Azure CLI로 로그인

토큰을 얻기 전에 토큰을 가져오려는 사용자로 로그인 해야 합니다.:

```azurecli-interactive
az login
```

## <a name="obtain-a-token"></a>토큰 가져오기

FHIR 용 Azure API는 `resource` `Audience` fhir 서버의 uri와 동일한 uri를 사용 하 여 또는를 사용 합니다 `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` . 다음 명령을 사용 하 여 토큰을 가져와 변수에 저장할 수 있습니다 `$token` .

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com | jq -r .accessToken)
```

## <a name="use-with-azure-api-for-fhir"></a>FHIR 용 Azure API와 함께 사용

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure CLI를 사용 하 여 FHIR 용 Azure API에 대 한 액세스 토큰을 가져오는 방법을 배웠습니다. Postman을 사용하여 FHIR API에 액세스하는 방법을 알아보려면 Postman 자습서로 이동합니다.

>[!div class="nextstepaction"]
>[Postman을 사용하여 FHIR API에 액세스](access-fhir-postman-tutorial.md)