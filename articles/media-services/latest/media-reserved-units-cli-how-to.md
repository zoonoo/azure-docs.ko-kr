---
title: 미디어 예약 단위(MRU) 스케일링 CLI
description: 이 토픽에서는 CLI를 사용하여 Azure Media Services에서 미디어 처리 크기를 조정하는 방법을 보여줍니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: c5fa3aa8397ea6e13500717f035c414af8de8e3d
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121679"
---
# <a name="how-to-scale-media-reserved-units"></a>미디어 예약 단위를 스케일링하는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 더 빠른 인코딩을 위해 MRS(미디어 예약 단위)를 스케일링하는 방법을 보여 줍니다.

> [!WARNING]
> 이 명령은 2020-05-01 버전 이상의 API를 사용하여 만든 Media Services 계정에서 더 이상 작동하지 않습니다. 계정에서는 부하에 따라 시스템이 자동으로 스케일 업/다운되기 때문에 미디어 예약 단위가 더 이상 필요하지 않습니다. Azure Portal에서 MRU를 관리하는 옵션이 표시되지 않으면 2020-05-01 API 이상으로 만든 계정을 사용하고 있는 것입니다.

## <a name="prerequisites"></a>사전 요구 사항

[Media Services 계정 만들기](./account-create-how-to.md)

[미디어 예약 단위](concept-media-reserved-units.md)를 이해합니다.

## <a name="scale-media-reserved-units-with-cli"></a>CLI를 사용하여 미디어 예약 단위 크기 조정

`mru` 명령을 실행합니다.

다음 [az ams account mru](/cli/azure/ams/account/mru) 명령은 **count** 및 **type** 매개 변수를 사용하여 "amsaccount" 계정에서 미디어 예약 단위를 설정합니다.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>결제

계정에 미디어 예약 단위를 프로비저닝한 시간(분)을 기준으로 요금이 청구됩니다. 이는 계정에서 실행 중인 작업이 있는지 여부와 관계없이 발생합니다. 자세한 내용은 [Media Services 가격 책정](https://azure.microsoft.com/pricing/details/media-services/) 페이지에서 FAQ 섹션을 참조하세요.   

## <a name="next-step"></a>다음 단계

[비디오 분석](analyze-videos-tutorial.md)

## <a name="see-also"></a>참고 항목

* [할당량 및 제한](limits-quotas-constraints-reference.md)
