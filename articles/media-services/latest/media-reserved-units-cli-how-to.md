---
title: CLI를 사용하여 미디어 예약 단위 크기 조정 - Azure | Microsoft Docs
description: 이 토픽에서는 CLI를 사용하여 Azure Media Services에서 미디어 처리 크기를 조정하는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2b10de83e00b3668f70461f76634c560bcbea1a4
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133794"
---
# <a name="scaling-media-processing"></a>미디어 처리 크기 조정

Azure Media Services를 사용하면 MRU(미디어 예약 단위)를 관리하여 계정에서 미디어 처리의 크기를 조정할 수 있습니다. 자세한 내용은 [미디어 처리 크기 조정](../previous/media-services-scale-media-processing-overview.md)을 참조하세요. 

이 문서에서는 [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref)를 사용하여 MRU의 크기를 조정하는 방법을 보여줍니다.

> [!NOTE]
> Media Services v3 또는 Video Indexer에 의해 트리거되는 오디오 분석 및 비디오 분석 작업의 경우 10개의 S3 MRU를 사용하여 계정을 프로비전하는 것이 좋습니다. <br/>10개가 넘는 S3 MRU가 필요한 경우 [Azure Portal](https://portal.azure.com/)을 사용하여 지원 티켓을 엽니다.

## <a name="prerequisites"></a>필수 조건 

- CLI를 로컬로 설치하여 사용하기 위해 이 문서에는 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

    현재 일부 [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) 명령만 Azure Cloud Shell에서 작동합니다. CLI를 로컬로 사용하는 것이 좋습니다.

- [Media Services 계정 만들기](create-account-cli-how-to.md)

## <a name="scale-media-reserved-units-with-cli"></a>CLI를 사용하여 미디어 예약 단위 크기 조정

다음 [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) 명령은 **count** 및 **type** 매개 변수를 사용하여 "amsaccount" 계정에서 미디어 예약 단위를 설정합니다.

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>결제

계정에서 MRU를 프로비전하는 개수, 형식 및 시간에 따라 요금이 청구됩니다. 작업을 실행하는지 여부와 상관없이 요금이 부과됩니다. 자세한 내용은 [Media Services 가격 책정](https://azure.microsoft.com/pricing/details/media-services/) 페이지에서 FAQ 섹션을 참조하세요.   

## <a name="next-step"></a>다음 단계

[비디오 분석](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>참고 항목

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
