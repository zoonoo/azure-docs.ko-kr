---
title: AutoRest를 사용하여 사용자 지정 언어 SDK 만들기
titleSuffix: Azure Digital Twins
description: AutoRest를 사용 하 여 게시 된 Sdk가 없는 다른 언어로 Azure 디지털 Twins 코드를 작성 하는 사용자 지정 언어 Sdk를 생성 하는 방법에 대해 알아봅니다.
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 35cf54199f8f2c187ad397c21fb941111f07c4a3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561843"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>AutoRest를 사용 하 여 Azure Digital Twins 용 사용자 지정 언어 Sdk 만들기

[게시 된 Azure Digital TWINS SDK](how-to-use-apis-sdks.md)가 없는 언어를 사용 하 여 Azure Digital twins로 작업 해야 하는 경우이 문서에서는 AutoRest를 사용 하 여 사용자가 선택한 언어로 SDK를 생성 하는 방법을 보여 줍니다. 

이 문서의 예제에서는 [데이터 평면 sdk](how-to-use-apis-sdks.md#overview-data-plane-apis)를 만드는 방법을 보여 주지만이 프로세스는  [제어 평면 sdk](how-to-use-apis-sdks.md#overview-control-plane-apis) 를 생성 하는 데에도 적용 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

SDK를 생성 하려면 먼저 로컬 컴퓨터에서 다음 설치를 완료 해야 합니다.
* [**AutoRest**](https://github.com/Azure/autorest)설치, 버전 2.0.4413 (현재 지원 되지 않는 버전 3)
* AutoRest를 사용 하기 위한 필수 구성 요소인 [**Node.js**](https://nodejs.org)를 설치 합니다.
* [ **Visual Studio** 설치](https://visualstudio.microsoft.com/downloads/)
* 함께 제공 된 예제 폴더와 함께 [데이터 평면 swagger 폴더](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)에서 최신 Azure 디지털 twins **데이터 평면 swagger** (openapi) 파일을 다운로드 합니다. Swagger 파일은 *digitaltwins.js에서* 호출 되는 파일입니다.

>[!TIP]
> 대신 **제어 평면 SDK** 를 만들려면이 문서의 단계를 완료 하 고 데이터 평면 [swagger 폴더](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) 에서 최신 **제어 평면 swagger** (openapi) 파일을 사용 합니다.

컴퓨터에 위의 목록에 있는 모든 항목이 장착 되 면 AutoRest를 사용 하 여 SDK를 만들 준비가 된 것입니다.

## <a name="create-the-sdk-using-autorest"></a>AutoRest를 사용 하 여 SDK 만들기 

Node.js 설치 되 면 다음 명령을 실행 하 여 필요한 버전의 AutoRest가 설치 되어 있는지 확인할 수 있습니다.
```cmd/sh
npm install -g autorest@2.0.4413
```

Azure Digital Twins Swagger 파일에 대해 AutoRest를 실행 하려면 다음 단계를 수행 합니다.
1. Azure Digital Twins Swagger 파일 및 함께 제공 되는 예제 폴더를 작업 디렉터리에 복사 합니다.
2. 명령 프롬프트 창을 사용 하 여 해당 작업 디렉터리로 전환 합니다.
3. 다음 명령을 사용 하 여 AutoRest를 실행 합니다. `<language>`자리 표시자를 원하는 언어 (,, 등)로 바꿉니다 `python` `java` `go` . [AUTOREST 추가 정보](https://github.com/Azure/autorest)에서 전체 옵션 목록을 찾을 수 있습니다.

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

결과적으로 작업 디렉터리에 *DigitalTwinsApi* 라는 새 폴더가 표시 됩니다. 생성 된 SDK 파일에는 *DigitalTwinsApi* 네임 스페이스가 포함 됩니다. 이 문서의 나머지 사용 예에서는이 네임 스페이스를 계속 사용 합니다.

AutoRest는 다양 한 언어 코드 생성기를 지원 합니다.

## <a name="make-the-sdk-into-a-class-library"></a>SDK를 클래스 라이브러리로 만들기

AutoRest에서 생성 된 파일을 .NET 솔루션에 직접 포함할 수 있습니다. 그러나 여러 개별 프로젝트 (클라이언트 앱, Azure Functions 앱 등)에 Azure Digital Twins SDK를 포함 하려는 경우가 있습니다. 따라서 생성 된 파일에서 별도의 프로젝트 (.NET 클래스 라이브러리)를 빌드하는 것이 유용할 수 있습니다. 그런 다음이 클래스 라이브러리 프로젝트를 여러 솔루션에 프로젝트 참조로 포함할 수 있습니다.

이 섹션에서는 SDK를 클래스 라이브러리로 빌드하는 방법에 대 한 지침을 제공 합니다 .이 라이브러리는 자체 프로젝트 이며 다른 프로젝트에 포함 될 수 있습니다. 이러한 단계는 **Visual Studio** 를 사용 합니다.

단계는 다음과 같습니다.

1. 클래스 라이브러리에 대 한 새 Visual Studio 솔루션 만들기
2. 프로젝트 이름으로 *DigitalTwinsApi* 사용
3. 솔루션 탐색기에서 생성 된 솔루션의 *DigitalTwinsApi* 프로젝트를 마우스 오른쪽 단추로 선택 하 고 *기존 항목 > 추가* ...를 선택 합니다.
4. SDK를 생성 한 폴더를 찾고 루트 수준에서 파일을 선택 합니다.
5. "확인"을 누릅니다.
6. 프로젝트에 폴더를 추가 합니다 (솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 선택 하 고 *> 새 폴더 추가* 선택).
7. 폴더 이름을 *모델* 으로
8. 솔루션 탐색기에서 *모델* 폴더를 마우스 오른쪽 단추로 선택 하 고 *기존 항목 > 추가* ...를 선택 합니다.
9. 생성 된 SDK의 *모델* 폴더에서 파일을 선택 하 고 "확인"을 누릅니다.

SDK를 성공적으로 빌드하려면 프로젝트에 다음 참조가 필요 합니다.
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

이러한 도구를 추가 하려면 *> Nuget 패키지 관리자 > 솔루션에 대 한 Nuget 패키지 관리*...를 엽니다.

1. 패널에서 *찾아보기* 탭이 선택 되어 있는지 확인 합니다.
2. *Microsoft Rest를 검색 합니다.*
3. `ClientRuntime`및 `ClientRuntime.Azure` 패키지를 선택 하 고 솔루션에 추가 합니다.

이제 프로젝트를 빌드하고 작성 한 모든 Azure 디지털 쌍 응용 프로그램에 프로젝트 참조로 포함할 수 있습니다.

## <a name="tips-for-using-the-sdk"></a>SDK 사용 팁

이 섹션에는 생성 된 SDK 사용에 대 한 일반 정보 및 지침이 포함 되어 있습니다.

### <a name="synchronous-and-asynchronous-calls"></a>동기 및 비동기 호출

모든 SDK 함수는 동기 및 비동기 버전으로 제공 됩니다.

### <a name="typed-and-untyped-data"></a>형식화 된 데이터 및 형식화 되지 않은 데이터

REST API 호출은 일반적으로 강력한 형식의 개체를 반환 합니다. 그러나 Azure Digital Twins는 사용자가 쌍에 대해 고유한 사용자 지정 형식을 정의할 수 있으므로 여러 Azure 디지털 쌍 호출에 대해 정적 반환 데이터를 미리 정의할 수 없습니다. 대신, Api는 해당 하는 경우 강력한 형식의 래퍼 형식을 반환 하 고 사용자 지정 쌍 데이터는 Json.NET 개체에 있습니다 (데이터 형식이 "object"가 API 서명에 표시 되는 경우에 사용 됨). 코드에서 이러한 개체를 적절 하 게 캐스팅할 수 있습니다.

### <a name="error-handling"></a>오류 처리

SDK에서 오류가 발생할 때마다 (404 등의 HTTP 오류 포함) SDK에서 예외가 throw 됩니다. 따라서 try/catch 블록 내에서 모든 API 호출을 캡슐화 하는 것이 중요 합니다.

다음은 쌍을 추가 하 고이 프로세스에서 오류를 catch 하는 코드 조각입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>페이징

AutoRest는 SDK에 대해 두 가지 유형의 페이징 패턴을 생성 합니다.
* 쿼리 API를 제외한 모든 Api에 대 한 하나
* 쿼리 API에 대 한 하나

쿼리가 아닌 페이징 패턴에서는 Azure Digital Twins에서 나가는 관계의 페이지 단위 목록을 검색 하는 방법을 보여 주는 샘플 메서드는 다음과 같습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

두 번째 패턴은 쿼리 API에 대해서만 생성 됩니다. 를 `continuationToken` 명시적으로 사용 합니다.

>[!TIP]
> 페이지를 가져오는 주된 이유는 쿼리 API 호출에 대 한 [쿼리 단위 요금](concepts-query-units.md) 을 계산 하는 것입니다.

이 패턴의 예는 다음과 같습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>다음 단계

SDK를 사용할 수 있는 클라이언트 앱을 만드는 단계를 안내 합니다.
* [*자습서: 클라이언트 앱 코딩*](tutorial-code.md)
