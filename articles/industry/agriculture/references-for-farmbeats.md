---
title: FarmBeats에 대 한 참조
description: ''
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: efd294910531509d736dbda274406bd7c801c124
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931209"
---
# <a name="references"></a>참조

다음은 Azure FarmBeats Api에 대해 간략하게 설명 하는 메모 모음입니다.

## <a name="rest-api"></a>REST API

Azure FarmBeats Api는 agricultural 비즈니스에 JSON 기반 응답을 제공 하는 표준화 된 RESTful 인터페이스를 제공 하며,이를 통해 Azure FarmBeats 기능을 활용 하는 데 도움이 됩니다.

- API를 통해 센서, 카메라, 드 론, 날씨, 위성 및 큐 레이트 그라운드 데이터를 가져올 수 있습니다.
- Common data provider에서 데이터의 정규화/contextualization.
- 모든 수집 데이터에 대 한 액세스 및 쿼리 기능을 스키마 화 된 합니다.
- Agronomic 기능을 기준으로 쿼리할 수 있는 메타 데이터의 자동 생성  
- 빠른 모델 빌드를 위해 자동으로 생성 된 시간 시퀀스 집계.
- 사용자 지정 데이터 처리 파이프라인을 쉽게 빌드하기 위한 ADF (통합 Azure Data Factory) 엔진입니다.

## <a name="application-development"></a>애플리케이션 개발

Api에는 Swagger 기술 설명서가 포함 되어 있습니다. 모든 Api 및 해당 요청/응답에 대 한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) 를 참조 하세요.

다음은 FarmBeats Data hub의 모든 개체/리소스에 대 한 요약입니다.

팜의 | 팜은 FarmBeats 시스템 내에서 관심 있는 실제 위치에 해당 합니다. 각 팜에는 팜 이름과 고유한 팜 ID가 있습니다.
--- | ---|
디바이스  | 장치는 팜에 있는 물리적 장치에 해당 합니다. 각 장치에는 고유한 장치 ID가 있습니다. 장치는 일반적으로 팜 ID를 사용 하 여 팜으로 프로 비전 됩니다.
DeviceModel  | DeviceModel는 장치의 메타 데이터 (예: 제조업체, 장치 유형, 게이트웨이 또는 노드)에 해당 합니다.
센서  | 센서는 값을 기록 하는 실제 센서에 해당 합니다. 센서는 일반적으로 장치 ID를 사용 하 여 장치에 연결 됩니다.
SensorModel  | SensorModel은 제조업체, 센서 유형 (예: 아날로그 또는 디지털, 주변 온도, 압력 등의 센서 측정)과 같은 센서의 메타 데이터에 해당 합니다.
원격 분석  | 원격 분석은 특정 센서 및 시간 범위에 대 한 원격 분석 메시지를 읽는 기능을 제공 합니다.
작업  | 작업은 원하는 출력을 얻기 위해 FarmBeats 시스템에서 실행 되는 작업의 워크플로에 해당 합니다. 각 작업은 작업 ID 및 작업 유형과 연결 됩니다.
JobType  | JobType는 시스템에서 지 원하는 여러 작업 유형에 해당 합니다. 여기에는 시스템 정의 & 사용자 정의 작업 유형이 포함 됩니다.
ExtendedType  | ExtendedType 시스템의 시스템 & 사용자 정의 형식 목록에 해당 합니다. 이렇게 하면 FarmBeats 시스템에서 새 센서 또는 장면 또는 Scenefile 유형을 설정할 수 있습니다.
파트너  | 파트너는 FarmBeats 용 센서/이미지 통합 파트너에 해당 합니다.
장면의  | 장면은 팜 컨텍스트에서 생성 된 모든 출력에 해당 합니다. 각 장면에는 연결 된 장면 ID, 장면 원본, 장면 유형 및 팜 ID가 있습니다. 각 장면 ID에는 여러 장면 파일이 연결 될 수 있습니다.
SceneFile |SceneFile는 단일 장면에 대해 생성 되는 모든 파일에 해당 합니다. 단일 장면 ID에는 여러 개의 SceneFile Id가 연결 될 수 있습니다.
규칙  |규칙은 경고를 트리거하기 위한 팜 관련 데이터의 조건에 해당 합니다. 각 규칙은 팜 데이터의 컨텍스트에 있습니다.
경고  | 경고는 규칙 조건이 충족 될 때 생성 되는 알림에 해당 합니다. 각 경고는 규칙의 컨텍스트에 있습니다.
RoleDefinition  | RoleDefinition은 역할에 대해 허용 되거나 허용 되지 않는 작업을 정의 합니다.
RoleAssignment  |RoleAssignment은 사용자 또는 서비스 주체에 역할을 할당 하는 것에 해당 합니다.

**날짜 형식**

JSON (JavaScript Object Notation)은 임의의 데이터 구조에 대 한 간단한 텍스트 표현을 제공 하는 일반적인 언어 독립적 데이터 형식입니다. 자세한 내용은 json.org를 참조 하세요.

## <a name="authentication-and-authorization"></a>인증 및 권한 부여

REST API에 대 한 HTTP 요청은 Azure Active Directory (Azure AD)로 보호 됩니다.
REST Api에 대해 인증 된 요청을 수행 하려면 API를 호출 하기 전에 클라이언트 코드에 유효한 자격 증명으로 인증 해야 합니다. 인증은 Azure AD의 다양 한 행위자 간에 조정 되며 인증 증명으로 클라이언트에 액세스 토큰을 제공 합니다. 토큰은 REST API 요청의 HTTP 인증 헤더에 전송 됩니다. Azure AD 인증에 대해 자세히 알아보려면 개발자를 위한 [Azure Active Directory](https://portal.azure.com) 를 참조 하세요.

다음과 같이 헤더 섹션의 후속 API 요청에서 액세스 토큰을 전송 해야 합니다.

```
headers = {"Authorization": "Bearer " + **access_token**}
```

**HTTP 요청 헤더**

Azure FarmBeats Data hub에 대 한 API 호출을 만들 때 지정 해야 하는 가장 일반적인 요청 헤더는 다음과 같습니다.


**머리글** | **설명 및 예제**
--- | ---
콘텐츠 형식  | Azure FarmBeats 데이터 허브 Api 형식의 요청 형식 (Content-type: application/<format>)은 json입니다. Content-type: application/json
권한 부여  | API 호출을 수행 하는 데 필요한 액세스 토큰을 지정 합니다. **권한 부여: 전달자 < 액세스-토큰 >**
수락 | 응답 형식입니다. Azure FarmBeats Data hubs Api의 경우 json Accept 형식은 **application/json입니다.**

**API 요청**

REST API 요청을 만들려면 HTTP (GET, POST, PUT 또는 DELETE) 메서드, API 서비스에 대 한 URL, 쿼리할 리소스에 대 한 URI, 데이터 전송, 업데이트 또는 삭제 및 하나 이상의 HTTP 요청 헤더를 결합 합니다.

API 서비스에 대 한 URL은\<https://> 데이터 허브 URL입니다. 필요에 따라 필터에 대 한 GET 호출에 쿼리 매개 변수를 포함 하 고 크기를 제한 하 고 응답의 데이터를 정렬할 수 있습니다.

아래 샘플 요청은 장치 목록을 가져오는 것입니다.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

대부분 GET, POST 및 PUT 호출에는 JSON 요청 본문이 필요 합니다.

아래 샘플 요청은 장치를 만들기 위한 것입니다. 여기에는 요청 본문을 사용 하는 입력 json이 있습니다.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

**쿼리 매개 변수**

REST **GET** 호출의 경우 요청 URI에 하나 이상의 쿼리 매개 변수를 포함 하 여 API 응답에서 데이터를 필터링 하 고, 크기를 제한 하 고, 데이터를 정렬할 수 있습니다. 쿼리 매개 변수의 경우 API 설명서를 참조 하 고 개별 GET 호출을 확인 하세요.
예를 들어 장치 목록을 쿼리할 때 (/Device에서 GET 호출) 다음과 같은 쿼리 매개 변수를 지정할 수 있습니다.  

![프로젝트 팜 비트](./media/for-references/query-parameters-device-1.png)

**오류 처리**

Azure FarmBeats Data hub API는 표준 HTTP 오류를 반환 합니다. 가장 일반적인 오류 코드는 다음과 같습니다.

 |오류 코드             | 설명 |
 |---                    | --- |
 |200                    | 성공 |
 |201                    | 만들기 (Post) 성공 |
 |400                    | 잘못된 요청. 요청에 오류가 있습니다. |
 |401                    | 권한이 없습니다. API 호출자에 게 리소스에 액세스할 수 있는 권한이 없습니다. |
 |404                    | 리소스를 찾을 수 없음 |
 |5XX                    | 내부 서버 오류입니다. 5XX로 시작 하는 오류 코드는 서버에서 오류가 발생 했음을 의미 합니다. 자세한 내용은 서버 로그를 참조 하 고 다음 섹션을 참조 하십시오. |


표준 HTTP 오류 외에도 Azure FarmBeats Data hub Api는 다음과 같은 형식으로 내부 오류를 반환 합니다.

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

예: 팜을 만들 때 입력 페이로드에 필수 필드 "Name"이 지정 되지 않았습니다. 결과 오류 메시지는 다음과 같습니다.

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="adding-users-or-app-registrations-to-azure-active-directory"></a>Azure Active Directory에 사용자 또는 앱 등록 추가

Azure FarmBeats Api는 Azure Active Directory에서 사용자 또는 앱 등록을 통해 액세스할 수 있습니다. Azure Active Directory에서 앱 등록을 만들려면 다음 단계를 수행 합니다.  

1. [Azure Portal](https://portal.azure.com) **Azure Active Directory, 앱 등록**, **새 등록**>로 이동 합니다. 또는 기존 계정을 사용할 수 있습니다.
2. 새 계정의 경우 다음을 완료 해야 합니다.

    - 이름 입력
    - **이 조직 디렉터리 에서만 계정 선택 (단일 테 넌 트)**
    - 나머지 필드의 기본값
    - **등록** 선택

3. 새/기존 앱 등록에서 **개요**, 다음을 완료 합니다.

    - **클라이언트 id** 및 **테 넌 트 id**를 캡처합니다.
    - 새 클라이언트 암호를 생성 하 고 **클라이언트 암호**를 캡처하려면 **인증서 및 암호** 로 이동 합니다.
    - 개요로 돌아가서 **로컬 디렉터리에서 응용 프로그램 관리** 옆의 링크를 클릭 합니다.
    - **속성** 으로 이동 하 여 **개체 ID** 를 캡처합니다.

4. [데이터 허브 Swagger](https://<yourdatahub>.azurewebsites.net/swagger/index.html) 로 이동 하 여 다음 단계를 수행 합니다.
    - **Roleassignment API** 로 이동 합니다.
    - **게시** 를 수행 하 여 방금 만든 **개체 ID** 에 대 한 roleassignment을 만듭니다. – 입력 json은 다음과 같습니다.

  > [!NOTE]
  > 사용자 및 AD 등록을 추가 하는 방법에 대 한 자세한 내용은 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 를 참조 하세요.

위의 단계를 완료 한 후 앱 등록 (클라이언트)은 전달자 인증을 통해 액세스 토큰을 사용 하 여 Azure FarmBeats Api를 호출할 수 있습니다.  

액세스 토큰을 사용 하 여 헤더 섹션의 후속 API 요청에서 다음과 같이 보냅니다.

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
