---
title: '빠른 시작: Node.js용 Azure 관리 클라이언트 라이브러리'
description: 이 빠른 시작에서는 Node.js용 Azure 관리 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 59063912b2976890976e3c27bc4f2d93b6392e37
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607643"
---
[참조 설명서](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices) | [패키지(NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices) | [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="prerequisites"></a>사전 요구 사항

* 유효한 Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 현재 버전의 [Node.js](https://nodejs.org/)

## <a name="create-an-azure-service-principal"></a>Azure 서비스 주체 만들기

애플리케이션이 Azure 계정과 상호 작용하도록 하려면 사용 권한을 관리하는 Azure 서비스 주체가 필요합니다. [Azure 서비스 주체 만들기](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0)의 지침을 따르세요.

서비스 주체를 만들면 비밀 값, ID 및 애플리케이션 ID가 있는 것을 볼 수 있습니다. 이후 단계를 위해 애플리케이션 ID 및 비밀을 임시 위치에 저장합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Cognitive Services 리소스를 만들기 전에 계정에 리소스를 포함할 Azure 리소스 그룹이 있어야 합니다. 리소스 그룹이 아직 없는 경우 [Azure Portal](https://ms.portal.azure.com/)에서 만듭니다.

## <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp && cd myapp
```

`package.json` 파일을 사용하여 노드 애플리케이션을 만들려면 `npm init` 명령을 실행합니다. 

```console
npm init
```

계속하기 전에 _index.js_라는 파일을 만듭니다.

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

다음 NPM 패키지를 설치합니다.

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

종속성이 있는 앱의 `package.json` 파일이 업데이트됩니다.

### <a name="import-libraries"></a>라이브러리 가져오기

_index.js_ 스크립트를 열고 다음 라이브러리를 가져옵니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>클라이언트 인증

다음 필드를 스크립트의 루트에 추가하고 사용자가 만든 서비스 주체와 Azure 계정 정보를 사용하여 해당 값을 입력합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

그런 다음, 다음 `quickstart` 함수를 추가하여 프로그램의 주요 작업을 처리합니다. 첫 번째 코드 블록은 위에서 입력한 자격 증명 변수를 사용하여 **CognitiveServicesManagementClient** 개체를 생성합니다. 이 개체는 모든 Azure 관리 작업에 필요합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>호출 관리 함수

`quickstart` 함수 끝에 다음 코드를 추가하여 사용 가능한 리소스를 나열하고, 샘플 리소스를 만들고, 소유한 리소스를 나열한 다음, 샘플 리소스를 삭제합니다. 다음 단계에서 이러한 함수를 정의합니다.

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services 리소스 만들기

### <a name="choose-a-service-and-pricing-tier"></a>서비스 및 가격 책정 계층 선택

새 리소스를 만들 때 원하는 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/)(또는 SKU)과 함께 사용할 서비스의 "종류"를 알아야 합니다. 리소스를 만들 때 이 정보와 기타 정보를 매개 변수로 사용합니다. 다음 함수는 사용 가능한 Cognitive Service "종류"를 나열합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

아래 SKU 목록과 가격 정보를 참조하세요. 

#### <a name="multi-service"></a>다중 서비스

| 서비스                    | 종류                      |
|----------------------------|---------------------------|
| 여러 서비스 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/) 페이지를 참조하세요.            | `CognitiveServices`     |


#### <a name="vision"></a>Vision

| 서비스                    | 종류                      |
|----------------------------|---------------------------|
| Computer Vision            | `ComputerVision`          |
| Custom Vision - 예측 | `CustomVision.Prediction` |
| Custom Vision - 학습   | `CustomVision.Training`   |
| Face                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

#### <a name="search"></a>검색

| 서비스            | 종류                  |
|--------------------|-----------------------|
| Bing Autosuggest   | `Bing.Autosuggest.v7` |
| Bing 사용자 지정 검색 | `Bing.CustomSearch`   |
| Bing Entity Search | `Bing.EntitySearch`   |
| Bing Search        | `Bing.Search.v7`      |
| Bing 맞춤법 검사   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>음성

| 서비스            | 종류                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| 음성 인식 | `SpeakerRecognition` |

#### <a name="language"></a>언어

| 서비스            | 종류                |
|--------------------|---------------------|
| 양식 이해 | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| 텍스트 분석     | `TextAnalytics`     |
| 텍스트 번역   | `TextTranslation`   |

#### <a name="decision"></a>의사 결정

| 서비스           | 종류               |
|-------------------|--------------------|
| Anomaly Detector  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>가격 책정 계층 및 요금 청구

가격 책정 계층(및 청구되는 금액)은 인증 정보를 사용하여 전송하는 트랜잭션 수를 기반으로 합니다. 각 가격 책정 계층은 다음을 지정합니다.
* 초당 허용되는 최대 트랜잭션 수(TPS)입니다.
* 가격 책정 계층 내에서 사용하도록 설정된 서비스 기능입니다.
* 미리 정의된 트랜잭션 수에 대한 비용입니다. 이 숫자를 초과하면 서비스에 대한 [가격 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)에 지정된 대로 추가 요금이 발생합니다.

> [!NOTE]
> 대부분의 Cognitive Services에는 서비스를 시도하는 데 사용할 수 있는 체험 계층이 있습니다. 체험 계층을 사용하려면 리소스의 SKU로 `F0`을 사용합니다.

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services 리소스 만들기

새 Cognitive Services 리소스를 만들고 구독하려면 **Create** 함수를 사용합니다. 이 함수는 전달하는 리소스 그룹에 청구 가능한 새 리소스를 추가합니다. 새 리소스를 만들 때 가격 책정 계층(또는 SKU) 및 Azure 위치와 함께 사용할 서비스의 "종류"를 알아야 합니다. 다음 함수는 이러한 인수를 모두 사용하여 리소스를 만듭니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

## <a name="view-your-resources"></a>리소스 보기

모든 리소스 그룹에서 Azure 계정 아래의 모든 리소스를 보려면 다음 함수를 사용합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>리소스 삭제

다음 함수는 지정된 리소스 그룹에서 지정된 리소스를 삭제합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>애플리케이션 실행

오류를 처리하는 주 `quickstart` 함수를 호출하려면 다음 코드를 스크립트의 아래쪽에 추가합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

그런 다음, 콘솔 창에서 `node` 명령을 사용하여 애플리케이션을 실행합니다.

```console
node index.js
```

## <a name="see-also"></a>참고 항목

* [Azure Management SDK 참조 설명서](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)
* [Azure Cognitive Services란?](../../Welcome.md)
* [Azure Cognitive Services에 대한 요청 인증](../../authentication.md)
* [Azure Portal를 사용하여 새 리소스 만들기](../../cognitive-services-apis-create-account.md)