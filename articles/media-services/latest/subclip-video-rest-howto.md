---
title: Azure 미디어 서비스 REST로 인코딩할 때 비디오 서브클립
description: 이 항목에서는 REST를 사용하여 Azure Media Services로 인코딩할 때 비디오를 서브클립하는 방법에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/10/2019
ms.author: juliako
ms.openlocfilehash: c39aded55fe36cb130459a4f6f119f872b1adbc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514326"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>미디어 서비스로 인코딩할 때 비디오 서브클립 - REST

[Job](https://docs.microsoft.com/rest/api/media/jobs)을 사용하여 인코딩할 때 비디오를 트리밍하거나 서브클립할 수 있습니다. 이 기능은 [BuiltInStandardEncoder 사전](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 설정 사전 설정 또는 [StandardEncoder 사전](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 설정 사전 설정을 사용하여 빌드된 모든 [변환에서](https://docs.microsoft.com/rest/api/media/transforms) 작동합니다. 

이 항목의 REST 예제에서는 인코딩 작업을 제출할 때 비디오를 트리밍하는 작업을 만듭니다. 

## <a name="prerequisites"></a>사전 요구 사항

이 항목에 설명된 단계를 완료하려면 다음을 수행해야 합니다.

- [Azure 미디어 서비스 계정을 만듭니다.](create-account-cli-how-to.md)
- [Azure Media Services REST API 호출에 대해 Postman 구성](media-rest-apis-with-postman.md)
    
    [Azure AD 토큰 가져오기](media-rest-apis-with-postman.md#get-azure-ad-token) 항목의 마지막 단계를 수행해야 합니다. 
- 변환 및 출력 자산을 만듭니다. [URL을 기반으로 원격 파일을 인코딩하고 비디오를 스트리밍하는](stream-files-tutorial-with-rest.md) 변환 및 출력 자산을 만드는 방법을 볼 수 있습니다- REST 자습서.
- [인코딩 개념](encoding-concept.md) 항목을 검토합니다.

## <a name="create-a-subclipping-job"></a>하위 클리핑 작업 만들기

1. 다운로드한 Postman 컬렉션에서 **변환 및 작업** -> **하위 클리핑을 사용하여 작업 만들기를**선택합니다.
    
    **PUT** 요청은 다음과 같습니다.
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. 변환 이름으로 "transformName" 환경 변수값을 업데이트합니다. 
1. **본문** 탭을 선택하고 출력 자산 이름으로 "myOutputAsset"를 업데이트합니다.

    ```
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
1. **보내기**를 누릅니다.

    생성및 제출된 작업과 작업의 상태에 대한 정보가 있는 **응답이** 표시됩니다. 

## <a name="next-steps"></a>다음 단계

[사용자 지정 변환으로 인코딩하는 방법](custom-preset-rest-howto.md) 
