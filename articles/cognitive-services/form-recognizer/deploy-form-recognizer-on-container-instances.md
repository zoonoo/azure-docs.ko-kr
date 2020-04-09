---
title: Azure 컨테이너 인스턴스에서 양식 인식기 컨테이너 실행
titleSuffix: Azure Cognitive Services
description: 양식 인식기 컨테이너를 Azure 컨테이너 인스턴스에 배포하고 웹 브라우저에서 테스트합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: acba4d9fab784181fda5728c30831c8c1838b91f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879533"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Azure 컨테이너 인스턴스에 양식 인식기 컨테이너 배포

Azure 컨테이너 [인스턴스에](https://docs.microsoft.com/azure/container-instances/)인지 서비스 [양식 인식기](form-recognizer-container-howto.md) 컨테이너를 배포 하는 방법에 대해 알아봅니다. 이 절차에서는 Azure 양식 인식기 리소스를 만드는 것을 보여 줍니다. 그런 다음 연결된 컨테이너 이미지를 가져오는 것에 대해 설명합니다. 마지막으로 브라우저에서 두 오케스트레이션을 연습하는 기능을 강조 표시합니다. 컨테이너를 사용하면 개발자의 관심을 인프라 관리에서 응용 프로그램 개발에 집중하는 것으로 전환할 수 있습니다.

> [!IMPORTANT]
> 양식 인식기 컨테이너는 현재 양식 인식기 API의 버전 1.0을 사용합니다. 대신 관리되는 서비스를 사용하여 최신 버전의 API에 액세스할 수 있습니다.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>컨테이너 레지스트리에 대한 액세스 요청

먼저 [인지 서비스 양식 인식기 컨테이너 액세스 요청 양식을](https://aka.ms/FormRecognizerContainerRequestAccess) 작성하여 제출하여 컨테이너에 대한 액세스를 요청해야 합니다. 이렇게 하면 컴퓨터 비전에 대한 서명도 표시됩니다. 컴퓨터 비전 요청 양식에 별도로 등록할 필요가 없습니다. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
