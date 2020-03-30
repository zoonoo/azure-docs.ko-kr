---
title: 웹후크 가입자 인증 구성 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: 웹후크 구독자 인증 구성
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 101dcae5870322878cec48098f2efae32cc68c14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841733"
---
# <a name="configure-webhook-subscriber-authentication"></a>웹후크 구독자 인증 구성

이 가이드는 이벤트 그리드 모듈에 대한 가능한 웹후크 가입자 구성의 예를 제공합니다. 기본적으로 웹후크 구독자에게는 HTTPS 끝점만 허용됩니다. 구독자가 자체 서명된 인증서를 제공하는 경우 Event Grid 모듈이 거부됩니다.

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

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>자체 서명된 인증서로 HTTPS 가입자 허용

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
>일반적으로 자체 `outbound__webhook__allowUnknownCA` `true` 서명된 인증서를 사용할 수 있으므로 속성을 테스트 환경에서만 설정합니다. 프로덕션 워크로드의 경우 **false로**설정하는 것이 좋습니다.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>HTTPS 구독자 허용하지만 인증서 유효성 검사 건너뛰기

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
>인증해야 `true` `outbound__webhook__skipServerCertValidation` 하는 인증서를 제시하지 않을 수 있으므로 속성을 테스트 환경에서만 설정합니다. 프로덕션 워크로드의 경우 프로덕션 워크로드를 **false로** 설정하는 것이 좋습니다.

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>자체 서명된 인증서로 HTTP 와 HTTPS 모두 허용

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
>HTTP 구독자를 `false` 먼저 표시하려는 경우 속성을 `outbound__webhook__httpsOnly` 테스트 환경에서만 설정합니다. 프로덕션 워크로드의 경우 프로덕션 워크로드를 **true로** 설정하는 것이 좋습니다.
