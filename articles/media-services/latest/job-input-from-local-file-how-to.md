---
title: 로컬 파일에서 Azure Media Services 작업 입력 만들기 | Microsoft Docs
description: 이 항목에서는 로컬 파일에서 작업 입력을 만드는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 9b366cca37e562e229e7d139426fc0b24978e366
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53412966"
---
# <a name="create-a-job-input-from-a-local-file"></a>로컬 파일에서 작업 입력 만들기

Media Services v3에서는, 비디오를 처리할 작업을 제출할 때 입력 비디오를 찾을 위치를 Media Services에 알려줘야 합니다. 입력 비디오는 Media Service 자산으로 저장할 수 있으며 이 경우 파일(로컬 또는 Azure Blob Storage에 저장됨)을 기반으로 입력 자산을 만듭니다. 이 항목에서는 로컬 파일에서 작업 입력을 만드는 방법을 보여줍니다. 전체 예제를 보려면 [GitHub 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs)을 참조하세요.

## <a name="net-sample"></a>.NET 샘플

다음 코드는 입력 자산을 만들어서 작업에 대한 입력으로 사용하는 방법을 보여줍니다. CreateInputAsset 함수는 다음 작업을 수행합니다.

* 자산 만들기
* [저장소에 있는 자산 컨테이너](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)에 대해 쓰기가 가능한 [SAS URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) 가져오기
* SAS URL을 사용하여 저장소의 컨테이너에 파일 업로드

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>다음 단계

[HTTP URL에서 작업 입력 만들기](job-input-from-http-how-to.md)
