---
title: API 고려 사항-Azure Marketplace
description: Marketplace API를 사용하는 경우의 버전 관리, 오류 처리 및 권한 부여 문제
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: bde55c48e8a3730727af7f3930b2a507c03e3ff3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102721"
---
# <a name="api-considerations"></a>API 고려 사항

<a name="api-versioning"></a>API 버전 관리
--------------

> [!NOTE]
> Cloud 파트너 포털 API는 파트너 센터와 통합되며 제품을 파트너 센터로 마이그레이션한 후에도 계속 작동합니다. 통합에는 작은 변경 사항이 도입되었습니다. 파트너 센터로 마이그레이션한 후 코드가 계속 작동 하는지 확인 하려면 [CLOUD 파트너 포털 API 참조](./cloud-partner-portal-api-overview.md) 에 나열 된 변경 내용을 검토 합니다.

동시에 사용할 수 있는 여러 API 버전이 있을 수 있습니다. 클라이언트는 쿼리 문자열의 일부로 `api-version` 매개 변수를 제공하여 호출하려는 버전을 나타내야 합니다.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

알 수 없거나 잘못된 API 버전을 사용할 때의 요청에 대한 응답은 HTTP 코드 400입니다. 이 오류는 응답 본문의 알려진 API 버전 컬렉션을 반환합니다.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>오류
------

API는 해당 HTTP 상태 코드와 경우에 따라 JSON으로 serialize된 응답의 추가 정보를 사용하여 오류에 응답합니다.
오류가 수신되면(특히 400 클래스 오류) 기본 원인을 해결한 후에 요청을 다시 시도하세요. 예를 들어, 위의 샘플 응답에서 요청을 다시 보내기 전에 API 버전 매개 변수를 수정하세요.

<a name="authorization-header"></a>권한 부여 헤더
--------------------

이 참조의 모든 API에 대해 Azure AD(Azure Active Directory)에서 가져온 전달자 토큰과 함께 인증 헤더를 전달해야 합니다. 이 헤더는 유효한 응답을 수신하는 데 필요합니다. 이 헤더가 없으면 `401 Unauthorized` 오류가 반환됩니다. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
