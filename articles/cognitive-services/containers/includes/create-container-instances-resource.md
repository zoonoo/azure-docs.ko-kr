---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
description: Azure container instance 리소스를 만드는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 09957461fd92114d1181a570550ee1a189edd8ea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876445"
---
## <a name="create-an-azure-container-instance-resource"></a>Azure Container Instance 리소스 만들기

1. Container Instances에 대 한 [만들기](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) 페이지로 이동 합니다.

2. **기본 사항** 탭에서 다음 세부 정보를 입력 합니다.

    |설정|Value|
    |--|--|
    |Subscription|구독을 선택합니다.|
    |Resource group|사용 가능한 리소스 그룹을 선택 하거나와 `cognitive-services`같은 새 리소스 그룹을 만듭니다.|
    |컨테이너 이름|과 `cognitive-container-instance`같은 이름을 입력 합니다. 이름은 하위 캡 이어야 합니다.|
    |위치|배포할 지역을 선택 합니다.|
    |이미지 형식|컨테이너 이미지가 자격 증명이 필요 하지 않은 컨테이너 레지스트리에 저장 되어 있는 경우를 선택 `Public`합니다. 컨테이너 이미지에 액세스 하려면 자격 증명이 필요 합니다 `Private`.를 선택 합니다. 컨테이너 이미지가 `Public` 또는 `Private` ("공개 미리 보기") 인지 여부에 대 한 자세한 내용은 [컨테이너 리포지토리 및 이미지](../../cognitive-services-container-support.md#container-repositories-and-images) 를 참조 하세요. |
    |이미지 이름|Cognitive Services 컨테이너 위치를 입력 합니다. 위치는 `docker pull` 명령에 대 한 인수로 사용 됩니다. 사용 가능한 이미지 이름 및 해당 리포지토리의 [컨테이너 리포지토리 및 이미지](../../cognitive-services-container-support.md#container-repositories-and-images) 를 참조 하세요.<br><br>이미지 이름은 세 부분을 지정 하 여 정규화 되어야 합니다. 먼저 컨테이너 레지스트리, 리포지토리, 마지막으로 이미지 이름: `<container-registry>/<repository>/<image-name>`을 차례로 클릭 합니다.<br><br>다음은 Microsoft Container Registry Azure `mcr.microsoft.com/azure-cognitive-services/keyphrase` Cognitive Services 리포지토리에서 핵심 구 추출 이미지를 나타내는 예제입니다. 또 다른 예는 `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` 컨테이너 미리 보기 컨테이너 레지스트리의 Microsoft 리포지토리에서 음성에서 텍스트 이미지를 나타내는입니다. |
    |OS 유형|`Linux`|
    |Size|특정 인지 서비스 컨테이너에 대 한 권장 권장 사항으로 크기를 변경 합니다.<br>2 CPU 코어<br>4GB

3. **네트워킹** 탭에서 다음 세부 정보를 입력 합니다.

    |설정|Value|
    |--|--|
    |포트|TCP 포트를로 `5000`설정 합니다. 포트 5000의 컨테이너를 노출 합니다.|

4. **고급** 탭에서 Azure container Instance 리소스의 컨테이너 청구 설정에 필요한 **환경 변수** 를 입력 합니다.

    | Key | 값 |
    |--|--|
    |`apikey`|리소스의 **키** 페이지에서 복사 합니다. 공백이 나 대시가 없는 32 영숫자 문자열입니다 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|리소스의 **개요** 페이지에서 복사 합니다.|
    |`eula`|`accept`|

5. **검토 및 만들기를** 클릭 합니다.
6. 유효성 검사를 통과 한 후 **만들기** 를 클릭 하 여 만들기 프로세스를 마칩니다.
7. 리소스가 성공적으로 배포 되 면 준비 된 것입니다.
