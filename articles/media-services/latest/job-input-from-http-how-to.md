---
title: HTTPS URL에서 Azure Media Services 작업 입력 만들기 | Microsoft Docs
description: 이 항목에서는 HTTP URL에서 작업 입력을 만드는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/24/2018
ms.author: juliako
ms.openlocfilehash: 8b8bfb578b9a7190e93da721531041bb93a9a03c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224735"
---
# <a name="create-a-job-input-from-an-https-url"></a>HTTPS URL에서 작업 입력 만들기

Media Services v3에서는, 비디오를 처리할 작업을 제출할 때 입력 비디오를 찾을 위치를 Media Services에 알려줘야 합니다. 한 가지 옵션은 HTTP URL을 작업 입력으로 지정하는 것입니다(아래 예제 참조). 현재 AMS v3은 HTTPS URL을 통한 청크 분할 전송 인코딩을 지원하지 않습니다. 전체 예제를 보려면 [GitHub 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)을 참조하세요.

## <a name="net-sample"></a>.NET 샘플

다음 코드에서는 HTTPS URL 입력으로 작업을 만드는 방법을 보여줍니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>다음 단계

[로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md).
