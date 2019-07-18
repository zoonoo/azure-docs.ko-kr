---
title: 서브 클립 Azure Media Services REST API를 사용 하 여 인코딩할 때 비디오
description: 이 항목에서는 REST를 사용 하 여 Azure Media Services를 사용 하 여 인코딩할 때에 비디오를 서브 클립 방법 설명
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
ms.openlocfilehash: df8c8a4040b4aae4379b4bfe0e9a16337588dd1b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305002"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>서브 클립 Media Services-REST 사용 하 여 인코딩할 때 비디오

Trim 또는 사용 하 여 인코딩할 때에 비디오를 서브 클립 수는 [작업](https://docs.microsoft.com/rest/api/media/jobs)합니다. 이 기능을 사용 하 여 사용할 [변환](https://docs.microsoft.com/rest/api/media/transforms) 중 하나를 사용 하 여 빌드된 합니다 [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 미리 설정 또는 [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 사전 설정입니다. 

이 항목의 나머지 예제에는 인코딩 작업을 제출 하는 대로 비디오를 트리밍하는 작업을 만듭니다. 

## <a name="prerequisites"></a>필수 조건

이 항목에 설명된 단계를 완료하려면 다음을 수행해야 합니다.

- [Azure Media Services 계정 만들기](create-account-cli-how-to.md)합니다.
- [Azure Media Services REST API 호출에 대해 Postman 구성](media-rest-apis-with-postman.md)
    
    [Azure AD 토큰 가져오기](media-rest-apis-with-postman.md#get-azure-ad-token) 항목의 마지막 단계를 수행해야 합니다. 
- 변환을 만들고 출력 자산입니다. 변환 및 출력을 만드는 방법을 표시 자산에는 [URL을 기반으로 하는 원격 파일을 인코딩 및 스트리밍 비디오-REST](stream-files-tutorial-with-rest.md) 자습서입니다.
- 검토 합니다 [인코딩 개념](encoding-concept.md) 항목입니다.

## <a name="create-a-subclipping-job"></a>서브 클리핑 작업 만들기

1. 다운로드 한 Postman 컬렉션 선택 **Transform 및 job** -> **사용 하 여 하위 클리핑 작업 만들기**합니다.
    
    합니다 **배치** 요청은 다음과 같습니다.
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. 변형 이름 "transformName" 환경 변수의 값을 업데이트 합니다. 
1. 선택 된 **본문** 탭 하 고 출력을 사용 하 여 "myOutputAsset" 업데이트 자산 이름입니다.

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

    표시 된 **응답** 생성 하 고 제출 된 작업에 대 한 정보 및 작업의 상태를 사용 하 여 합니다. 

## <a name="next-steps"></a>다음 단계

[사용자 지정 변환을 사용 하 여 인코딩하는 방법](custom-preset-rest-howto.md) 
