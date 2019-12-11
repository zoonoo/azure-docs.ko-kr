---
title: Azure Media Player로 재생-Azure | Microsoft Docs
description: Azure Media Player은 다양 한 브라우저 및 장치에서 Microsoft Azure Media Services 미디어 콘텐츠를 재생 하기 위해 작성 된 웹 비디오 플레이어입니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: juliako
ms.openlocfilehash: a0b0a4e89242103811f20071b7235c825c9d8bd2
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967527"
---
# <a name="azure-media-player-overview"></a>Azure Media Player 개요

Azure Media Player은 다양 한 브라우저 및 장치에서 Microsoft Azure Media Services 미디어 콘텐츠를 재생 하기 위해 작성 된 웹 비디오 플레이어입니다. Azure Media Player는 HTML5, MSE (미디어 원본 확장) 및 EME (암호화 된 미디어 확장)와 같은 업계 표준을 활용 하 여 보강 적응 스트리밍 환경을 제공 합니다. 이러한 표준을 장치 또는 브라우저에서 사용할 수 없는 경우 Azure Media Player는 Flash 및 Silverlight를 대체 기술로 사용 합니다. 사용 된 재생 기술에 관계 없이 개발자는 Api에 액세스 하기 위한 통합 JavaScript 인터페이스를 사용할 수 있습니다. 따라서 Azure Media Services에서 제공 하는 콘텐츠를 추가 노력 없이 광범위 한 장치 및 브라우저에서 재생할 수 있습니다.

Microsoft Azure Media Services를 사용 하 여 콘텐츠를 재생 하는 HLS, 대시 부드러운 스트리밍 스트리밍 형식으로 콘텐츠를 제공할 수 있습니다. Azure Media Player는 이러한 다양 한 형식을 고려 하 여 플랫폼/브라우저 기능에 따라 자동으로 최상의 링크를 재생 합니다. Media Services는 PlayReady 암호화 또는 AES-128 비트 봉투 암호화를 사용 하 여 자산을 동적으로 암호화할 수도 있습니다. Azure Media Player를 사용 하면 적절 하 게 구성 된 경우 PlayReady 및 AES 128 비트의 암호화 된 콘텐츠를 해독할 수 있습니다. 

> [!NOTE]
> 암호화 된 콘텐츠를 위해 HTTPS 재생이 필요 합니다.

[무료 평가판 시작](https://azure.microsoft.com/pricing/free-trial/)

## <a name="use-azure-media-player-demo-page"></a>Azure Media Player 데모 페이지 사용

### <a name="start-using"></a>사용 시작

[Azure Media Player 데모 페이지](https://aka.ms/azuremediaplayer) 를 사용 하 여 Azure Media Services 샘플 또는 자신의 스트림을 재생할 수 있습니다.  

새 비디오를 재생 하려면 다른 URL을 붙여넣고 **업데이트**를 누릅니다.

다양 한 재생 옵션 (예: 기술, 언어 또는 암호화)을 구성 하려면 **고급 옵션**을 누릅니다.

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>비디오 스트림의 진단 모니터링

[Azure Media Player 데모 페이지](https://aka.ms/azuremediaplayer) 를 사용 하 여 비디오 스트림의 진단을 모니터링할 수 있습니다. 

![Azure Media Player 진단](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>HTML에서 Azure Media Player 설정

Azure Media Player를 쉽게 설정할 수 있습니다. Media Services 계정에서 미디어 콘텐츠의 기본 재생을 가져오는 데 몇 분 밖에 걸리지 않습니다. Azure Media Player를 설정 하 고 구성 하는 방법에 대 한 자세한 내용은 [Azure Media Player 설명서](https://aka.ms/ampdocs) 를 참조 하세요. 

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Media Player dokumentációja](https://aka.ms/ampdocs)
- [Azure Media Player 샘플](https://aka.ms/ampsamples)
