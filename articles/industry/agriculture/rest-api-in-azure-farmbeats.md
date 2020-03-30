---
title: 참조
description: ''
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 318b3e3f774a4381434fd56154f4c0d95c28c7a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479518"
---
# <a name="references"></a>참조

이 문서에서는 Azure FarmBeats API에 대해 설명합니다.

## <a name="rest-api"></a>REST API

Azure FarmBeats API는 농업 기업에 JSON 기반 응답과 함께 표준화된 RESTful 인터페이스를 제공하여 다음과 같은 Azure FarmBeats 기능을 활용할 수 있도록 지원합니다.

- API를 사용하여 센서, 카메라, 드론, 날씨, 위성 및 선별된 지상 데이터를 얻을 수 있습니다.
- 공통 데이터 공급자 간에 데이터의 정규화 및 컨텍스트화.
- 수집된 모든 데이터에 대한 스키마화된 액세스 및 쿼리 기능.
- 농업 기능을 기반으로 쿼리할 수 있는 메타데이터의 자동 생성.
- 신속한 모델 작성을 위해 시간 시퀀스 집계가 자동으로 생성됩니다.
- 통합된 Azure Data Factory 엔진을 사용하여 사용자 지정 데이터 처리 파이프라인을 쉽게 빌드할 수 있습니다.

## <a name="application-development"></a>애플리케이션 개발

FarmBeats API에는 Swagger 기술 문서가 포함되어 있습니다. 모든 API 및 해당 요청 또는 응답에 대한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)을 참조하십시오.

다음 표에서는 FarmBeats Datahub의 모든 개체와 리소스를 요약합니다.

| 개체 및 리소스 | 설명
--- | ---|
팜 | 팜은 FarmBeats 시스템 내의 실제 관심 위치에 해당합니다. 각 팜에는 팜 이름과 고유한 팜 ID가 있습니다. |
디바이스  | 장치는 팜에 있는 물리적 장치에 해당합니다. 각 장치에는 고유한 장치 ID가 있습니다. 장치는 일반적으로 팜 ID가 있는 팜에 프로비전됩니다.
DeviceModel  | DeviceModel은 게이트웨이 또는 노드인 장치 유형 및 제조업체와 같은 장치의 메타데이터에 해당합니다.
센서  | 센서는 값을 기록하는 물리적 센서에 해당합니다. 센서는 일반적으로 장치 ID가 있는 장치에 연결됩니다.
센서 모델  | SensorModel은 제조업체, 아날로그 또는 디지털 센서 유형, 주변 온도 및 압력과 같은 센서 측정과 같은 센서의 메타데이터에 해당합니다.
원격 분석  | 원격 분석에서는 특정 센서 및 시간 범위에 대한 원격 분석 메시지를 읽을 수 있는 기능을 제공합니다.
작업  | Job은 원하는 출력을 얻기 위해 FarmBeats 시스템에서 실행되는 모든 작업 워크플로에 해당합니다. 각 작업은 작업 ID 및 작업 유형과 연결됩니다.
JobType  | JobType은 시스템에서 지원하는 다른 작업 유형에 해당합니다. 시스템 정의 및 사용자 정의 작업 유형이 포함됩니다.
ExtendedType  | ExtendedType은 시스템의 시스템 및 사용자 정의 형식 목록에 해당합니다. ExtendedType은 FarmBeats 시스템에서 새 센서, 장면 또는 장면 파일 형식을 설정하는 데 도움이 됩니다.
파트너  | 파트너는 FarmBeats의 센서 및 이미지 통합 파트너에 해당합니다.
장면  | 장면은 팜컨텍스트에서 생성된 출력에 해당합니다. 각 장면에는 장면 ID, 장면 소스, 장면 유형 및 연관된 팜 ID가 있습니다. 각 장면 ID에는 연결된 여러 장면 파일이 있을 수 있습니다.
장면 파일 |SceneFile은 단일 장면에 대해 생성된 모든 파일에 해당합니다. 단일 장면 ID에는 여러 장면 파일 ID가 연결되어 있을 수 있습니다.
규칙  |규칙은 경고를 트리거하는 팜 관련 데이터에 대한 조건에 해당합니다. 각 규칙은 팜 데이터의 컨텍스트에 있습니다.
경고  | 경고는 규칙 조건이 충족될 때 생성되는 알림에 해당합니다. 각 경고는 규칙의 컨텍스트에 있습니다.
RoleDefinition  | RoleDefinition는 역할에 대해 허용되고 허용되지 않는 작업을 정의합니다.
RoleAssignment  |역할 할당은 사용자 또는 서비스 주체에게 역할 할당에 해당합니다.

### <a name="data-format"></a>데이터 형식

JSON은 임의의 데이터 구조의 간단한 텍스트 표현을 제공하는 공통 언어 독립적 인 데이터 형식입니다. 자세한 내용은 [JSON 웹 사이트를](https://www.json.org/)참조하십시오.

## <a name="authentication-and-authorization"></a>인증 및 권한 부여

REST API에 대한 HTTP 요청은 Azure Active Directory(Azure AD)로 보호됩니다.
REST API에 인증된 요청을 하려면 API를 호출하기 전에 클라이언트 코드에 유효한 자격 증명을 사용하여 인증해야 합니다. 인증은 Azure AD에 의해 다양한 행위자 간에 조정됩니다. 클라이언트에 인증 증명으로 액세스 토큰을 제공합니다. 그런 다음 이 토큰은 REST API 요청의 HTTP 권한 부여 헤더로 전송됩니다. Azure AD 인증에 대한 자세한 내용은 개발자용 [Azure Active Directory를](https://portal.azure.com) 참조하십시오.

액세스 토큰은 헤더 섹션에서 다음과 같이 후속 API 요청에서 보내야 합니다.

```
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP 요청 헤더

다음은 Azure FarmBeats Datahub에 대한 API 호출을 수행할 때 지정해야 하는 가장 일반적인 요청 헤더입니다.


**헤더** | **설명 및 예**
--- | ---
콘텐츠 형식  | 요청 형식(콘텐츠 유형: 응용<format>프로그램/ ). Azure FarmBeats Datahub API의 경우 형식은 JSON입니다. Content-Type: application/json
권한 부여  | API 호출을 만드는 데 필요한 액세스 토큰을 지정합니다. 권한 부여: 액세스 토큰 <보유자>
수락 | 응답 형식입니다. Azure FarmBeats Datahub API의 경우 형식은 JSON입니다. 수락: 신청서/json

### <a name="api-requests"></a>API 요청

REST API 요청을 하려면 HTTP(GET, POST, PUT 또는 DELETE) 메서드, API 서비스에 대한 URL, 쿼리할 리소스에 URI를 결합하여 쿼리하고 데이터를 업데이트 하거나 삭제한 다음 하나 이상의 HTTP 요청 헤더를 추가합니다.

API 서비스에 대한 URL은 Datahub URL(예:\<datahub-웹 사이트 이름>.azurewebsite.net)https://.

선택적으로 GET 호출에 쿼리 매개 변수를 포함하여 필터링하고 크기를 제한하며 응답에서 데이터를 정렬할 수 있습니다.

다음 샘플 요청은 장치 목록을 얻는 데 사용됩니다.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

대부분의 GET, POST 및 PUT 호출에는 JSON 요청 본문이 필요합니다.

다음 샘플 요청은 장치를 만듭니다. 이 요청에는 요청 본문에 JSON입력이 있습니다.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>쿼리 매개 변수

REST GET 호출의 경우 요청 URI에 하나 이상의 쿼리 매개 변수를 포함하여 API 응답에서 데이터를 필터링, 제한 및 정렬할 수 있습니다. 쿼리 매개 변수는 API 설명서 및 개별 GET 호출을 참조하십시오.
예를 들어 장치 목록(GET 호출 /Device)을 쿼리할 때 다음 쿼리 매개 변수를 지정할 수 있습니다.

![장치 목록](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>오류 처리

Azure FarmBeats Datahub API는 표준 HTTP 오류를 반환합니다. 가장 일반적인 오류 코드는 다음과 같습니다.

 |오류 코드             | 설명 |
 |---                    | --- |
 |200                    | Success |
 |201                    | 성공 만들기(게시물) |
 |400                    | 잘못된 요청. 요청에 오류가 있습니다. |
 |401                    | 권한이 없습니다. API 호출자는 리소스에 액세스할 권한이 없습니다. |
 |404                    | 리소스를 찾을 수 없습니다. |
 |5배스                    | 내부 서버 오류입니다. 5XX로 시작하는 오류 코드는 서버에 약간의 오류가 있음을 의미합니다. 자세한 내용은 서버 로그 및 다음 섹션을 참조하십시오. |


표준 HTTP 오류 외에도 Azure FarmBeats Datahub API는 다음과 같은 형식으로 내부 오류를 반환합니다.

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

이 예제에서는 팜을 만들 때 필수 필드 "Name" 입력 페이로드에 지정 되지 않았습니다. 결과 오류 메시지는 다음과 같은 것입니다.

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Azure Active Directory에 사용자 또는 앱 등록 추가

Azure FarmBeats API는 Azure Active Directory에서 사용자 또는 앱 등록에서 액세스할 수 있습니다. Azure Active Directory에서 앱 등록을 만들려면 다음 단계를 따르십시오.

1. [Azure 포털로](https://portal.azure.com)이동하여 **Azure Active Directory** > 앱 등록**새 등록을****선택합니다.** >  또는 기존 계정을 사용할 수 있습니다.
2. 새 계정의 경우 다음을 수행합니다.

    - 이름을 입력합니다.
    - **이 조직 디렉터리에서 계정만 선택합니다(단일 테넌트).**
    - 나머지 필드의 기본값을 사용합니다.
    - **등록을**선택합니다.

3. 새 앱 및 기존 앱 등록 **개요** 창에서 다음을 수행합니다.

    - 클라이언트 **ID** 및 **테넌트 ID를**캡처합니다.
    - 인증서 **및 암호로** 이동하여 새 클라이언트 비밀을 생성하고 **클라이언트-비밀**을 캡처합니다.
    - **개요로**돌아가서 **로컬 디렉터리에서 응용 프로그램 관리**옆에 있는 링크를 선택합니다.
    - **속성으로** 이동하여 **개체 ID를**캡처합니다.

4. [Datahub Swagger로](https://<yourdatahub>.azurewebsites.net/swagger/index.html) 이동하여 다음을 수행합니다.
    - **역할 할당 API로 이동합니다.**
    - POST를 수행하여 방금 만든 **개체 ID에** 대한 **RoleAssignment** 개체를 만듭니다.

  > [!NOTE]
  > 사용자 및 Active Directory 등록을 추가하는 방법에 대한 자세한 내용은 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)를 참조하십시오.

이전 단계를 완료한 후 앱 등록(클라이언트)은 베어러 인증을 통해 액세스 토큰을 사용하여 Azure FarmBeats API를 호출할 수 있습니다.

액세스 토큰을 사용하여 헤더 섹션의 후속 API 요청에서 다음과 같이 보내십시오.

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
