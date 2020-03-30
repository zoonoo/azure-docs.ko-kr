---
title: 보안 및 인증 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: IoT 에지의 이벤트 그리드에서 보안 및 인증.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5dfa17fd702b76e2cfaa7a91066dbc6749c1069e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844516"
---
# <a name="security-and-authentication"></a>보안 및 인증

보안 및 인증은 고급 개념이며 먼저 이벤트 그리드 기본 사항에 익숙해야 합니다. IoT Edge의 이벤트 그리드를 새로 접하는 경우 [여기에서 시작하십시오.](concepts.md) 이벤트 그리드 모듈은 IoT Edge의 기존 보안 인프라를 기반으로 합니다. 자세한 내용 및 설정은 [이 설명서를](../../iot-edge/security.md) 참조하십시오.

다음 섹션에서는 이러한 설정을 보호및 인증하는 방법을 자세히 설명합니다.

* TLS 구성
* 인바운드 클라이언트 인증
* 아웃바운드 서버 인증
* 아웃바운드 클라이언트 인증

>[!IMPORTANT]
>이벤트 그리드 모듈 보안 및 인증 레버리지는 IoT Edge에서 사용할 수 있는 기존 인프라를 활용합니다. IoT Edge 하위 시스템이 안전하다는 가정이 있습니다.

>[!IMPORTANT]
>이벤트 그리드 구성은 **기본적으로 안전합니다.** 다음 하위 섹션에서는 인증의 측면을 재정의하는 데 사용할 수 있는 모든 옵션과 가능한 값을 설명합니다. 변경하기 전에 영향을 이해합니다. 변경 사항이 적용되려면 이벤트 그리드 모듈을 다시 배포해야 합니다.

## <a name="tls-configuration-aka-server-authentication"></a>TLS 구성(서버 인증이라고도 합니다)

이벤트 그리드 모듈은 HTTP 및 HTTPS 엔드포인트를 모두 호스팅합니다. 모든 IoT Edge 모듈에는 IoT Edge의 보안 데몬이 서버 인증서를 할당합니다. 서버 인증서를 사용하여 엔드포인트를 보호합니다. 만료 시 모듈은 IoT Edge 보안 데몬의 새 인증서로 자동으로 새로 고쳐집니다.

기본적으로 HTTPS 통신만 허용됩니다. inbound__serverAuth__tlsPolicy 구성을 통해 이 동작을 재정의할 수 **있습니다.** 다음 표에서는 이 속성의 가능한 값을 캡처합니다.

| 가능한 값 | 설명 |
| ---------------- | ------------ |
| Strict | 기본값 HTTPS만 활성화
| 사용 | HTTP와 HTTPS 를 모두 활성화합니다.
| 사용 안 함 | HTTP만 활성화

## <a name="inbound-client-authentication"></a>인바운드 클라이언트 인증

클라이언트는 관리 및/또는 런타임 작업을 수행하는 엔터티입니다. 클라이언트는 다른 IoT 에지 모듈, 비 IoT 응용 프로그램일 수 있습니다.

Event Grid 모듈은 두 가지 유형의 클라이언트 인증을 지원합니다.

* 공유 액세스 서명(SAS) 키 기반
* 인증서 기반

기본적으로 Event Grid 모듈은 인증서 기반 인증만 허용하도록 구성됩니다. 시작 시 Event Grid 모듈은 IoT Edge 보안 데몬에서 "TrustBundle"을 검색하고 이를 사용하여 클라이언트 인증서의 유효성을 검사합니다. 이 체인으로 해결되지 않는 클라이언트 인증서는 `UnAuthorized`에서 거부됩니다.

### <a name="certificate-based-client-authentication"></a>인증서 기반 클라이언트 인증

인증서 기반 인증은 기본적으로 설정되어 있습니다. inbound__clientAuth__clientCert__enabled 속성을 통해 인증서 기반 인증을 사용하지 않도록 선택할 수 **있습니다.** 다음 표는 가능한 값을 캡처합니다.

| 가능한 값 | 설명 |
| ----------------  | ------------ |
| true | 기본값 클라이언트 인증서를 표시하려면 Event Grid 모듈에 모든 요청이 필요합니다. 또한 **inbound__clientAuth__clientCert__source**구성해야 합니다.
| false | 클라이언트가 인증서를 제시하도록 강요하지 마십시오.

다음 표는 **inbound__clientAuth__clientCert__source** 대해 가능한 값을 캡처합니다.

| 가능한 값 | 설명 |
| ---------------- | ------------ |
| IoT Edge | 기본값 IoT Edge의 트러스트번들을 사용하여 모든 클라이언트 인증서의 유효성을 검사합니다.

클라이언트가 자체 서명된 서명을 제공하는 경우 기본적으로 Event Grid 모듈은 이러한 요청을 거부합니다. inbound__clientAuth__clientCert__allowUnknownCA 속성을 통해 자체 서명된 클라이언트 인증서를 허용하도록 선택할 수 **있습니다.** 다음 표는 가능한 값을 캡처합니다.

| 가능한 값 | 설명 |
| ----------------  | ------------|
| true | 기본값 자체 서명된 인증서를 성공적으로 표시할 수 있습니다.
| false | 자체 서명된 인증서가 표시되면 요청이 실패합니다.

>[!IMPORTANT]
>프로덕션 시나리오에서 **inbound__clientAuth__clientCert__allowUnknownCA** **false로**설정할 수 있습니다.

### <a name="sas-key-based-client-authentication"></a>SAS 키 기반 클라이언트 인증

Event Grid 모듈은 인증서 기반 인증 외에도 SAS 키 기반 인증을 수행할 수도 있습니다. SAS 키는 Event Grid 모듈에 구성된 비밀과 유사하며 들어오는 모든 호출의 유효성을 검사하는 데 사용해야 합니다. 클라이언트는 HTTP 헤더 'aeg-sas-key'의 비밀을 지정해야 합니다. 요청이 일치하지 `UnAuthorized` 않으면 거부됩니다.

SAS 키 기반 인증을 제어하는 구성은 **inbound__clientAuth__sasKeys__enabled.**

| 가능한 값 | 설명  |
| ----------------  | ------------ |
| true | SAS 키 기반 인증을 허용합니다. **inbound__clientAuth__sasKeys__key1** 또는 **inbound__clientAuth__sasKeys__key2** 필요
| false | 기본값 SAS 키 기반 인증은 사용할 수 없습니다.

 **inbound__clientAuth__sasKeys__key1** 및 **inbound__clientAuth__sasKeys__key2** 들어오는 요청을 확인하기 위해 Event Grid 모듈을 구성하는 키입니다. 키 중 하나 이상을 구성해야 합니다. 요청을 하는 클라이언트는 요청 헤더 **'aeg-sas-key'의**일부로 키를 제시해야 합니다. 두 키가 모두 구성된 경우 클라이언트는 키 중 하나를 표시할 수 있습니다.

> [!NOTE]
>두 인증 방법을 모두 구성할 수 있습니다. 이러한 경우 SAS 키가 먼저 검사되고 실패한 경우에만 인증서 기반 인증이 수행됩니다. 요청이 성공하려면 인증 방법 중 하나만 성공하면 됩니다.

## <a name="outbound-client-authentication"></a>아웃바운드 클라이언트 인증

아웃바운드 컨텍스트의 클라이언트는 이벤트 그리드 모듈을 참조합니다. 수행 중인 작업은 구독자에게 이벤트를 전달하는 것입니다. 구독 모듈은 서버로 간주됩니다.

모든 IoT Edge 모듈에는 IoT Edge의 보안 데몬이 ID 인증서를 할당합니다. 나가는 호출에 ID 인증서를 사용합니다. 만료 시 모듈은 IoT Edge 보안 데몬의 새 인증서로 자동으로 새로 고쳐집니다.

아웃바운드 클라이언트 인증을 제어하는 구성은 **outbound__clientAuth__clientCert__enabled.**

| 가능한 값 | 설명 |
| ----------------  | ------------ |
| true | 기본값 인증서를 표시하려면 Event Grid 모듈의 모든 발파 요청이 필요합니다. **outbound__clientAuth__clientCert__source**구성해야 합니다.
| false | 이벤트 그리드 모듈에서 인증서를 제시할 필요가 없습니다.

인증서의 원본을 제어하는 구성은 **outbound__clientAuth__clientCert__source.**

| 가능한 값 | 설명 |
| ---------------- | ------------ |
| IoT Edge | 기본값 IoT Edge 보안 데몬으로 구성된 모듈의 ID 인증서를 사용합니다.

### <a name="outbound-server-authentication"></a>아웃바운드 서버 인증

이벤트 그리드 구독자의 대상 유형 중 하나는 "웹후크"입니다. 기본적으로 이러한 구독자에 대해 HTTPS 끝점만 허용됩니다.

웹후크 대상 정책을 제어하는 구성은 **outbound__webhook__httpsOnly.**

| 가능한 값 | 설명 |
| ----------------  | ------------ |
| true | 기본값 HTTPS 끝점을 가진 구독자만 허용합니다.
| false | HTTP 또는 HTTPS 끝점을 가진 구독자를 허용합니다.

기본적으로 Event Grid 모듈은 구독자의 서버 인증서의 유효성을 검사합니다. **outbound__webhook__skipServerCertValidation**재정의하여 유효성 검사를 건너뛸 수 있습니다. 가능한 값은 다음과 같습니다.

| 가능한 값 | 설명 |
| ----------------  | ------------ |
| true | 구독자의 서버 인증서의 유효성을 검사하지 마십시오.
| false | 기본값 구독자의 서버 인증서를 확인합니다.

구독자의 인증서가 자체 서명된 경우 기본적으로 Event Grid 모듈은 해당 구독자를 거부합니다. 자체 서명된 인증서를 허용하려면 **outbound__webhook__allowUnknownCA.** 다음 표에서는 가능한 값을 캡처합니다.

| 가능한 값 | 설명 |
| ----------------  | ------------ |
| true | 기본값 자체 서명된 인증서를 성공적으로 표시할 수 있습니다.
| false | 자체 서명된 인증서가 표시되면 요청이 실패합니다.

>[!IMPORTANT]
>프로덕션 시나리오에서 **outbound__webhook__allowUnknownCA** **false로**설정하려고 합니다.

> [!NOTE]
>IoT Edge 환경은 자체 서명된 인증서를 생성합니다. 프로덕션 워크로드에 대해 공인 CA에서 발급한 인증서를 생성하고 inbound 및 outbound 모두에서 **allowUnknownCA** 속성을 **false로**설정하는 것이 좋습니다.

## <a name="summary"></a>요약

이벤트 그리드 모듈은 **기본적으로 안전합니다.** 프로덕션 배포에 대해 이러한 기본값을 유지하는 것이 좋습니다.

구성 하는 동안 사용할 기본 원칙은 다음과 같습니다.

* 모듈에 HTTPS 요청만 허용합니다.
* 인증서 기반 클라이언트 인증만 허용합니다. 잘 알려진 CO에서 발급한 인증서만 허용합니다. 자체 서명된 인증서를 허용하지 않습니다.
* SASKey 기반 클라이언트 인증을 허용하지 않습니다.
* 항상 나가는 호출에 Event Grid 모듈의 ID 인증서를 제시합니다.
* Webhook 대상 유형에 대한 HTTPS 구독자만 허용합니다.
* 항상 Webhook 대상 유형에 대한 구독자의 서버 인증서의 유효성을 검사합니다. 잘 알려진 CO에서 발급한 인증서만 허용합니다. 자체 서명된 인증서를 허용하지 않습니다.

기본적으로 이벤트 그리드 모듈은 다음 구성으로 배포됩니다.

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
