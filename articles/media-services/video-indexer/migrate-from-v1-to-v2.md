---
title: Azure Video Indexer API v1에서 v2로 마이그레이션
titlesuffix: Azure Media Services
description: 이 항목에서는 Azure Video Indexer API v1에서 v2로 마이그레이션하는 방법을 설명합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: ad2e2dace7d94c196d46beed957b39f3953246b5
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292061"
---
# <a name="migrate-from-the-video-indexer-api-v1-to-v2"></a>Video Indexer API v1에서 v2로 마이그레이션

> [!Note]
> Video Indexer V1 API는 2018년 8월 1일부터 더 이상 사용되지 않습니다. 이제는 Video Indexer v2 API를 사용해야 합니다. <br/>Video Indexer v2 API를 사용하여 개발하려면 [여기](https://api-portal.videoindexer.ai/)의 지침을 참조하세요. 

이 문서에서는 v2에 도입된 변경 사항에 대해 설명합니다.  

## <a name="api-changes"></a>API 변경

### <a name="authorization-and-operations"></a>권한 부여 및 작업

v2 버전에서 Video Indexer는 API의 인증 및 권한 부여 모델을 변경했습니다. 다음 두 가지 API 세트가 있습니다. 

* 권한 부여 
* 작업

**권한 부여** API는 **작업** API 호출에 대한 액세스 토큰을 가져오는 데 사용됩니다. **작업** API에는 비디오 업로드, 인사이트 가져오기 및 기타 작업과 같은 모든 Video Indexer API가 포함되어 있습니다.

**권한 부여** API를 [구독](video-indexer-get-started.md)하면 v1에서와 같이 구독 키를 전달하여 액세스 토큰을 가져올 수 있습니다.

## <a name="getting-and-using-the-access-token-for-operations-apis"></a>작업 API에 대한 액세스 토큰 가져오기 및 사용

**작업** API를 호출하는 경우 구독 키는 더 이상 사용되지 않습니다. 대신 **권한 부여** API에서 가져온 액세스 토큰을 전달합니다. 

각 요청에는 호출하는 API의 액세스 수준과 일치하는 유효한 토큰이 있어야 합니다. 예를 들어 계정을 가져오는 것과 같은 사용자 작업에는 사용자 액세스 토큰이 필요합니다. 모든 비디오를 나열하는 것과 같은 계정 수준 작업에는 계정 액세스 토큰이 필요합니다. 비디오를 다시 인덱싱하는 것과 같은 비디오 작업에는 계정 액세스 토큰 또는 비디오 액세스 토큰이 필요합니다.

작업을 더 쉽게 수행하려면 **권한 부여** API > **GetAccounts**를 사용하여 먼저 사용자 토큰을 가져오지 않고 계정을 가져올 수 있습니다. 유효한 토큰이 있는 계정을 가져오도록 요청하여 추가 호출을 건너뛰고 계정 토큰을 가져올 수 있습니다.

다양한 액세스 토큰에 대한 자세한 내용은 [Azure Video Indexer API 사용](video-indexer-use-apis.md)을 참조하세요.

### <a name="locations"></a>위치

API에 대한 각 호출에는 Video Indexer 계정의 위치가 포함되어야 합니다. 위치가 없거나 잘못된 API 호출은 실패합니다.

다음 표에 설명된 값이 적용됩니다. **매개 변수 값**은 API를 사용할 때 전달하는 값입니다.

|**Name**|**매개 변수 값**|**설명**|
|---|---|---|
|평가판|체험|평가판 계정에 사용됩니다. 예: https://api.videoindexer.ai/trial/Accounts/{accountId}/Videos/{videoId}/Index?language=English|
|미국 서부|westus2|Azure 미국 서부 2 지역에 사용됩니다.  예: https://api.videoindexer.ai/westus2/Accounts/{accountId}/Videos/{videoId}/Index?language=English|
|북유럽 |northeurope|Azure 북유럽 지역에 사용됩니다. 예제: https://api.videoindexer.ai/northeurope/Accounts/{accountId}/Videos/{videoId}/Index?language=English |
|동아시아|eastasia|Azure 동아시아 지역에 사용됩니다. 예제: https://api.videoindexer.ai/eastasia/Accounts/{accountId}/Videos/{videoId}/Index?language=English|

### <a name="data-model"></a>데이터 모델

Video Indexer에는 이제 더 명확한 인사이트를 제공하는 간소화된 데이터 모델이 있습니다. v2 API에서 생성되는 출력에 대한 자세한 내용은 [v2 API에서 생성된 Video Indexer 출력 검사](video-indexer-output-json-v2.md)를 참조하세요.

### <a name="swagger"></a>Swagger

Video Indexer API 정의는 이에 따라 업데이트되었으며 [Video Indexer 개발자 포털](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token)을 통해 다운로드할 수 있습니다.


### <a name="v1-vs-v2-examples"></a>V1 및 V2 예제

새 V2 API에 포함된 세 가지 주요 매개 변수는 다음과 같습니다.

1. [LOCATION] - 위에서 설명한 대로입니다. trial, westus2, northeurope 또는 eastasia 중 하나입니다.
2. [YOUR_ACCOUNT_ID] - 계정의 GUID ID입니다. 모든 계정을 가져올 때 검색됩니다(아래 설명 참조).
3. [YOUR_VIDEO_ID] - 비디오의 ID입니다(예: "d4fa369abc"). 비디오를 업로드하거나 검색할 때 반환됩니다.

#### <a name="uploading-a-video-in-v1"></a>V1에서 비디오 업로드

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns?name=some_name&description=some_description&privacy=private&videoUrl=http://URL_TO_YOUR_VIDEO
```

#### <a name="uploading-a-video-in-v2"></a>V2에서 비디오 업로드

1. 업로드 요청에 대한 액세스 토큰을 가져옵니다.

  모든 계정과 해당 액세스 토큰을 가져올 수 있습니다.

    ```
    https://api.videoindexer.ai/auth/[LOCATION]/Accounts?generateAccessTokens=true&allowEdit=true
    ```

  또는 특정 계정 액세스 토큰을 가져올 수 있습니다.
  
  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/AccessToken?allowEdit=true
  ```
2. 비디오를 업로드합니다.

  ```
  POST https://api.videoindexer.ai/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos?name=MySample&description=MySampleDescription&videoUrl=[URL_ENCODED_VIDEO_URL]&accessToken=eyJ0eXAiOiJ...
  ```

#### <a name="getting-insights-in-v1"></a>V1에서 인사이트 가져오기

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]
```
  
#### <a name="getting-insights-in-v2"></a>V2에서 인사이트 가져오기

1. 계정 액세스 토큰을 사용하거나 비디오 수준 액세스 토큰을 가져옵니다.

  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/AccessToken
  ```
  
2. 인사이트를 가져옵니다.

  ```
  https://api.videoindexer.ai/[LOCATION]/Accounts[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

#### <a name="getting-video-processing-state-in-v1"></a>V1에서 비디오 처리 상태 가져오기

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]/State
```
  
#### <a name="getting-video-processing-state-in-v2"></a>V2에서 비디오 처리 상태 가져오기

API v2에서 처리 상태는 Video Index 가져오기 API의 일부로 반환됩니다.

1. 계정 액세스 토큰을 사용하거나 비디오 수준 액세스 토큰을 가져옵니다.

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```
  
2. 인사이트를 가져옵니다.

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

## <a name="next-steps"></a>다음 단계

[Azure Video Indexer API 사용](video-indexer-use-apis.md)

