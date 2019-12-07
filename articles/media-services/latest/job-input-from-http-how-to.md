---
title: HTTPS URL에서 Azure Media Services 작업 입력 만들기 | Microsoft Docs
description: 이 항목에서는 HTTPS URL에서 Azure Media Services 작업 입력을 만드는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: a3d4568dd237491f28ae2880bdd78dd236870c3f
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74899819"
---
# <a name="create-a-job-input-from-an-https-url"></a>HTTPS URL에서 작업 입력 만들기

Media Services v3에서는, 비디오를 처리할 작업을 제출할 때 입력 비디오를 찾을 위치를 Media Services에 알려줘야 합니다. 옵션 중 하나는 다음 예제와 같이 HTTPS URL을 작업 입력으로 지정하는 것입니다. 현재 AMS v3은 HTTPS URL을 통한 청크 분할 전송 인코딩을 지원하지 않습니다. 전체 예제를 보려면 [GitHub 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)을 참조하세요.

> [!TIP]
> 개발을 시작 하기 전에 [Media Services V3 api를 사용 하 여 개발](media-services-apis-overview.md) (api에 액세스 하는 방법, 명명 규칙 등)을 검토 합니다.

## <a name="net-sample"></a>.NET 샘플

다음 코드에서는 HTTPS URL 입력으로 작업을 만드는 방법을 보여줍니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>작업 오류 코드

[오류 코드](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md).
