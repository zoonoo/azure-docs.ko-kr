---
title: Media Services REST로 인코딩할 때 비디오 서브클리핑
description: 이 항목에서는 REST를 사용하여 Azure Media Services로 인코딩할 때 비디오를 서브클리핑하는 방법에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/10/2019
ms.author: inhenkel
ms.openlocfilehash: e934f4f6aa4eef545fbd5420a12a180fe3818294
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106107259"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Media Services로 인코딩할 때 비디오 서브클리핑 - REST

[작업](/rest/api/media/jobs)을 사용하여 인코딩할 때 비디오를 자르거나 서브클리핑할 수 있습니다. 이 기능은 [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 사전 설정 또는 [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) 사전 설정을 사용하여 빌드된 모든 [변환](/rest/api/media/transforms)과 함께 작동합니다. 

이 항목의 REST 예제에서는 인코딩 작업을 전송할 때 비디오를 트리밍하는 작업을 만듭니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 항목에 설명된 단계를 완료하려면 다음을 수행해야 합니다.

- [Azure Media Services 계정을 만듭니다](./account-create-how-to.md).
- [Azure Media Services REST API 호출에 대해 Postman 구성](media-rest-apis-with-postman.md)
    
    [Azure AD 토큰 가져오기](media-rest-apis-with-postman.md#get-azure-ad-token) 항목의 마지막 단계를 수행해야 합니다. 
- 변환 및 출력 자산을 만듭니다. [URL을 기반으로 원격 파일 인코딩 및 비디오 스트리밍 - REST](stream-files-tutorial-with-rest.md) 자습서에서 변환 및 출력 자산을 만드는 방법을 확인할 수 있습니다.
- [인코딩 개념](encode-concept.md) 항목을 검토합니다.

## <a name="create-a-subclipping-job"></a>서브클리핑 작업 만들기

1. 다운로드한 Postman 컬렉션에서 **변환 및 작업** -> **서브클리핑으로 작업 만들기** 를 선택합니다.
    
    **PUT** 요청은 다음과 같습니다.
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. "transformName" 환경 변수의 값을 변환 이름으로 업데이트합니다. 
1. **본문** 탭을 선택하고 "myOutputAsset"을 출력 자산 이름으로 업데이트합니다.

    ```json
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
            "Ignite-short.mp4"
          ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. **보내기** 를 누릅니다.

    만들고 제출한 작업 및 작업 상태에 대한 정보가 포함된 **응답** 이 표시됩니다. 

## <a name="next-steps"></a>다음 단계

[사용자 지정 변환으로 인코딩하는 방법](encode-custom-preset-rest-how-to.md) 
