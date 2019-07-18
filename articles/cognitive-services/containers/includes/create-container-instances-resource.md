---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
description: Azure container instance 리소스를 만드는 방법에 대해 알아봅니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 05284d434e6bd22fd50957f7cc5ec966f88a4fd4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229231"
---
## <a name="create-an-azure-container-instance-resource"></a>Azure Container Instance 리소스 만들기

1. Container Instances에 대 한 [만들기](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) 페이지로 이동 합니다.

2. **기본 사항** 탭에서 다음 세부 정보를 입력 합니다.

    |설정|값|
    |--|--|
    |구독|구독을 선택합니다.|
    |리소스 그룹|사용 가능한 리소스 그룹을 선택 하거나와 `cognitive-services`같은 새 리소스 그룹을 만듭니다.|
    |컨테이너 이름|과 `cognitive-container-instance`같은 이름을 입력 합니다. 이름은 하위 캡 이어야 합니다.|
    |위치|배포할 지역을 선택 합니다.|
    |이미지 형식|`Public`|
    |이미지 이름|Cognitive Services 컨테이너 위치를 입력 합니다. 위치는 `docker pull` 명령에 사용 될 수 있습니다. [컨테이너 리포지토리 및 이미지](../../cognitive-services-container-support.md#container-repositories-and-images) 는 사용 가능한 이미지 이름과 해당 리포지토리의 이미지를 참조 하세요.|
    |OS 유형|`Linux`|
    |크기|특정 인지 서비스 컨테이너에 대 한 권장 권장 사항으로 크기를 변경 합니다.<br>2 CPU 코어<br>4GB

3. **네트워킹** 탭에서 다음 세부 정보를 입력 합니다.

    |설정|값|
    |--|--|
    |포트|TCP 포트를로 `5000`설정 합니다. 포트 5000의 컨테이너를 노출 합니다.|

4. **고급** 탭에서 ACI 리소스의 컨테이너 청구 설정에 필요한 **환경 변수** 를 입력 합니다.

    | Key | 값 |
    |--|--|
    |`apikey`|리소스의 **키** 페이지에서 복사 합니다. 공백이 나 대시가 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`없는 32 영숫자 문자열입니다.|
    |`billing`|리소스의 **개요** 페이지에서 복사 합니다.|
    |`eula`|`accept`|

1. **검토 및 만들기를** 클릭 합니다.
1. 유효성 검사를 통과 한 후 **만들기** 를 클릭 하 여 만들기 프로세스를 마칩니다.
1. 리소스가 성공적으로 배포 되 면 준비 된 것입니다.