---
title: Azure 컨테이너 인스턴스에서 변칙 탐지기 컨테이너 실행
titleSuffix: Azure Cognitive Services
description: 변칙 탐지기 컨테이너를 Azure 컨테이너 인스턴스에 배포하고 웹 브라우저에서 테스트합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 2fba0a0d64502a30b6dfbc9f4f109bca65cca8b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76716356"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Azure 컨테이너 인스턴스에 변칙 탐지기 컨테이너 배포

Azure [컨테이너 인스턴스에](https://docs.microsoft.com/azure/container-instances/)인지 서비스 [변칙 탐지기](../anomaly-detector-container-howto.md) 컨테이너를 배포 하는 방법에 대해 알아봅니다. 이 절차에서는 변칙 탐지기 리소스를 만드는 것을 보여 줍니다. 그런 다음 연결된 컨테이너 이미지를 가져오는 것에 대해 설명합니다. 마지막으로 브라우저에서 두 오케스트레이션을 연습하는 기능을 강조 표시합니다. 컨테이너를 사용하면 개발자의 관심을 인프라 관리에서 응용 프로그램 개발에 집중하는 것으로 전환할 수 있습니다.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>프라이빗 컨테이너 레지스트리에 대한 액세스 요청

먼저 [변칙 탐지기 컨테이너 요청 양식을](https://aka.ms/adcontainer) 작성하여 제출하여 컨테이너에 대한 액세스를 요청해야 합니다.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>다음 단계

* 컨테이너 이미지를 끌어당기기 위한 [컨테이너 설치 및 실행](../anomaly-detector-container-configuration.md) 검토 및 컨테이너 실행
* [컨테이너 구성](../anomaly-detector-container-configuration.md)에서 구성 설정을 검토합니다.
* [변칙 탐지기 API 서비스에 대해 자세히 알아보기](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
