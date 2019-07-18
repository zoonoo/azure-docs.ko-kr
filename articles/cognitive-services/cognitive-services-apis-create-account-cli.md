---
title: Azure CLI를 사용 하 여 Cognitive Services 계정 만들기
titlesuffix: Azure Cognitive Services
description: Azure CLI를 사용 하 여 Azure Cognitive Services Api 계정을 만드는 방법입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: acafc2c42c2946632496b646d001c58d6b48c2a6
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657723"
---
# <a name="create-a-cognitive-services-account-using-the-azure-command-line-interfacecli"></a>Azure 명령줄 Interface(CLI)를 사용 하 여 Cognitive Services 계정 만들기

이 빠른 시작에서는 Azure Cognitive Services에 등록 하 고 단일 서비스 또는 다중 서비스 구독에 계정을 만들어야 하는 방법을 알아봅니다 사용 합니다 [Azure 명령줄 Interface(CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)합니다. 이러한 서비스는 Azure에서 표시 됩니다 [리소스](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), 하나 이상의 Azure Cognitive Services Api에 연결할 수 있도록 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 유효한 Azure 구독. 평가판 [계정을 만들 수 있습니다](https://azure.microsoft.com/free/).
* [Azure 명령줄 Interface(CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>Azure CLI 설치 및 로그인 

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 설치합니다. CLI의 로컬 설치에 로그인 하려면 다음을 실행 합니다 [az 로그인](https://docs.microsoft.com/cli/azure/reference-index#az-login) 명령:

```console
az login
```

녹색을 사용할 수도 있습니다 **시도** 브라우저에서 이러한 명령을 실행 하는 단추입니다.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>새 Azure Cognitive Services 리소스 그룹 만들기

구독에서 Cognitive Services는 Azure 리소스에서 표시 됩니다. 모든 Cognitive Services 계정 (및 관련된 Azure 리소스)은 Azure 리소스 그룹에 속해야 합니다.

### <a name="choose-your-resource-group-location"></a>리소스 그룹 위치 선택

리소스를 만들려면 해야 사용할 수 있는 Azure 위치 중 하나에 구독에 대 한 합니다. 사용 하 여 사용 가능한 위치 목록을 검색할 수 있습니다 합니다 [위치 목록 az 계정](/cli/azure/account#az-account-list-locations) 명령입니다. 대부분의 Cognitive Services는 여러 위치에서 액세스할 수 있습니다. 사용자에 게 가장 가까운 하나를 선택 하거나 서비스에 사용할 수 있는 위치를 참조 하세요.

> [!IMPORTANT]
> * 필요할 때 Azure Cognitive Services를 호출할 때 해당 Azure 위치를 기억 합니다.
> * 일부 Cognitive Services의 가용성은 지역에 따라 달라질 수 있습니다. 자세한 내용은 [지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)합니다.  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Azure 위치를 설정한 후 새 리소스 그룹을 사용 하 여 Azure CLI 만들기 합니다 [az 그룹 만들기](/cli/azure/group#az-group-create) 명령입니다.

아래 예제에서는 azure 위치를 바꿀 `westus2` 구독에 사용할 수 있는 Azure 위치 중 하나를 사용 하 여 합니다.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services 리소스 만들기

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Cognitive 서비스 및 가격 책정 계층 선택

함께 사용 하 여 사용 하려는 서비스의 "종류" 알아야 할는 새 리소스를 만들 때 합니다 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/) (또는 sku) 있습니다. 리소스를 만들 때이 정보와 다른 매개 변수로 사용 합니다.

> [!NOTE]
> 많은 Cognitive services 무료 계층을 제공 서비스를 사용할 수 있습니다. 무료 계층을 사용 하려면 `F0` 리소스에 대 한 sku로 합니다.

### <a name="vision"></a>시각

| 서비스                    | Kind                      |
|----------------------------|---------------------------|
| Computer Vision            | `ComputerVision`          |
| Custom Vision-예측 | `CustomVision.Prediction` |
| 사용자 지정 비전-학습   | `CustomVision.Training`   |
| Face API                   | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

### <a name="search"></a>검색

| 서비스            | Kind                  |
|--------------------|-----------------------|
| Bing Autosuggest   | `Bing.Autosuggest.v7` |
| Bing 사용자 지정 검색 | `Bing.CustomSearch`   |
| Bing Entity Search | `Bing.EntitySearch`   |
| Bing Search        | `Bing.Search.v7`      |
| Bing 맞춤법 검사   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>음성 명령

| 서비스            | Kind                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| 음성 인식 | `SpeakerRecognition` |

### <a name="language"></a>언어

| 서비스            | Kind                |
|--------------------|---------------------|
| 양식 이해 | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| 텍스트 분석     | `TextAnalytics`     |
| 텍스트 번역   | `TextTranslation`   |

### <a name="decision"></a>결정

| 서비스           | Kind               |
|-------------------|--------------------|
| Anomaly Detector  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |

사용 하 여 사용 가능한 Cognitive 서비스 목록을 "종류"을 찾을 수 있습니다 합니다 [az cognitiveservices 계정을 목록 종류](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) 명령:

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>리소스 그룹에 새 리소스 추가

만들고 새 Cognitive Services 리소스에 구독을 사용 합니다 [az cognitiveservices 계정을 만들](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) 명령. 이 명령은 이전에 만든 리소스 그룹에 새 청구 가능한 리소스를 추가 합니다. 또는 함께 해당 가격 책정 계층 (sku)를 사용 하려는 서비스의 "종류" 알아야 할는 새 리소스를 만들 때 Azure 위치 및:

이상 감지기를 라는 F0 (무료) 리소스를 만들 수 있습니다 `anomaly-detector-resource` 아래 명령을 사용 하 여 합니다.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-subscription"></a>구독에 대 한 키를 가져옵니다.

명령줄 Interface(CLI)의 로컬 설치에 로그인 하려면 사용 합니다 [az 로그인](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 명령입니다.

```console
az login
```

사용 된 [az cognitiveservices 계정 키 목록](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) Cognitive Service 리소스에 대 한 키를 가져오는 명령입니다.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>리소스 정리

정리 하 고 Cognitive Services 구독을 제거 하려는 경우에 리소스 또는 리소스 그룹을 삭제할 수 있습니다. 리소스 그룹과 다른 리소스를 삭제도 리소스 그룹을 삭제 합니다.

리소스 그룹 및 새 저장소 계정을 포함 하 여 관련된 리소스를 제거 하려면 az group delete 명령을 사용 합니다.

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>참고자료

* [Azure Cognitive Services에 대한 요청 인증](authentication.md)
* [Azure Cognitive Services 란?](Welcome.md)
* [자연 언어 지원](language-support.md)
* [Docker 컨테이너 지원](cognitive-services-container-support.md)
