---
title: Personalizer 리소스 만들기
description: 서비스 구성에는 서비스의 보상 처리 방법, 서비스의 탐색 빈도, 모델을 다시 학습하는 빈도 및 저장할 데이터의 양이 포함됩니다.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: adb97db53d1fc0b6f0cdb14b697c82ec52501b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336055"
---
# <a name="create-a-personalizer-resource"></a>개인 설정 리소스 만들기

개인 설정 리소스는 개인 설정 학습 루프와 동일합니다. 단일 리소스 또는 학습 루프는 각 주제 도메인 또는 콘텐츠 영역에 대해 만들어집니다. 학습 루프를 혼동하고 잘못된 예측을 제공하기 때문에 동일한 루프에서 여러 콘텐츠 영역을 사용하지 마십시오.

개인 설정자가 웹 페이지의 두 개 이상의 콘텐츠 영역에 가장 적합한 콘텐츠를 선택하려면 각각에 대해 다른 학습 루프를 사용합니다.


## <a name="create-a-resource-in-the-azure-portal"></a>Azure Portal에서 리소스를 만듭니다.

각 피드백 루프에 대한 Personalizer 리소스를 만듭니다.

1. [Azure 포털에](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)로그인합니다. 이전 링크를 클릭하면 Personalizer 서비스의 **만들기** 페이지로 이동합니다.
1. 서비스 이름을 입력하고 구독, 위치, 가격 책정 계층 및 리소스 그룹을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![Azure 포털을 사용하여 학습 루프라고도 하는 개인 설정 자 리소스를 만듭니다.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. 리소스를 만들려면 **만들기를** 선택합니다.

1. 리소스가 배포된 후 **리소스로 이동 단추를** 선택하여 개인 설정 리소스로 이동합니다.

1. 리소스의 **빠른 시작** 페이지를 선택한 다음 끝점 및 키의 값을 복사합니다. 랭크 API와 리워드 API를 사용하려면 리소스 끝점과 키가 모두 필요합니다.

1. [학습 루프를 구성하려면](how-to-settings.md)새 리소스의 **구성** 페이지를 선택합니다.

## <a name="create-a-resource-with-the-azure-cli"></a>Azure CLI를 사용하여 리소스 만들기

1. 다음 명령을 사용하여 Azure CLI에 로그인합니다.

    ```azurecli-interactive
    az login
    ```

1. 개인 설정 리소스와 함께 사용하려는 모든 Azure 리소스를 관리하기 위한 논리적 그룹인 리소스 그룹을 만듭니다.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. 기존 리소스 그룹에 대한 다음 명령을 사용하여 새 Personalizer 리소스, _학습 루프를_만듭니다.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    그러면 **리소스 끝점이**포함된 JSON 개체가 반환됩니다.

1. 다음 Azure CLI 명령을 사용하여 **리소스 키를**가져옵니다.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    랭크 API와 리워드 API를 사용하려면 리소스 끝점과 키가 모두 필요합니다.

## <a name="next-steps"></a>다음 단계

* [구성](how-to-settings.md) 개인화 학습 루프