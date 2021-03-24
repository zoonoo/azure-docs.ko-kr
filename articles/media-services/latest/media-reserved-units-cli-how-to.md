---
title: Mru (미디어 예약 단위) CLI 크기 조정
description: 이 토픽에서는 CLI를 사용하여 Azure Media Services에서 미디어 처리 크기를 조정하는 방법을 보여줍니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 06c0c6333b84697415ef598d4c5e853d5c006f08
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870163"
---
# <a name="how-to-scale-media-reserved-units"></a>미디어 예약 단위 크기를 조정 하는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 더 빠른 인코딩에 대해 MRSs (미디어 예약 단위) 크기를 조정 하는 방법을 보여 줍니다.

> [!WARNING]
> 이 명령은 2020-05-01 버전 이상의 API를 사용 하 여 만든 Media Services 계정에 대해 더 이상 작동 하지 않습니다. 이러한 계정에 대해 미디어 예약 단위는 부하에 따라 시스템이 자동으로 확장 및 축소 되기 때문에 더 이상 필요 하지 않습니다. Azure Portal에서 Mru를 관리 하는 옵션이 표시 되지 않으면 2020-05-01 API 이상으로 만든 계정을 사용 하 고 있는 것입니다.

## <a name="prerequisites"></a>사전 요구 사항

[Media Services 계정 만들기](./create-account-howto.md)

[미디어 예약 단위](concept-media-reserved-units.md)를 이해 합니다.

## <a name="scale-media-reserved-units-with-cli"></a>CLI를 사용하여 미디어 예약 단위 크기 조정

`mru` 명령을 실행합니다.

다음 [az ams account mru](/cli/azure/ams/account/mru) 명령은 **count** 및 **type** 매개 변수를 사용하여 "amsaccount" 계정에서 미디어 예약 단위를 설정합니다.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>결제

계정에 미디어 예약 단위를 프로 비전 한 시간 (분)을 기준으로 요금이 청구 됩니다. 이는 계정에서 실행 중인 작업이 있는지 여부와 관계 없이 발생 합니다. 자세한 내용은 [Media Services 가격 책정](https://azure.microsoft.com/pricing/details/media-services/) 페이지에서 FAQ 섹션을 참조하세요.   

## <a name="next-step"></a>다음 단계

[비디오 분석](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>참고 항목

* [할당량 및 제한](limits-quotas-constraints.md)
