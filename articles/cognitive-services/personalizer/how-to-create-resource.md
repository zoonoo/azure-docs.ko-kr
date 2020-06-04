---
title: Personalizer 리소스 만들기
description: 서비스 구성에는 서비스의 보상 처리 방법, 서비스의 탐색 빈도, 모델을 다시 학습하는 빈도 및 저장할 데이터의 양이 포함됩니다.
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 8b56516b5555ca971d7ab1db3caf5b76f252729b
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344376"
---
# <a name="create-a-personalizer-resource"></a>Personalizer 리소스 만들기

Personalizer 리소스는 Personalizer learning 루프와 동일 합니다. 각 주체 도메인 또는 콘텐츠 영역에 대해 단일 리소스 또는 학습 루프가 생성 됩니다. 학습 루프를 혼동 하 고 잘못 된 예측을 제공 하기 때문에 같은 루프에서 여러 콘텐츠 영역을 사용 하지 마십시오.

Personalizer가 웹 페이지의 둘 이상의 콘텐츠 영역에 대 한 최상의 콘텐츠를 선택 하도록 하려면 각에 대해 서로 다른 학습 루프를 사용 합니다.


## <a name="create-a-resource-in-the-azure-portal"></a>Azure Portal에서 리소스를 만듭니다.

각 피드백 루프에 대한 Personalizer 리소스를 만듭니다.

1. [Azure 포털](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)에 로그인합니다. 이전 링크를 클릭하면 Personalizer 서비스의 **만들기** 페이지로 이동합니다.
1. 서비스 이름을 입력하고 구독, 위치, 가격 책정 계층 및 리소스 그룹을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![Azure Portal를 사용 하 여 학습 루프 라고도 하는 Personalizer 리소스를 만듭니다.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. **만들기** 를 선택 하 여 리소스를 만듭니다.

1. 리소스를 배포한 후 **리소스로 이동** 단추를 선택 하 여 Personalizer 리소스로 이동 합니다.

1. 리소스에 대 한 **빠른 시작** 페이지를 선택한 다음, 끝점과 키에 대 한 값을 복사 합니다. 랭크 및 보상 Api를 사용 하려면 리소스 끝점과 키가 모두 필요 합니다.

1. 새 리소스에 대 한 **구성** 페이지를 선택 하 여 [학습 루프를 구성](how-to-settings.md)합니다.

## <a name="create-a-resource-with-the-azure-cli"></a>Azure CLI를 사용 하 여 리소스 만들기

1. 다음 명령을 사용 하 여 Azure CLI에 로그인 합니다.

    ```azurecli-interactive
    az login
    ```

1. Personalizer 리소스와 함께 사용 하려는 모든 Azure 리소스를 관리 하기 위한 논리적 그룹인 리소스 그룹을 만듭니다.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. 기존 리소스 그룹에 대 한 다음 명령을 사용 하 여 새 Personalizer 리소스 인 _학습 루프_를 만듭니다.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    그러면 **리소스 끝점**을 포함 하는 JSON 개체가 반환 됩니다.

1. 다음 Azure CLI 명령을 사용 하 여 **리소스 키**를 가져옵니다.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    랭크 및 보상 Api를 사용 하려면 리소스 끝점과 키가 모두 필요 합니다.

## <a name="next-steps"></a>다음 단계

* [구성](how-to-settings.md) Personalizer learning 루프