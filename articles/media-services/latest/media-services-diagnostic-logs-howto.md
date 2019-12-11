---
title: Azure Monitor를 통해 Media Services 진단 로그 모니터링 Microsoft Docs
description: 이 문서에서는 Azure Monitor를 통해 진단 로그를 라우팅하고 표시 하는 방법을 보여 줍니다.
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
ms.openlocfilehash: e00da689820778a8cc7c5ce95184d4d8c79c70f2
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977712"
---
# <a name="monitor-media-services-diagnostic-logs"></a>진단 로그 Media Services 모니터링

[Azure Monitor](../../azure-monitor/overview.md) 를 사용 하면 응용 프로그램의 작동 방식을 이해 하는 데 도움이 되는 메트릭 및 진단 로그를 모니터링할 수 있습니다. 이 기능에 대 한 자세한 설명 및 Azure Media Services 메트릭과 진단 로그를 사용 하려는 이유를 보려면 [Media Services 메트릭 및 진단 로그 모니터링](media-services-metrics-diagnostic-logs.md)을 참조 하세요.

이 문서에서는 저장소 계정에 데이터를 라우팅하는 방법을 보여 주고 데이터를 확인 합니다. 

## <a name="prerequisites"></a>Előfeltételek

- [Media Services 계정을 만듭니다](create-account-cli-how-to.md).
- [모니터 Media Services 메트릭 및 진단 로그를](media-services-metrics-diagnostic-logs.md)검토 합니다.

## <a name="route-data-to-the-storage-account-using-the-portal"></a>포털을 사용 하 여 저장소 계정에 데이터 라우팅

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.
1. 에서 Media Services 계정으로 이동 하 고 **모니터**에서 **진단 설정** 을 클릭 합니다. Itt láthatja az előfizetésben lévő összes olyan erőforrást, amely monitorozási adatokat készít az Azure Monitoron keresztül. 

    ![Diagnosztikai beállítások szakasz](media/media-services-diagnostic-logs/logs01.png)

1. **진단 설정 추가**를 클릭 합니다.

   Az erőforrások diagnosztikai beállítása annak definíciója, hogy *milyen* monitorozási adatokat kell átirányítani egy adott erőforrásból, és *hová* kell kerülniük ezeknek a monitorozási adatoknak.

1. A megjelenő szakaszban adjon egy **nevet** a beállításnak, és jelölje be az **Archiválás tárfiókba** jelölőnégyzetet.

    로그를 전송 하려는 저장소 계정을 선택 하 고 **확인**을 누릅니다.
1. Jelölje be a **Napló** és a **Metrika** alatti jelölőnégyzeteket. Az erőforrás típusától függően előfordulhat, hogy csak az egyik lehetőség jelenik meg. Ezek a jelölőnégyzetek szabályozzák, hogy a napló- és metrikaadatok milyen, az erőforrástípushoz elérhető kategóriái kerülnek a kiválasztott célra, amely ebben az esetben a tárfiók.

   ![Diagnosztikai beállítások szakasz](media/media-services-diagnostic-logs/logs02.png)
1. Állítsa a **Megőrzés (nap)** csúszkát a 30 értékre. Ez a csúszka beállítja a monitorozási adatok tárfiókban való megőrzésének időtartamát napokban. Az Azure Monitor automatikusan törli a megadott számú napnál régebbi adatokat. A nulla értékű megőrzési időszak határozatlan ideig tárolja az adatokat.
1. Kattintson a **Save** (Mentés) gombra.

Az erőforrás monitorozási adatai mostantól a tárfiókba kerülnek.

## <a name="route-data-to-the-storage-account-using-the-cli"></a>CLI를 사용 하 여 저장소 계정에 데이터 라우팅

저장소 계정에서 진단 로그의 저장소를 사용 하도록 설정 하려면 다음 `az monitor diagnostic-settings` CLI 명령을 실행 합니다. 

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

Példa:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>포털을 사용 하 여 저장소 계정에서 데이터 보기

Ha követte az előző lépéseket, az adatok elkezdtek a tárfiókba érkezni.

Előfordulhat, hogy akár öt percet is várnia kell, mielőtt az esemény megjelenik a tárfiókban.

1. A portálban navigáljon a bal oldali navigációs sávon található **Tárfiókok** szakaszhoz.
1. Azonosítsa az előző szakaszban létrehozott tárfiókot, és kattintson rá.
1. **Blob**을 클릭 한 다음 **keydeliveryrequests**라는 레이블이 지정 된 컨테이너에서를 클릭 합니다. 로그를 포함 하는 컨테이너입니다. 모니터링 데이터는 리소스 ID를 기준으로 한 다음 날짜 및 시간을 기준으로 컨테이너로 분류 됩니다.
1. Az erőforrás-azonosító, dátum és idő tárolóiba kattintva keresse meg a PT1H.json fájlt. Kattintson a PT1H.json fájlra, majd a **Letöltés** gombra.

 Most megtekintheti a tárfiókban tárolt JSON-eseményt.

### <a name="examples-of-pt1hjson"></a>PT1H.JSON의 예

#### <a name="clear-key-delivery-log"></a>키 배달 로그 지우기

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

#### <a name="widevine-encrypted-key-delivery-log"></a>암호화 된 키 배달 로그

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

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="see-also"></a>Lásd még:

* [Azure Monitor 메트릭](../../azure-monitor/platform/data-platform.md)
* [진단 로그 Azure Monitor](../../azure-monitor/platform/resource-logs-overview.md)
* [Azure 리소스에서 로그 데이터를 수집 하 고 사용 하는 방법](../../azure-monitor/platform/resource-logs-overview.md)

## <a name="next-steps"></a>Következő lépések

[메트릭 모니터링](media-services-metrics-howto.md)
