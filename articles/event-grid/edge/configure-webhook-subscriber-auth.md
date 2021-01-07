---
title: Webhook 구독자 인증 구성-Azure Event Grid IoT Edge | Microsoft Docs
description: 웹후크 구독자 인증 구성
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: d78b6e80005e9533ccd5ebfaea853f35ae6004be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171655"
---
# <a name="configure-webhook-subscriber-authentication"></a>웹후크 구독자 인증 구성

이 가이드는 Event Grid 모듈에 대해 가능한 webhook 구독자 구성의 예를 제공 합니다. 기본적으로 웹 후크 구독자에 대 한 HTTPS 끝점만 수락 됩니다. 구독자가 자체 서명 된 인증서를 제공 하는 경우 Event Grid 모듈에서 거부 됩니다.

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

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>자체 서명 된 인증서를 사용 하 여 HTTPS 구독자 허용

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
>`outbound__webhook__allowUnknownCA` `true` 일반적으로 자체 서명 된 인증서를 사용 하는 것 처럼 테스트 환경 에서만 속성을로 설정 합니다. 프로덕션 워크 로드의 경우 **false**로 설정 하는 것이 좋습니다.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>HTTPS 구독자를 허용 하지만 인증서 유효성 검사를 건너뜁니다.

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
>`outbound__webhook__skipServerCertValidation` `true` 인증 해야 하는 인증서를 제시 하지 않을 수 있으므로 테스트 환경 에서만 속성을로 설정 합니다. 프로덕션 워크 로드의 경우 **false** 로 설정 하는 것이 좋습니다.

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>자체 서명 된 인증서를 사용 하 여 HTTP 및 HTTPS 모두 허용

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
>`outbound__webhook__httpsOnly` `false` HTTP 구독자를 먼저 불러올 수 있으므로 테스트 환경 에서만 속성을로 설정 합니다. 프로덕션 워크 로드의 경우 **true** 로 설정 하는 것이 좋습니다.
