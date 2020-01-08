---
title: Azure Container instances에 LUIS 컨테이너 배포
titleSuffix: Azure Cognitive Services
description: LUIS 컨테이너를 Azure Container instance에 배포 하 고 웹 브라우저에서 테스트 합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 30fd19634f6054b8b636dabcb4ef83b118554468
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689424"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>LUIS (Language Understanding) 컨테이너를 Azure Container instances에 배포

Cognitive Services [LUIS](luis-container-howto.md) 컨테이너를 Azure [container instances](https://docs.microsoft.com/azure/container-instances/)에 배포 하는 방법에 대해 알아봅니다. 이 절차에서는 변칙 탐지기 리소스를 만드는 방법을 보여 줍니다. 그런 다음 연결 된 컨테이너 이미지를 풀링 하는 방법을 설명 합니다. 마지막으로 브라우저에서 두 오케스트레이션의 오케스트레이션을 실행 하는 기능을 강조 표시 합니다. 컨테이너를 사용 하면 개발자가 인프라를 관리 하지 않고 응용 프로그램 개발에 집중 하는 것으로 전환할 수 있습니다.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기

LUIS 컨테이너에는 런타임에 가져온 `.gz` 모델 파일이 필요 합니다. 컨테이너는 컨테이너 인스턴스에서 볼륨 탑재를 통해이 모델 파일에 액세스할 수 있어야 합니다. Azure 파일 공유를 만드는 방법에 대 한 자세한 내용은 [파일 공유 만들기](../../storage/files/storage-how-to-create-file-share.md)를 참조 하세요. 나중에 필요 하므로 Azure Storage 계정 이름, 키 및 파일 공유 이름을 적어 둡니다.

### <a name="export-and-upload-packaged-luis-app"></a>패키지 된 LUIS 앱 내보내기 및 업로드

LUIS model (패키지 된 앱)을 Azure 파일 공유에 업로드 하려면 <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">먼저 <span class="docon docon-navigate-external x-hidden-focus"> </span>LUIS 포털에서 내보내야 </a>합니다. Azure Portal에서 저장소 계정 리소스의 **개요** 페이지로 이동 하 고 **파일 공유**를 선택 합니다. 최근에 만든 파일 공유 이름을 선택 하 고 **업로드** 단추를 선택 합니다.

> [!div class="mx-imgBorder"]
> 파일 공유에 업로드 ![](media/luis-how-to-deploy-to-aci/upload-file-share.png)

LUIS model 파일을 업로드 합니다.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
