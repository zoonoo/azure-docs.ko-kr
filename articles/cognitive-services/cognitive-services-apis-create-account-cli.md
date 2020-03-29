---
title: Azure CLI를 사용하여 코그너티브 서비스 리소스 만들기
titleSuffix: Azure Cognitive Services
description: Azure 명령줄 인터페이스를 사용하여 리소스를 만들고 구독하여 Azure Cognitive 서비스를 시작하십시오.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: aahi
ms.openlocfilehash: 72b00d78d19ed0e963b4dad01b82033c659e1efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221260"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Azure 명령줄 인터페이스(CLI)를 사용하여 코그너티브 서비스 리소스 만들기

이 빠른 시작을 사용하여 [CLI(Azure 명령줄 인터페이스)를](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)사용하여 Azure 인지 서비스를 시작합니다. 코그너티브 서비스는 Azure 구독에서 만드는 Azure [리소스로](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) 표시됩니다. 리소스를 만든 후 생성된 키 및 끝점을 사용하여 응용 프로그램을 인증합니다. 


이 빠른 시작에서는 Azure 코그너티브 서비스에 등록하고 [CLI(Azure 명령줄 인터페이스)를](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)사용하여 단일 서비스 또는 다중 서비스 구독이 있는 계정을 만드는 방법을 알아봅니다. 이러한 서비스는 하나 이상의 Azure 인지 서비스 API에 연결할 수 있는 Azure [리소스로](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)표시됩니다.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>사전 요구 사항

* 유효한 Azure 구독 - 무료로 [구독을 만듭니다.](https://azure.microsoft.com/free/)
* [Azure 명령줄 인터페이스(CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>Azure CLI 를 설치하고 로그인 

Azure [CLI를](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)설치합니다. CLI의 로컬 설치에 로그인하려면 [az 로그인](https://docs.microsoft.com/cli/azure/reference-index#az-login) 명령을 실행합니다.

```azurecli-interactive
az login
```

녹색 **시도** 단추를 사용하여 브라우저에서 이러한 명령을 실행할 수도 있습니다.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>새 Azure 코그너티브 서비스 리소스 그룹 만들기

코그너티브 서비스 리소스를 만들기 전에 리소스를 포함할 Azure 리소스 그룹이 있어야 합니다. 새 리소스를 만들 때 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용할 수 있습니다. 이 문서에서는 새 리소스 그룹을 만드는 방법을 보여 주며 있습니다.

### <a name="choose-your-resource-group-location"></a>리소스 그룹 위치 선택

리소스를 만들려면 구독에 사용할 수 있는 Azure 위치 중 하나가 필요합니다. az 계정 목록 위치 명령을 사용 하 여 사용 가능한 위치 [목록을 검색할](/cli/azure/account#az-account-list-locations) 수 있습니다. 대부분의 코그너티브 서비스는 여러 위치에서 액세스할 수 있습니다. 가장 가까운 위치를 선택하거나 서비스에 사용할 수 있는 위치를 확인합니다.

> [!IMPORTANT]
> * Azure 인지 서비스를 호출할 때 필요할 수 있으므로 Azure 위치를 기억하십시오.
> * 일부 코그너티브 서비스의 이용 가능 여부는 지역에 따라 다를 수 있습니다. 자세한 내용은 [지역별 Azure 제품을](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)참조하십시오.  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Azure 위치가 있는 후 [az 그룹 만들기](/cli/azure/group#az-group-create) 명령을 사용하여 Azure CLI에 새 리소스 그룹을 만듭니다.

아래 예에서 Azure 위치를 `westus2` 구독에 사용할 수 있는 Azure 위치 중 하나로 바꿉습니다.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services 리소스 만들기

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>코그너티브 서비스 및 가격 책정 계층 선택

새 리소스를 만들 때 [사용하려는](https://azure.microsoft.com/pricing/details/cognitive-services/) 서비스의 "종류"와 가격 책정 계층(또는 sku)을 알아야 합니다. 리소스를 만들 때 이 정보와 기타 정보를 매개 변수로 사용합니다.

### <a name="multi-service"></a>다중 서비스

| 서비스                    | 종류                      |
|----------------------------|---------------------------|
| 여러 서비스. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/) 페이지를 참조하십시오.            | `CognitiveServices`     |


> [!NOTE]
> 아래의 많은 코그너티브 서비스에는 서비스를 시도하는 데 사용할 수 있는 프리 티어가 있습니다. 프리 티어를 사용하려면 리소스의 sku로 사용합니다. `F0`

### <a name="vision"></a>시각

| 서비스                    | 종류                      |
|----------------------------|---------------------------|
| Computer Vision            | `ComputerVision`          |
| 사용자 지정 비전 - 예측 | `CustomVision.Prediction` |
| 사용자 지정 비전 - 교육   | `CustomVision.Training`   |
| Face                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

### <a name="search"></a>검색

| 서비스            | 종류                  |
|--------------------|-----------------------|
| Bing Autosuggest   | `Bing.Autosuggest.v7` |
| Bing 사용자 지정 검색 | `Bing.CustomSearch`   |
| Bing Entity Search | `Bing.EntitySearch`   |
| Bing Search        | `Bing.Search.v7`      |
| Bing 맞춤법 검사   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Speech

| 서비스            | 종류                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| 음성 인식 | `SpeakerRecognition` |

### <a name="language"></a>언어

| 서비스            | 종류                |
|--------------------|---------------------|
| 양식 이해 | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| 텍스트 분석     | `TextAnalytics`     |
| 텍스트 번역   | `TextTranslation`   |

### <a name="decision"></a>의사 결정

| 서비스           | 종류               |
|-------------------|--------------------|
| Anomaly Detector  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |

[az Cognitiveservices 계정 목록 종류](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) 명령을 통해 사용 가능한 인지 서비스 "종류" 목록을 찾을 수 있습니다.

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>리소스 그룹에 새 리소스 추가

새 코그너티브 서비스 리소스를 만들고 구독하려면 [az Cognitiveservices 계정 create](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) 명령을 사용합니다. 이 명령은 이전에 만든 리소스 그룹에 새 청구 가능한 리소스를 추가합니다. 새 리소스를 만들 때 가격 책정 계층(또는 sku) 및 Azure 위치와 함께 사용하려는 서비스의 "종류"를 알아야 합니다.

아래 명령으로 명명된 `anomaly-detector-resource` 변칙 탐지기에 대한 F0(무료) 리소스를 만들 수 있습니다.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>리소스에 대한 키 받기

명령줄 인터페이스(CLI)의 로컬 설치에 로그인하려면 [az 로그인](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 명령을 사용합니다.

```azurecli-interactive
az login
```

az [코그너티브 서비스 계정 키 목록](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) 명령을 사용하여 코그너티브 서비스 리소스의 키를 가져옵니다.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>가격 등급 및 청구

가격 책정 계층(및 청구되는 금액)은 인증 정보를 사용하여 보내는 트랜잭션 수를 기반으로 합니다. 각 가격 책정 계층은 다음을 지정합니다.
* 허용되는 트랜잭션의 최대 수(TPS)입니다.
* 가격 책정 계층 내에서 활성화된 서비스 기능입니다.
* 미리 정의된 트랜잭션 양에 대한 비용입니다. 이 금액을 초과하면 서비스에 대한 가격 [세부 정보에](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) 명시된 추가 요금이 부과됩니다.

## <a name="get-current-quota-usage-for-your-resource"></a>리소스에 대한 현재 할당량 사용량 얻기

az [코그너티브 서비스 계정 목록 사용](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage) 명령을 사용하여 코그너티브 서비스 리소스에 대한 사용량을 가져옵니다.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>리소스 정리

코그너티브 서비스 리소스를 정리하고 제거하려면 코그너티브 서비스 리소스 또는 리소스 그룹을 삭제할 수 있습니다. 리소스 그룹을 삭제해도 그룹에 포함된 다른 리소스도 삭제됩니다.

리소스 그룹 및 관련 리소스를 제거하려면 az 그룹 삭제 명령을 사용합니다.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>참조

* [Azure Cognitive Services에 대한 요청 인증](authentication.md)
* [Azure 코그너티브 서비스란?](Welcome.md)
* [자연어 지원](language-support.md)
* [도커 컨테이너 지원](cognitive-services-container-support.md)
