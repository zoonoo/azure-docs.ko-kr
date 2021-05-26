---
title: 웹후크 구독자 인증 구성 - Azure Event Grid IoT Edge | Microsoft Docs
description: 웹후크 구독자 인증 구성
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: 8855369516067c0cf05bab8be7128b628d61ce6b
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110370500"
---
# <a name="configure-webhook-subscriber-authentication"></a>웹후크 구독자 인증 구성

이 가이드에서는 Event Grid 모듈에 가능한 웹후크 구독자 구성에 대한 예제를 제공합니다. 기본적으로 웹후크 구독자의 경우 HTTPS 엔드포인트만 수락됩니다. 구독자가 자체 서명된 인증서를 제공하면 Event Grid 모듈에서 거부합니다.

## <a name="allow-only-https-subscriber"></a>HTTPS 구독자만 허용

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>자체 서명된 인증서를 사용하여 HTTPS 구독자 허용

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>일반적으로 자체 서명된 인증서를 사용할 수 있으므로 테스트 환경에서만 `outbound__webhook__allowUnknownCA` 속성을 `true`로 설정합니다. 프로덕션 워크로드의 경우 **false** 로 설정하는 것이 좋습니다.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>HTTPS 구독자를 허용하되 인증서 유효성 검사 건너뛰기

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>인증해야 하는 인증서를 제공하지 않을 수 있으므로 테스트 환경에서만 `outbound__webhook__skipServerCertValidation` 속성을 `true`로 설정합니다. 프로덕션 워크로드의 경우 **false** 로 설정하는 것이 좋습니다.

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>자체 서명된 인증서를 사용하여 HTTP 및 HTTPS 둘 다 허용

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>HTTP 구독자를 먼저 브링업할 수 있으므로 테스트 환경에서만 `outbound__webhook__httpsOnly` 속성을 `false`로 설정합니다. 프로덕션 워크로드의 경우 **true** 로 설정하는 것이 좋습니다.
