---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 1ea0b01997d3d5cecff73f951c51de5898c2f07a
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503701"
---
다음 Azure CLI 명령은 무료 가격 책정 계층에서 Anomaly Detector 리소스를 프로비전합니다. **시도** 단추를 클릭하고 코드를 붙여넣고 Enter 키를 눌러 Azure Cloud Shell에서 코드를 실행합니다. 애플리케이션 인증을 위한 키가 출력됩니다. 완료되면 `ANOMALY_DETECTOR_KEY`라는 키에 대해 [환경 변수를 만듭니다](../articles/cognitive-services/cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication).

> [!NOTE]
> * 필요에 따라 다음을 수행할 수 있습니다.
>    * 로컬 컴퓨터에서 [Azure Portal](../articles/cognitive-services/cognitive-services-apis-create-account.md) 또는 [Azure CLI](../articles/cognitive-services/cognitive-services-apis-create-account.md)를 사용하여 리소스를 만듭니다.
>    * 7일 동안 유효한 [평가판 키](https://azure.microsoft.com/try/cognitive-services/#decision)를 가져옵니다. 키를 등록 후 [Azure 웹 사이트](https://azure.microsoft.com/try/cognitive-services/my-apis/)에서 사용할 수 있습니다.
>    * [Azure Portal](https://portal.azure.com/)에서 이 리소스를 확인합니다. 

Cognitive Services는 프로비전하는 Azure 리소스로 표시됩니다. 모든 Cognitive Services 계정(및 해당 Azure 리소스)은 Azure 리소스 그룹에 속해야 합니다.

1. Azure 리소스 그룹 만들기

    ```azurecli-interactive
    az group create \
        --name example-anomaly-detector-resource-group \
        --location westus2
    ```

2. 무료 계층에서 Anomaly Detector 리소스 만들기

    ```azurecli-interactive
    az cognitiveservices account create \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group \
        --kind AnomalyDetector \
        --sku F0 \
        --location westus2 \
        --yes
    ```

3. 리소스에 대한 키 나열

    ```azurecli-interactive
    az cognitiveservices account keys list \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group
    ```