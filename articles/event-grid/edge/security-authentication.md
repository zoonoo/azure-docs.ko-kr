---
title: 보안 및 인증 - Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge의 Event Grid 보안 및 인증입니다.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: cfdefe642e7e0f428680340747f825641d19b391
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110378287"
---
# <a name="security-and-authentication"></a>보안 및 인증

보안 및 인증은 고급 개념으로, 먼저 Event Grid 기본 사항에 대해 잘 알고 있어야 합니다. IoT Edge의 Event Grid를 처음 접하는 경우 [여기](concepts.md)에서 시작하세요. Event Grid 모듈은 IoT Edge의 기존 보안 인프라를 기반으로 빌드됩니다. 자세한 내용 및 설정은 [이 설명서](../../iot-edge/security.md)를 참조하세요.

다음 섹션에서는 이러한 설정을 보호하고 인증하는 방법에 대해 자세히 설명합니다.

* TLS 구성
* 인바운드 클라이언트 인증
* 아웃바운드 서버 인증
* 아웃바운드 클라이언트 인증

>[!IMPORTANT]
>Event Grid 모듈 보안 및 인증에서는 IoT Edge에서 사용할 수 있는 기존 인프라를 활용합니다. IoT Edge 하위 시스템은 보호된다고 가정합니다.

>[!IMPORTANT]
>Event Grid 구성은 **기본적으로 보호** 됩니다. 다음 하위 섹션에서는 인증 측면을 재정의하는 데 사용할 수 있는 모든 옵션 및 가능한 값에 대해 설명합니다. 변경을 수행하기 전에 영향을 이해하세요. 변경 내용을 적용하려면 Event Grid 모듈을 다시 배포해야 합니다.

## <a name="tls-configuration-aka-server-authentication"></a>TLS 구성(즉, 서버 인증)

Event Grid 모듈은 HTTP 및 HTTPS 엔드포인트를 둘 다 호스트합니다. 모든 IoT Edge 모듈에는 IoT Edge 보안 디먼을 통해 서버 인증서가 할당됩니다. 서버 인증서를 사용하여 엔드포인트를 보호합니다. 인증서가 만료되면 모듈에서는 자동으로 IoT Edge 보안 디먼의 새 인증서로 새로 고칩니다.

기본적으로 HTTPS 통신만 허용됩니다. **inbound__serverAuth__tlsPolicy** 구성을 통해 이 동작을 재정의할 수 있습니다. 다음 표에서는 이 속성의 가능한 값을 보여 줍니다.

| 가능한 값 | Description |
| ---------------- | ------------ |
| Strict | 기본값 HTTPS만 사용합니다.
| 사용 | HTTP 및 HTTPS를 둘 다 사용합니다.
| 사용 안 함 | HTTP만 사용합니다.

## <a name="inbound-client-authentication"></a>인바운드 클라이언트 인증

클라이언트는 관리 및/또는 런타임 작업을 수행하는 엔터티입니다. 클라이언트는 다른 IoT Edge 모듈일 수도 있고 비IoT 애플리케이션일 수도 있습니다.

Event Grid 모듈에서는 두 가지 유형의 클라이언트 인증을 지원합니다.

* SAS(공유 액세스 서명) 키 기반
* 인증서 기반

기본적으로 Event Grid 모듈은 인증서 기반 인증만 허용하도록 구성되어 있습니다. 시작 시 Event Grid 모듈은 IoT Edge 보안 디먼에서 “TrustBundle”을 검색하고 이를 사용하여 클라이언트 인증서의 유효성을 검사합니다. 이 체인으로 확인되지 않는 클라이언트 인증서는 `UnAuthorized`를 사용하여 거부합니다.

### <a name="certificate-based-client-authentication"></a>인증서 기반 클라이언트 인증

인증서 기반 인증은 기본적으로 설정되어 있습니다. **inbound__clientAuth__clientCert__enabled** 속성을 통해 인증서 기반 인증을 사용하지 않도록 선택할 수 있습니다. 다음 표에서는 가능한 값을 보여 줍니다.

| 가능한 값 | Description |
| ----------------  | ------------ |
| true | 기본값 Event Grid 모듈에 대한 모든 요청에서는 클라이언트 인증서를 제시해야 합니다. 또한 **inbound__clientAuth__clientCert__source** 를 구성해야 합니다.
| false | 클라이언트에서 인증서를 제시하도록 강제 적용하지 않습니다.

다음 표에서는 **inbound__clientAuth__clientCert__source** 의 가능한 값을 보여 줍니다.

| 가능한 값 | Description |
| ---------------- | ------------ |
| IoT Edge | 기본값 IoT Edge의 Trustbundle을 사용하여 모든 클라이언트 인증서의 유효성을 검사합니다.

클라이언트가 자체 서명된 인증서를 제시하는 경우 기본적으로 Event Grid 모듈은 해당 요청을 거부합니다. **inbound__clientAuth__clientCert__allowUnknownCA** 속성을 통해 자체 서명된 클라이언트 인증서를 허용하도록 선택할 수 있습니다. 다음 표에서는 가능한 값을 보여 줍니다.

| 가능한 값 | Description |
| ----------------  | ------------|
| true | 기본값 자체 서명된 인증서를 제시할 수 있도록 허용합니다.
| false | 자체 서명된 인증서를 제시하면 요청이 실패합니다.

>[!IMPORTANT]
>프로덕션 시나리오에서는 **inbound__clientAuth__clientCert__allowUnknownCA** 를 **false** 로 설정하려 할 수 있습니다.

### <a name="sas-key-based-client-authentication"></a>SAS 키 기반 클라이언트 인증

Event Grid 모듈은 인증서 기반 인증 외에도 SAS 키 기반 인증을 수행할 수 있습니다. SAS 키는 Event Grid 모듈에 구성된 비밀과 같으며 모든 수신 호출의 유효성을 검사하는 데 사용해야 합니다. 클라이언트는 HTTP 헤더 ‘aeg-sas-key’에 비밀을 지정해야 합니다. 일치하지 않으면 `UnAuthorized`를 사용하여 요청을 거부합니다.

SAS 키 기반 인증을 제어하는 구성은 **inbound__clientAuth__sasKeys__enabled** 입니다.

| 가능한 값 | Description  |
| ----------------  | ------------ |
| true | SAS 키 기반 인증을 허용합니다. **inbound__clientAuth__sasKeys__key1** 또는 **inbound__clientAuth__sasKeys__key2** 가 있어야 합니다.
| false | 기본값 SAS 키 기반 인증을 사용하지 않습니다.

 **inbound__clientAuth__sasKeys__key1** 및 **inbound__clientAuth__sasKeys__key2** 는 Event Grid 모듈에서 수신 요청에 대해 확인하도록 구성하는 키입니다. 이러한 키 중 하나 이상을 구성해야 합니다. 요청하는 클라이언트는 요청 헤더 ‘**aeg-sas-key**’의 일부로 키를 제시해야 합니다. 키가 둘 다 구성된 경우 클라이언트는 키 중 하나를 제시할 수 있습니다.

> [!NOTE]
>인증 방법을 둘 다 구성할 수도 있습니다. 이 경우 SAS 키를 먼저 확인하고 실패한 경우에만 인증서 기반 인증을 수행합니다. 요청이 성공하려면 인증 방법 중 하나만 성공해야 합니다.

## <a name="outbound-client-authentication"></a>아웃바운드 클라이언트 인증

아웃바운드 컨텍스트의 클라이언트는 Event Grid 모듈을 참조합니다. 수행 중인 작업은 구독자에게 이벤트를 전송합니다. 구독 모듈은 서버로 간주됩니다.

모든 IoT Edge 모듈에는 IoT Edge 보안 디먼을 통해 ID 인증서가 할당됩니다. ID 인증서는 발신 호출에 사용합니다. 인증서가 만료되면 모듈에서는 자동으로 IoT Edge 보안 디먼의 새 인증서로 새로 고칩니다.

아웃바운드 클라이언트 인증을 제어하는 구성은 **outbound__clientAuth__clientCert__enabled** 입니다.

| 가능한 값 | Description |
| ----------------  | ------------ |
| true | 기본값 Event Grid 모듈의 모든 발신 요청에서 인증서를 제시해야 합니다. **outbound__clientAuth__clientCert__source** 를 구성해야 합니다.
| false | Event Grid 모듈에서 인증서를 제시하지 않아도 됩니다.

인증서 원본을 제어하는 구성은 **outbound__clientAuth__clientCert__source** 입니다.

| 가능한 값 | Description |
| ---------------- | ------------ |
| IoT Edge | 기본값 IoT Edge 보안 디먼에서 구성한 모듈의 ID 인증서를 사용합니다.

### <a name="outbound-server-authentication"></a>아웃바운드 서버 인증

Event Grid 구독자의 대상 유형 중 하나는 “웹후크”입니다. 기본적으로 이 구독자에는 HTTPS 엔드포인트만 허용됩니다.

웹후크 대상 정책을 제어하는 구성은 **outbound__webhook__httpsOnly** 입니다.

| 가능한 값 | Description |
| ----------------  | ------------ |
| true | 기본값 HTTPS 엔드포인트가 있는 구독자만 허용합니다.
| false | HTTP 또는 HTTPS 엔드포인트가 있는 구독자를 허용합니다.

기본적으로 Event Grid 모듈은 구독자 서버 인증서의 유효성을 검사합니다. **outbound__webhook__skipServerCertValidation** 을 재정의하여 유효성 검사를 건너뛸 수 있습니다. 가능한 값은 다음과 같습니다.

| 가능한 값 | Description |
| ----------------  | ------------ |
| true | 구독자 서버 인증서의 유효성을 검사하지 않습니다.
| false | 기본값 구독자 서버 인증서의 유효성을 검사합니다.

구독자 인증서가 자체 서명된 경우 기본적으로 Event Grid 모듈은 해당 구독자를 거부합니다. 자체 서명된 인증서를 허용하도록 **outbound__webhook__allowUnknownCA** 를 재정의할 수 있습니다. 다음 표에서는 가능한 값을 보여 줍니다.

| 가능한 값 | Description |
| ----------------  | ------------ |
| true | 기본값 자체 서명된 인증서를 제시할 수 있도록 허용합니다.
| false | 자체 서명된 인증서를 제시하면 요청이 실패합니다.

>[!IMPORTANT]
>프로덕션 시나리오에서는 **outbound__webhook__allowUnknownCA** 를 **false** 로 설정하려 할 수 있습니다.

> [!NOTE]
>IoT Edge 환경은 자체 서명된 인증서를 생성합니다. 프로덕션 워크로드의 경우 권한 있는 CA에서 발급한 인증서를 생성하고 인바운드 및 아웃바운드 둘 다에서 **allowUnknownCA** 속성을 **false** 로 설정하는 것이 좋습니다.

## <a name="summary"></a>요약

Event Grid 모듈은 **기본적으로 보호** 됩니다. 프로덕션 배포에서는 이 기본값을 유지하는 것이 좋습니다.

구성하는 동안 사용할 기본 원칙은 다음과 같습니다.

* 모듈에는 HTTPS 요청만 허용합니다.
* 인증서 기반 클라이언트 인증만 허용합니다. 잘 알려진 CA에서 발급한 인증서만 허용합니다. 자체 서명된 인증서는 허용하지 않습니다.
* SASKey 기반 클라이언트 인증을 허용하지 않습니다.
* 발신 호출에서는 항상 Event Grid 모듈의 ID 인증서를 제시합니다.
* 웹후크 대상 유형에서는 HTTPS 구독자만 허용합니다.
* 웹후크 대상 유형에서는 항상 구독자 서버 인증서의 유효성을 검사합니다. 잘 알려진 CA에서 발급한 인증서만 허용합니다. 자체 서명된 인증서는 허용하지 않습니다.

기본적으로 Event Grid 모듈은 다음 구성을 사용하여 배포됩니다.

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
