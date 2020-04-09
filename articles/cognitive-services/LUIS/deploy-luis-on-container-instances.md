---
title: Azure 컨테이너 인스턴스에 LUIS 컨테이너 배포
titleSuffix: Azure Cognitive Services
description: LUIS 컨테이너를 Azure 컨테이너 인스턴스에 배포하고 웹 브라우저에서 테스트합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: 08af17106846a0f5f7a0ccc2b01da1b2e15c1143
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879215"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>AZURE 컨테이너 인스턴스에 언어 이해(LUIS) 컨테이너 배포

Azure [컨테이너 인스턴스에](https://docs.microsoft.com/azure/container-instances/)인지 서비스 [LUIS](luis-container-howto.md) 컨테이너를 배포 하는 방법에 대해 알아봅니다. 이 절차에서는 변칙 탐지기 리소스를 만드는 것을 보여 줍니다. 그런 다음 연결된 컨테이너 이미지를 가져오는 것에 대해 설명합니다. 마지막으로 브라우저에서 두 오케스트레이션을 연습하는 기능을 강조 표시합니다. 컨테이너를 사용하면 개발자의 관심을 인프라 관리에서 응용 프로그램 개발에 집중하는 것으로 전환할 수 있습니다.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기

LUIS 컨테이너에는 `.gz` 런타임시 끌어온 모델 파일이 필요합니다. 컨테이너는 컨테이너 인스턴스의 볼륨 마운트를 통해 이 모델 파일에 액세스할 수 있어야 합니다. Azure 파일 공유 를 만드는 방법에 대한 자세한 내용은 [파일 공유 만들기를](../../storage/files/storage-how-to-create-file-share.md)참조하십시오. 나중에 필요할 때 Azure Storage 계정 이름, 키 및 파일 공유 이름을 기록해 둡니다.

### <a name="export-and-upload-packaged-luis-app"></a>패키지된 LUIS 앱 내보내기 및 업로드

LUIS 모델(패키지된 앱)을 Azure 파일 공유에 업로드하려면 <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">먼저 <span class="docon docon-navigate-external x-hidden-focus"> </span>LUIS 포털에서 내보내야 </a>합니다. Azure 포털에서 저장소 계정 리소스의 **개요** 페이지로 이동하여 **파일 공유를**선택합니다. 최근에 만든 파일 공유 이름을 선택한 다음 **업로드** 단추를 선택합니다.

> [!div class="mx-imgBorder"]
> ![파일 공유에 업로드](media/luis-how-to-deploy-to-aci/upload-file-share.png)

LUIS 모델 파일을 업로드합니다.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
