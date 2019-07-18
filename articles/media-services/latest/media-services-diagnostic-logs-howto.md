---
title: Azure Monitor를 통해 Media Services 진단 로그를 모니터링 합니다. | Microsoft Docs
description: 이 문서에서는 라우팅 및 Azure Monitor를 통해 진단 로그를 보는 방법을 보여 줍니다.
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
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 233b043ffdc295fe94ed2e3ba837d4229848df22
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795850"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Media Services에 대 한 진단 정보를 모니터링 합니다.

[Azure Monitor](../../azure-monitor/overview.md) 응용 프로그램을 수행 하는 방법을 이해할 수 있도록 진단 로그 및 메트릭 모니터링 하는 사용 하도록 설정 합니다. 에 대 한 자세한 설명은 기능 및 Azure Media Services 메트릭 및 진단 로그를 사용 하는 이유를 보려면 [Media Services 모니터링 메트릭 및 진단 로그](media-services-metrics-diagnostic-logs.md)합니다.

이 문서에서는 저장소 계정에 데이터 라우팅 및 다음 데이터를 보는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 구성 요소

- [Media Services 계정 만들기](create-account-cli-how-to.md)
- 검토 [Media Services 모니터링 메트릭 및 진단 로그](media-services-metrics-diagnostic-logs.md)합니다.

## <a name="route-data-to-the-storage-account-using-the-portal"></a>포털을 사용 하 여 저장소 계정에 대 한 경로 데이터

1. Azure Portal ( https://portal.azure.com ) 에 로그인합니다.
1. Media Services 계정으로 이동 하 고 클릭 **진단 설정** 아래에서 **모니터**합니다. 여기에 Azure Monitor를 통해 모니터링 데이터를 생성하는 구독에 있는 모든 리소스 목록이 표시됩니다. 

    ![진단 설정 섹션](media/media-services-diagnostic-logs/logs01.png)

1. 클릭 **진단 설정 추가**합니다.

   리소스 진단 설정은 특정 리소스에서 *어떤* 모니터링 데이터를 라우팅할지, 모니터링 데이터를 *어디로* 이동할지를 정의한 것입니다.

1. 표시된 섹션에서 설정에 **이름**을 지정하고 **저장소 계정에 보관** 상자를 선택합니다.

    로그 및 키를 눌러 전송 하려는 저장소 계정 선택 **확인**합니다.
1. **로그** 및 **메트릭** 아래의 상자를 모두 선택합니다. 리소스 종류에 따라 이러한 옵션 중 하나를 포함할 수 있습니다. 이 확인란에 따라 선택한 대상(이 경우, 저장소 계정)으로 보내지는 해당 리소스 종류에서 사용할 수 있는 로그 및 메트릭 데이터의 범주가 달라집니다.

   ![진단 설정 섹션](media/media-services-diagnostic-logs/logs02.png)
1. **보존 기간(일)** 슬라이더를 30으로 설정합니다. 이 슬라이더는 저장소 계정에 모니터링 데이터를 보존할 일 수를 설정합니다. Azure Monitor는 지정된 일 수보다 오래된 데이터를 자동으로 삭제합니다. 보존 기간을 0일로 설정하면 데이터를 무기한 저장합니다.
1. **Save**을 클릭합니다.

이제 리소스의 모니터링 데이터가 저장소 계정으로 이동합니다.

## <a name="route-data-to-the-storage-account-using-the-cli"></a>CLI를 사용 하 여 저장소 계정에 대 한 경로 데이터

저장소 계정에서 진단 로그의 저장소를 사용 하도록 설정 하려면 다음 실행 `az monitor diagnostic-settings` CLI 명령: 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

예를 들어:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>포털을 사용 하 여 저장소 계정에서 데이터 보기

이전 단계를 수행했다면 데이터가 저장소 계정으로 이동하기 시작했습니다.

저장소 계정에 이벤트가 표시되려면 최대 5분 동안 기다려야 할 수 있습니다.

1. 포털의 왼쪽 탐색 모음에서 찾아 **저장소 계정** 섹션으로 이동합니다.
1. 이전 섹션에서 만든 저장소 계정을 식별하고 클릭합니다.
1. 클릭할 **Blob**, 그 다음에 레이블이 지정 된 컨테이너 **insights-로그-keydeliveryrequests**합니다. 로그에 있는 컨테이너입니다. 모니터링 데이터 컨테이너에 리소스 ID 기준으로 다음에 따라 분류 되어 날짜 및 시간입니다.
1. 리소스 ID, 날짜 및 시간에 해당하는 컨테이너를 클릭하여 PT1H.json 파일로 이동합니다. PT1H.json 파일을 클릭하고 **다운로드**를 클릭합니다.

 이제 저장소 계정에 저장된 JSON 이벤트를 볼 수 있습니다.

### <a name="examples-of-pt1hjson"></a>PT1H.json의 예

#### <a name="clear-key-delivery-log"></a>지우기 키 배달 로그

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Widevine 암호화 된 키 배달 로그

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="see-also"></a>참고자료

* [Azure Monitor 메트릭](../../azure-monitor/platform/data-platform.md)
* [Azure Monitor 진단 로그](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Azure 리소스에서 로그 데이터 수집 및 소비 하는 방법](../../azure-monitor/platform/diagnostic-logs-overview.md)

## <a name="next-steps"></a>다음 단계

[메트릭 모니터링](media-services-metrics-howto.md)
