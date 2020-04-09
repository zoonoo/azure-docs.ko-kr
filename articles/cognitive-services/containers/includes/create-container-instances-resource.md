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
ms.openlocfilehash: 09957461fd92114d1181a570550ee1a189edd8ea
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876445"
---
## <a name="create-an-azure-container-instance-resource"></a>Azure 컨테이너 인스턴스 리소스 만들기

1. 컨테이너 인스턴스에 대한 [만들기](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) 페이지로 이동합니다.

2. 기본 탭에서 다음 세부 정보를 **입력합니다.**

    |설정|값|
    |--|--|
    |Subscription|구독을 선택합니다.|
    |Resource group|사용 가능한 리소스 그룹을 선택하거나 `cognitive-services`와 같은 새 리소스 그룹을 만듭니다.|
    |컨테이너 이름|`cognitive-container-instance`와 같은 이름을 입력합니다. 이름은 하한수여야 합니다.|
    |위치|배포할 지역을 선택합니다.|
    |이미지 형식|컨테이너 이미지가 자격 증명이 필요하지 않은 컨테이너 레지스트리에 저장된 `Public`경우 을 선택합니다. 컨테이너 이미지에 액세스하려면 자격 증명이 필요한 경우 를 선택합니다. `Private` 컨테이너 이미지인지 `Public` `Private` 여부("공개 미리 보기")에 대한 자세한 내용은 컨테이너 [리포지토리 및 이미지를](../../cognitive-services-container-support.md#container-repositories-and-images) 참조하십시오. |
    |이미지 이름|코그너티브 서비스 컨테이너 위치를 입력합니다. 위치는 `docker pull` 명령에 대한 인수로 사용되는 것입니다. 사용 가능한 이미지 이름과 해당 리포지토리에 대한 [컨테이너 리포지토리 및 이미지를](../../cognitive-services-container-support.md#container-repositories-and-images) 참조하십시오.<br><br>이미지 이름은 세 부분을 지정하여 정규화해야 합니다. 첫째, 컨테이너 레지스트리, 다음 리포지토리, 마지막으로 이미지 `<container-registry>/<repository>/<image-name>`이름:<br><br>다음은 Azure Cognitive `mcr.microsoft.com/azure-cognitive-services/keyphrase` Services 리포지토리 아래의 Microsoft 컨테이너 레지스트리의 핵심 구 추출 이미지를 나타내는 예제입니다. 또 다른 `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` 예는 컨테이너 미리 보기 컨테이너 레지스트리의 Microsoft 리포지토리에서 텍스트로 음성 변환 이미지를 나타내는 것입니다. |
    |OS 유형|`Linux`|
    |크기|특정 코그너티브 서비스 컨테이너에 대한 권장 권장 사항으로 크기를 변경합니다.<br>2 개의 CPU 코어<br>4GB

3. **네트워킹** 탭에서 다음 세부 정보를 입력합니다.

    |설정|값|
    |--|--|
    |포트|TCP 포트를 `5000`로 설정합니다. 포트 5000에 컨테이너를 노출합니다.|

4. **고급** 탭에서 Azure 컨테이너 인스턴스 리소스의 컨테이너 청구 설정에 필요한 **환경 변수를** 입력합니다.

    | 키 | 값 |
    |--|--|
    |`apikey`|리소스의 **키** 페이지에서 복사되었습니다. 공백이나 대시가없는 32 영숫자 문자 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`문자열입니다.|
    |`billing`|리소스의 **개요** 페이지에서 복사되었습니다.|
    |`eula`|`accept`|

5. **검토 및 만들기를 클릭합니다.**
6. 유효성 검사가 통과한 후 **만들기를** 클릭하여 작성 프로세스를 완료합니다.
7. 리소스가 성공적으로 배포되면
