---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
description: Azure 컨테이너 인스턴스 리소스를 만드는 방법에 알아봅니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 38addf4651373ba0f4df411325218a255c835508
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717169"
---
## <a name="create-an-azure-container-instance-resource"></a>Azure 컨테이너 인스턴스 리소스 만들기

1. 로 이동 합니다 [만들기](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) Container Instances에 대 한 페이지입니다.

2. 에 **기본 사항** 탭에서 다음 세부 정보를 입력 합니다.

    |설정|값|
    |--|--|
    |구독|구독을 선택합니다.|
    |리소스 그룹|사용 가능한 리소스 그룹을 선택 하거나 새로 만들 같은 `cognitive-services`합니다.|
    |컨테이너 이름|같은 이름을 입력 `cognitive-container-instance`합니다. 이름을 낮은 대문자 여야 합니다.|
    |위치|배포에 대 한 지역을 선택 합니다.|
    |이미지 형식|`Public`|
    |이미지 이름|Cognitive Services 컨테이너 위치를 입력 합니다. 위치에 사용 되는 같을 수는 `docker pull` 명령을 참조 하세요 합니다 [컨테이너 리포지토리 및 이미지](../../cognitive-services-container-support.md#container-repositories-and-images) 사용 가능한 이미지 이름에 해당 하는 리포지토리와 합니다.|
    |OS 유형|`Linux`|
    |크기|특정 Cognitive 서비스 컨테이너에 대해 제안 된 권장 크기를 변경 합니다.<br>CPU 코어가 2<br>4GB

3. 에 **네트워킹** 탭에서 다음 세부 정보를 입력 합니다.

    |설정|값|
    |--|--|
    |포트|TCP 포트를 설정 `5000`합니다. 포트 5000에서 컨테이너를 노출합니다.|

4. 에 **고급** 탭에서 필요한 입력 **환경 변수** 컨테이너용 [청구 설정](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments) ACI 리소스의:

    | Key | 값 |
    |--|--|
    |`apikey`|복사 합니다 **키** Text Analytics 리소스 페이지. 공백이 나 대시 없이 32 자리 영숫자 문자열 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`합니다.|
    |`billing`|복사 합니다 **개요** Text Analytics 리소스 페이지. 예: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. 클릭 **검토 및 만들기**
1. 유효성 검사에 통과 한 후 클릭 **만들기** 생성 프로세스를 완료 합니다.
1. 리소스가 성공적으로 배포 될 때 준비