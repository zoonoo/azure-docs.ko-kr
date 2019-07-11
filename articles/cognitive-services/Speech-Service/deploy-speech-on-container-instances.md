---
title: Azure Container Instances를 실행 합니다.
titleSuffix: Azure Cognitive Services
description: Azure 컨테이너 인스턴스로 음성 서비스 컨테이너를 배포 하 고 웹 브라우저에서 테스트 합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 062765be22135b12abb29ff6f7ce8a772c67adae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711897"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>음성 서비스 컨테이너 Azure Container Instances에 배포

Cognitive Services를 배포 하는 방법을 알아봅니다 [Speech Service](speech-container-howto.md) Azure에 컨테이너 [Container Instances](https://docs.microsoft.com/azure/container-instances/)합니다. 이 절차는 Azure Speech Service 리소스를 만드는 방법을 보여 줍니다. 다음 연결 된 컨테이너 이미지를 풀링 하는 것이 다루겠습니다. 마지막으로 브라우저에서 두 오케스트레이션 기능을 강조 표시 합니다. 컨테이너를 사용 하 여 응용 프로그램 개발에 집중 하는 대신에 인프라를 관리에서 개발자의 주의 이동할 수 있습니다.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>컨테이너 레지스트리에 대한 액세스 요청

컨테이너에 액세스를 요청하려면 먼저 [Cognitive Services 음성 컨테이너 요청 양식](https://aka.ms/speechcontainerspreview/)을 작성하여 제출합니다. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech Service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create an Speech Service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
