---
title: Azure Container Instances에서 변칙 탐지기 컨테이너 실행
titleSuffix: Azure Cognitive Services
description: 변칙 탐지기 컨테이너를 Azure Container Instance에 배포 하 고 웹 브라우저에서 테스트 합니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mbullwin
ms.openlocfilehash: 3979bb82bfa055cc2a134bf3119097c452ffb855
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94364125"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Azure Container Instances에 변칙 탐지기 컨테이너 배포

Cognitive Services [변칙 탐지기](../anomaly-detector-container-howto.md) 컨테이너를 Azure [Container Instances](../../../container-instances/index.yml)에 배포 하는 방법에 대해 알아봅니다. 이 절차에서는 변칙 탐지기 리소스를 만드는 방법을 보여 줍니다. 그런 다음 연결 된 컨테이너 이미지를 풀링 하는 방법을 설명 합니다. 마지막으로 브라우저에서 두 오케스트레이션의 오케스트레이션을 실행 하는 기능을 강조 표시 합니다. 컨테이너를 사용 하면 개발자가 인프라를 관리 하지 않고 응용 프로그램 개발에 집중 하는 것으로 전환할 수 있습니다.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>다음 단계

* 컨테이너 이미지를 끌어오거나 컨테이너를 실행 하기 위한 컨테이너 [설치 및 실행](../anomaly-detector-container-configuration.md) 을 검토 합니다.
* [컨테이너 구성](../anomaly-detector-container-configuration.md)에서 구성 설정을 검토합니다.
* [변칙 탐지기 API 서비스에 대 한 자세한 정보](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)