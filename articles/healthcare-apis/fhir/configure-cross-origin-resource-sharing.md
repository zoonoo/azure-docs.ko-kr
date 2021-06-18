---
title: Azure API for FHIR 원본 간 리소스 공유 구성
description: 이 문서에서는 Azure API for FHIR 원본 간 리소스 공유를 구성하는 방법을 설명합니다.
author: matjazl
ms.author: zxue
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: 3ce1f914afab2a46ab734c270b00e2c7938dc80b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112283918"
---
# <a name="configure-cross-origin-resource-sharing-in-azure-api-for-fhir"></a>Azure API for FHIR 원본 간 리소스 공유 구성

Azure API for FHIR(전자 의료 기록 교환)는 [CORS(크로스-원본 자원 공유)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing)를 지원합니다. CORS를 사용하면 한 도메인(원본)의 애플리케이션이 도메인 간 요청이라고 하는 다른 도메인의 리소스에 액세스할 수 있도록 설정을 구성할 수 있습니다.

CORS는 종종 RESTful API를 다른 도메인에 호출해야 하는 단일 페이지 앱에서 사용됩니다.

Azure API for FHIR CORS 설정을 구성하려면 다음 설정을 지정합니다.

- **원본(Access-Control-Allow-Origin)**. Azure API for FHIR 원본 간 요청을 할 수 있는 도메인 목록입니다. 각 도메인(원본)은 별도의 줄에 입력해야 합니다. 모든 도메인에서 호출할 수 있도록 별표(*)를 입력할 수 있지만 보안 위험이 있으므로 권장하지 않습니다.

- **헤더(Access-Control-Allow-Headers) .** 원본 요청에 포함될 헤더 목록입니다. 모든 헤더를 허용하려면 별표(*)를 입력합니다.

- **메서드(Access-Control-Allow-Methods)**. API 호출에서 허용되는 메서드(PUT, GET, POST 등)입니다. 모든 메서드에 **대해 Select all** 선택합니다.

- **최대 연령(Access-Control-Max-Age)**. Access-Control-Allow-Headers 및 Access-Control-Allow-Methods에 대한 준비 요청 결과를 캐시하는 데 사용할 값(초)입니다.

- **자격 증명 허용(Access-Control-Allow-Credentials)**. CORS 요청에는 일반적으로 [CSRF(교차 사이트 요청 위조)](https://en.wikipedia.org/wiki/Cross-site_request_forgery) 공격을 방지하기 위한 쿠키가 포함되지 않습니다. 이 설정을 선택하면 쿠키와 같은 자격 증명을 포함하도록 요청할 수 있습니다. 별표(*)로 원본을 이미 설정한 경우에는 이 설정을 구성할 수 없습니다.

![CORS(원본 간 리소스 공유) 설정](media/cors/cors.png)

>[!NOTE]
>다른 도메인 원본에 대해 다른 설정을 지정할 수 없습니다. 모든 **설정(헤더,** **메서드,** **최대 사용 연령** 및 자격 증명 **허용)은** 원본 설정에 지정된 모든 원본에 적용됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure API for FHIR 원본 간 공유를 구성하는 방법을 배웠습니다. 다음으로 완전 관리형 Azure API for FHIR 배포합니다.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR 배포](fhir-paas-portal-quickstart.md)