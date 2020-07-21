---
title: Azure Container Instances에서 Face 컨테이너 실행
titleSuffix: Azure Cognitive Services
description: Face 컨테이너를 Azure Container Instance에 배포 하 고 웹 브라우저에서 테스트 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: 5fbe316580cfa2ca280a2587536df037146e8d02
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538125"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Azure Container Instances에 Face 컨테이너를 배포 합니다.

> [!IMPORTANT]
> 얼굴 컨테이너 사용자에 대 한 제한에 도달 했습니다. 현재 얼굴 컨테이너에 대해 새로운 응용 프로그램을 수락 하지 않습니다.

Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/)에 Cognitive Services [Face](../face-how-to-install-containers.md) 컨테이너를 배포 하는 방법을 알아봅니다. 이 절차에서는 Azure Face 리소스를 만드는 방법을 보여 줍니다. 그런 다음 연결 된 컨테이너 이미지를 풀링 하는 방법을 설명 합니다. 마지막으로 브라우저에서 두 오케스트레이션의 오케스트레이션을 실행 하는 기능을 강조 표시 합니다. 컨테이너를 사용 하면 개발자가 인프라를 관리 하지 않고 응용 프로그램 개발에 집중 하는 것으로 전환할 수 있습니다.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]