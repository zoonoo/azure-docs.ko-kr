---
title: Azure 컨테이너 인스턴스에서 Face 컨테이너 실행
titleSuffix: Azure Cognitive Services
description: Face 컨테이너를 Azure 컨테이너 인스턴스에 배포하고 웹 브라우저에서 테스트합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 4df5949e24e3fa59d37379c058a777c93dda2c44
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878370"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Azure 컨테이너 인스턴스에 Face 컨테이너 배포

Azure 컨테이너 [인스턴스에](https://docs.microsoft.com/azure/container-instances/)코그너티브 서비스 [Face](../face-how-to-install-containers.md) 컨테이너를 배포하는 방법에 대해 알아봅니다. 이 절차에서는 Azure Face 리소스를 만드는 것을 보여 줍니다. 그런 다음 연결된 컨테이너 이미지를 가져오는 것에 대해 설명합니다. 마지막으로 브라우저에서 두 오케스트레이션을 연습하는 기능을 강조 표시합니다. 컨테이너를 사용하면 개발자의 관심을 인프라 관리에서 응용 프로그램 개발에 집중하는 것으로 전환할 수 있습니다.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>프라이빗 컨테이너 레지스트리에 대한 액세스 요청

[!INCLUDE [Request access to private container registry](../../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]