---
title: Azure Media Services REST 호출에 대한 Postman 환경 가져오기
description: 이 항목에서는 Azure Media Services REST 호출에 대한 Postman 환경 정의를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8254d121c62a20de0a1593920b7793195f8eb50e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78926705"
---
# <a name="import-the-postman-environment"></a>Postman 환경 가져오기  

이 문서에는 Media Services REST API를 호출하는 그룹화된 HTTP 요청이 포함된 [Postman 컬렉션](postman-collection.md)을 사용하는 **Postman** 환경 변수의 정의가 포함되어 있습니다. 환경 및 컬렉션 파일은 [Media Services REST API 호출에 대한 Postman 구성](media-rest-apis-with-postman.md) 자습서에서 사용됩니다.

> [!NOTE]
> `AzureADSTSEndpoint `  = 의 `https://login.microsoftonline.com/{{TenantId}}/oauth2/token`값입니다. 테 넌 트 ID를 가져오려면 포털에서 사용자 이름 위로 마우스를 가져가면 (오른쪽 위 모서리에 있음) "디렉터리: Microsoft ({{TENANTID}})에 표시 됩니다.

```
{
  "id": "2dbce3ce-74c2-2ceb-0461-c4c2323f5b09",
  "name": "AzureMedia",
  "values": [
    {
      "enabled": true,
      "key": "TenantID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AzureADSTSEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "RESTAPIEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientSecret",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AccessToken",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAssetId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAccessPolicyId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "UploadURL",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "MediaFileName",
      "value": "BigBuckBunny.mp4",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastChannelId",
      "value": "",
      "type": "text"
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_using": "Postman/5.5.0"
}
```
