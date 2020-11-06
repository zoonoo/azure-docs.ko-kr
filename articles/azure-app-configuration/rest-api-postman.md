---
title: Azure Active Directory REST API-Postman을 사용 하 여 테스트
description: Postman을 사용 하 여 Azure 앱 구성을 테스트 REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 9690678fc7b794c694e588a7993cb131d8264a72
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424277"
---
# <a name="test-the-azure-app-configuration-rest-api-using-postman"></a>Postman을 사용 하 여 Azure 앱 구성 REST API 테스트

[Postman](https://www.getpostman.com/)을 사용 하 여 REST API를 테스트 하려면 요청에서 [인증](./rest-api-authentication-hmac.md) 에 필요한 HTTP 헤더를 포함 해야 합니다. REST API 테스트를 위해 Postman을 구성 하 고 인증 헤더를 자동으로 생성 하는 방법은 다음과 같습니다.

1. 새 [요청](https://learning.getpostman.com/docs/postman/sending_api_requests/requests/) 만들기
1. `signRequest` [JavaScript 인증 샘플](./rest-api-authentication-hmac.md#javascript) 에서 요청에 대 한 [사전 요청 스크립트](https://learning.getpostman.com/docs/postman/scripts/pre_request_scripts/) 에 함수를 추가 합니다.
1. 사전 요청 스크립트의 끝에 다음 코드를 추가 합니다. TODO 주석으로 표시 된 대로 액세스 키를 업데이트 합니다.

    ```js
    // TODO: Replace the following placeholders with your access key
    var credential = "<Credential>"; // Id
    var secret = "<Secret>"; // Value

    var isBodyEmpty = pm.request.body === null || pm.request.body === undefined || pm.request.body.isEmpty();

    var headers = signRequest(
        pm.request.url.getHost(),
        pm.request.method,
        pm.request.url.getPathWithQuery(),
        isBodyEmpty ? undefined : pm.request.body.toString(),
        credential,
        secret);

    // Add headers to the request
    headers.forEach(header => {
        pm.request.headers.upsert({key: header.name, value: header.value});
    })
    ```

1. 요청 보내기
