---
title: '빠른 시작: Python용 Azure 관리 클라이언트 라이브러리'
description: 이 빠른 시작에서는 Python용 Azure 관리 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 743b05b38eddc80ce7462a3439613fc767d91daa
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607638"
---
[참조 설명서](https://docs.microsoft.com/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices) | [패키지(PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/) | [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="prerequisites"></a>사전 요구 사항

* 유효한 Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

## <a name="create-an-azure-service-principal"></a>Azure 서비스 주체 만들기

애플리케이션이 Azure 계정과 상호 작용하도록 하려면 사용 권한을 관리하는 Azure 서비스 주체가 필요합니다. [Azure 서비스 주체 만들기](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0)의 지침을 따르세요.

서비스 주체를 만들면 비밀 값, ID 및 애플리케이션 ID가 있는 것을 볼 수 있습니다. 이후 단계를 위해 애플리케이션 ID 및 비밀을 임시 위치에 저장합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Cognitive Services 리소스를 만들기 전에 계정에 리소스를 포함할 Azure 리소스 그룹이 있어야 합니다. 리소스 그룹이 아직 없는 경우 [Azure Portal](https://ms.portal.azure.com/)에서 만듭니다.

## <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

원하는 편집기 또는 IDE에서 새 Python 애플리케이션을 만들고 콘솔 창에서 프로젝트로 이동합니다.

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install azure-mgmt-cognitiveservices
```

Visual Studio IDE를 사용하는 경우 클라이언트 라이브러리는 다운로드 가능한 NuGet 패키지로 제공됩니다.

### <a name="import-libraries"></a>라이브러리 가져오기

Python 스크립트를 열고 다음 라이브러리를 가져옵니다.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_imports)]

## <a name="authenticate-the-client"></a>클라이언트 인증

다음 필드를 스크립트의 루트에 추가하고 사용자가 만든 서비스 주체와 Azure 계정 정보를 사용하여 해당 값을 입력합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_constants)]

그런 다음, **CognitiveServicesManagementClient** 개체를 구성하는 다음 코드를 추가합니다. 이 개체는 모든 Azure 관리 작업에 필요합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_auth)]

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services 리소스 만들기

### <a name="choose-a-service-and-pricing-tier"></a>서비스 및 가격 책정 계층 선택

새 리소스를 만들 때 원하는 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/)(또는 SKU)과 함께 사용할 서비스의 "종류"를 알아야 합니다. 리소스를 만들 때 이 정보와 기타 정보를 매개 변수로 사용합니다. 다음 함수는 사용 가능한 Cognitive Service "종류"를 나열합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

아래 SKU 목록과 가격 정보를 참조하세요. 

#### <a name="multi-service"></a>다중 서비스

| 서비스                    | 종류                      |
|----------------------------|---------------------------|
| 여러 서비스. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/) 페이지를 참조하세요.            | `CognitiveServices`     |


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

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

## <a name="view-your-resources"></a>리소스 보기

모든 리소스 그룹에서 Azure 계정 아래의 모든 리소스를 보려면 다음 함수를 사용합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list)]

## <a name="delete-a-resource"></a>리소스 삭제

다음 함수는 지정된 리소스 그룹에서 지정된 리소스를 삭제합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_delete)]

## <a name="call-management-functions"></a>호출 관리 함수

위의 함수를 호출하려면 다음 코드를 스크립트의 아래쪽에 추가합니다. 이 코드는 사용 가능한 리소스를 나열하고, 샘플 리소스를 만들고, 소유한 리소스를 나열한 다음, 샘플 리소스를 삭제합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_calls)]

## <a name="run-the-application"></a>애플리케이션 실행

`python` 명령을 사용하여 명령줄에서 애플리케이션을 실행합니다.

```console
python <your-script-name>.py
```

## <a name="see-also"></a>참고 항목

* [Azure Management SDK 참조 설명서](https://docs.microsoft.com/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python)
* [Azure Cognitive Services란?](../../Welcome.md)
* [Azure Cognitive Services에 대한 요청 인증](../../authentication.md)
* [Azure Portal를 사용하여 새 리소스 만들기](../../cognitive-services-apis-create-account.md)