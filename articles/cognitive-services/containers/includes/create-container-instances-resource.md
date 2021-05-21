---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
description: Azure 컨테이너 인스턴스 리소스를 만드는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 24f6052c436b73d0075371fa74160d21826e2209
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97866023"
---
## <a name="create-an-azure-container-instance-resource-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Container Instance 리소스 만들기

1. Container Instances의 [만들기](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) 페이지로 이동합니다.

2. **기본 사항** 탭에서 다음 정보를 입력합니다.

    |설정|값|
    |--|--|
    |Subscription|구독을 선택합니다.|
    |Resource group|사용 가능한 리소스 그룹을 선택하거나 `cognitive-services`와 같은 새 리소스 그룹을 만듭니다.|
    |컨테이너 이름|`cognitive-container-instance`와 같은 이름을 입력합니다. 이름은 소문자여야 합니다.|
    |위치|배포할 지역을 선택합니다.|
    |이미지 형식|컨테이너 이미지가 자격 증명이 필요하지 않은 컨테이너 레지스트리에 저장된 경우 `Public`을 선택합니다. 컨테이너 이미지에 액세스하는 데 자격 증명이 필요한 경우 `Private`을 선택합니다. 컨테이너 이미지가 `Public` 또는 `Private`("공개 미리 보기")인지 여부에 대한 자세한 내용은 [컨테이너 리포지토리 및 이미지](../container-image-tags.md)를 참조하세요. |
    |이미지 이름|Cognitive Services 컨테이너 위치를 입력합니다. 위치는 `docker pull` 명령에 대한 인수로 사용됩니다. 사용 가능한 이미지 이름 및 해당 리포지토리는 [컨테이너 리포지토리 및 이미지](../container-image-tags.md)를 참조하세요.<br><br>이미지 이름은 세 부분을 지정하여 정규화되어야 합니다. 먼저 컨테이너 레지스트리, 리포지토리, 마지막으로 이미지 이름 `<container-registry>/<repository>/<image-name>`을 차례로 클릭합니다.<br><br>다음은 `mcr.microsoft.com/azure-cognitive-services/keyphrase`이 Azure Cognitive Services 리토지토리 아래의 Microsoft Container Registry에서 핵심 구 추출 이미지를 나타내는 예입니다. 또 다른 예는 컨테이너 미리 보기 컨테이너 레지스트리의 Microsoft 리포지토리에 있는 음성 텍스트 변환 이미지를 나타내는 `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`입니다. |
    |OS 유형|`Linux`|
    |크기|특정 Cognitive Service 컨테이너에 대해 제안된 권장 사항으로 크기를 변경합니다.<br>2개의 CPU 코어<br>4GB

3. **네트워킹** 탭에서 다음 세부 정보를 입력합니다.

    |설정|값|
    |--|--|
    |포트|TCP 포트를 `5000`으로 설정합니다. 컨테이너를 포트 5000에 표시합니다.|

4. **고급** 탭에서 Azure Container Instance 리소스의 컨테이너 청구 설정에 필요한 **환경 변수** 를 입력합니다.

    | 키 | 값 |
    |--|--|
    |`ApiKey`|리소스의 **키 및 엔드포인트** 페이지에서 복사되었습니다. 공백 또는 대시가 없는 32자 영숫자 문자열인 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`입니다.|
    |`Billing`| 리소스의 **키 및 엔드포인트** 페이지에서 복사한 엔드포인트 URL입니다.|
    |`Eula`|`accept`|

5. **검토 후 만들기** 를 클릭합니다.
6. 유효성 검사를 통과한 후 **만들기** 를 클릭하여 만들기 프로세스를 마칩니다.
7. 리소스가 성공적으로 배포되면 준비된 것입니다.
