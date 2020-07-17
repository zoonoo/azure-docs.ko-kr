---
title: Azure Media Player 개요
description: Azure Media Player에 대해 자세히 알아봅니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: b6d30aebd4de272ba98fce87f23701b129eacb02
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727560"
---
# <a name="azure-media-player-overview"></a>Azure Media Player 개요 #

Azure Media Player는 다양한 브라우저 및 디바이스의 [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/)에서 미디어 콘텐츠를 재생하기 위해 빌드된 웹 비디오 플레이어입니다. Azure Media Player는 풍부해진 적응 스트리밍 환경을 제공할 수 있는 HTML5, 미디어 원본 확장(MSE) 및 암호화된 미디어 확장(EME) 등의 업계 표준을 활용합니다.  이러한 표준을 디바이스 또는 브라우저에서 사용할 수 없는 경우, Azure Media Player는 Flash 및 Silverlight를 대체 기술로 사용합니다. 사용된 재생 기술에 관계 없이 개발자는 API에 액세스하기 위한 통합된 JavaScript 인터페이스가 있어야 합니다.  이렇게 하면 Azure Media Services에서 제공하는 콘텐츠를 추가 작업 없이 광범위한 디바이스 및 브라우저에서 재생할 수 있습니다.

Microsoft Azure Media Services에서 컨텐츠를 DASH, 부드러운 스트리밍 및 HLS 스트리밍 형식으로 제공하여 재생할 수 있습니다. Azure Media Player는 이러한 다양한 형식을 고려하여 플랫폼/브라우저 기능에 따라 최상의 링크를 자동으로 재생합니다. Microsoft Azure Media Services를 사용하면 일반적인 암호화(PlayReady 또는 Widevine) 또는 AES 128 비트 봉투 암호화로 자산을 동적으로 암호화할 수 있습니다. 적절하게 구성된 경우 Azure Media Player를 사용하여 PlayReady의 및 AES 128 비트 암호화된 콘텐츠를 암호 해독할 수 있습니다.  이를 구성하는 방법은 [보호된 콘텐츠](azure-media-player-protected-content.md) 섹션을 참조하세요.

새로운 기능을 요청하거나 아이디어 또는 피드백을 제공하려면 [Azure Media Player용 UserVoice](https://aka.ms/ampuservoice)에 제출하세요. 문제 또는 질문이 있거나 버그를 발견한 경우 ampinfo@microsoft.com에 문의하세요.

릴리스를 놓치지 않고 Azure Media Player가 제공하는 최신 버전을 최신 상태로 유지하려면 [등록](https://aka.ms/ampsignup)하세요.

> [!NOTE]
> Azure Media Player는 Azure Media Services의 미디어 스트림만 지원합니다.

## <a name="license"></a>License ##

Azure Media Player는 라이선스가 부여되었으며 Azure Media Player에 대한 Microsoft 소프트웨어 사용 약관에 설명된 조건에 따릅니다. 전체 사용 약관은 [라이선스 파일](azure-media-player-license.md)을 참조하세요. 자세한 내용은 [개인정보처리방침](https://www.microsoft.com/en-us/privacystatement/default.aspx)을 참조하세요.

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)