---
title: Azure Monitor를 통해 진단 로그 모니터링
description: 이 문서에서는 Azure Monitor를 통해 진단 로그를 라우팅하고 보는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: b21d7a5eef36a4aa2deac4d1005a7b82ab06687b
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108138934"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Media Services 진단 로그 모니터링

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

[Azure Monitor](../../../azure-monitor/overview.md)를 사용하면 애플리케이션의 성능을 파악하는 데 도움이 되는 메트릭 및 진단 로그를 모니터링할 수 있습니다. 이 기능에 대한 자세한 설명을 보고 Azure Media Services 메트릭 및 진단 로그를 사용하는 이유를 알아보려면 [Media Services 메트릭 및 진단 로그 모니터링](monitor-media-services.md)을 참조하세요.

이 문서에서는 데이터를 스토리지 계정으로 라우팅한 다음, 데이터를 보는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Media Services 계정 만들기](../account-create-how-to.md)
- [Media Services 모니터링](monitor-media-services.md)을 검토합니다.

## <a name="route-data-to-the-storage-account-using-the-portal"></a>포털을 사용하여 스토리지 계정에 데이터 라우팅

1. Azure Portal ( https://portal.azure.com ) 에 로그인합니다.
1. Media Services 계정으로 이동하고 **모니터** 에서 **진단 설정** 을 클릭합니다. 여기에 Azure Monitor를 통해 모니터링 데이터를 생성하는 구독에 있는 모든 리소스 목록이 표시됩니다.

    ![모니터링 섹션의 진단 설정이 강조 표시된 스크린샷.](../media/media-services-diagnostic-logs/logs01.png)

1. **진단 설정 추가** 를 클릭합니다.

   리소스 진단 설정은 특정 리소스에서 *어떤* 모니터링 데이터를 라우팅할지, 모니터링 데이터를 *어디로* 이동할지를 정의한 것입니다.

1. 표시된 섹션에서 설정에 **이름** 을 지정하고 **스토리지 계정에 보관** 상자를 선택합니다.

    로그를 보낼 스토리지 계정을 선택하고 **확인** 을 누릅니다.
1. **로그** 및 **메트릭** 아래의 상자를 모두 선택합니다. 리소스 종류에 따라 이러한 옵션 중 하나를 포함할 수 있습니다. 이 확인란에 따라 선택한 대상(이 경우, 스토리지 계정)으로 보내지는 해당 리소스 종류에서 사용할 수 있는 로그 및 메트릭 데이터의 범주가 달라집니다.

   ![진단 설정 섹션](../media/media-services-diagnostic-logs/logs02.png)
1. **보존 기간(일)** 슬라이더를 30으로 설정합니다. 이 슬라이더는 스토리지 계정에 모니터링 데이터를 보존할 일 수를 설정합니다. Azure Monitor는 지정된 일 수보다 오래된 데이터를 자동으로 삭제합니다. 보존 기간을 0일로 설정하면 데이터를 무기한 저장합니다.
1. **저장** 을 클릭합니다.

이제 리소스의 모니터링 데이터가 스토리지 계정으로 이동합니다.

## <a name="route-data-to-the-storage-account-using-the-azure-cli"></a>Azure CLI를 사용하여 스토리지 계정에 데이터 라우팅

스토리지 계정에서 진단 로그의 스토리지를 사용하려면 다음 `az monitor diagnostic-settings` Azure CLI 명령을 실행합니다.

```azurecli-interactive
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

```azurecli-interactive
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>포털을 사용하여 스토리지 계정의 데이터 보기

이전 단계를 수행했다면 데이터가 스토리지 계정으로 이동하기 시작했습니다.

스토리지 계정에 이벤트가 표시되려면 최대 5분 동안 기다려야 할 수 있습니다.

1. 포털의 왼쪽 탐색 모음에서 찾아 **스토리지 계정** 섹션으로 이동합니다.
1. 이전 섹션에서 만든 스토리지 계정을 식별하고 클릭합니다.
1. **Blob** 을 클릭한 다음, **insights-logs-keydeliveryrequests** 로 표시된 컨테이너를 클릭합니다. 로그가 들어 있는 컨테이너입니다. 모니터링 데이터는 리소스 ID별로 컨테이너로 분류된 후 날짜 및 시간별로 분류됩니다.
1. 리소스 ID, 날짜 및 시간에 해당하는 컨테이너를 클릭하여 PT1H.json 파일로 이동합니다. PT1H.json 파일을 클릭하고 **다운로드** 를 클릭합니다.

 이제 스토리지 계정에 저장된 JSON 이벤트를 볼 수 있습니다.

### <a name="examples-of-pt1hjson"></a>PT1H.json의 예

#### <a name="clear-key-delivery-log"></a>키 전달 로그 지우기

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

#### <a name="widevine-encrypted-key-delivery-log"></a>Widevine으로 암호화된 키 전달 로그

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

## <a name="additional-notes"></a>추가적인 참고 사항

* Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="see-also"></a>추가 정보

* [Azure Monitor 메트릭](../../../azure-monitor/data-platform.md)
* [Azure Monitor 진단 로그](../../../azure-monitor/essentials/platform-logs-overview.md)
* [Azure 리소스에서 로그 데이터를 수집하고 소비하는 방법](../../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="next-steps"></a>다음 단계

[메트릭 모니터링](media-services-metrics-howto.md)