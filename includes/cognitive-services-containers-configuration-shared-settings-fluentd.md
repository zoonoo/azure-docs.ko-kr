---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: ffd17f7a641e1481aa4c88f8b2eb12ec11fa7d8b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116702"
---
Fluentd는 통합된 로깅에 대한 오픈 소스 데이터 수집기입니다. `Fluentd` 설정은 [Fluentd](https://www.fluentd.org) 서버에 컨테이너의 연결을 관리합니다. 컨테이너에는 Fluentd 로깅 공급자가 포함되어 있어 컨테이너에서 로그 및 메트릭 데이터(선택 사항)를 Fluentd 서버에 쓸 수 있습니다.

다음 표에서는 `Fluentd` 섹션에서 지원되는 구성 설정을 설명합니다.

| 이름 | 데이터 형식 | 설명 |
|------|-----------|-------------|
| `Host` | 문자열 | Fluentd 서버의 IP 주소 또는 DNS 호스트 이름입니다. |
| `Port` | 정수 | Fluentd 서버의 포트입니다.<br/> 기본값은 24224입니다. |
| `HeartbeatMs` | 정수 | 하트비트 간격(밀리초)입니다. 이 간격이 만료되기 전에 전송된 이벤트 트래픽이 없을 경우 하트 비트는 Fluentd 서버로 전송됩니다. 기본값은 60000밀리초(1분)입니다. |
| `SendBufferSize` | 정수 | 전송 작업에 할당된 네트워크 버퍼 공간(바이트)입니다. 기본값은 32768바이트(32킬로바이트)입니다. |
| `TlsConnectionEstablishmentTimeoutMs` | 정수 | Fluentd 서버와 SSL/TLS 연결을 설정하기 위한 시간 제한(밀리초)입니다. 기본값은 10000밀리초(10초)입니다.<br/> `UseTLS`가 false로 설정되어 있는 경우 이 값은 무시됩니다. |
| `UseTLS` | 부울 | 컨테이너가 Fluentd 서버와 통신하기 위해 SSL/TLS를 사용해야 하는지 여부를 나타냅니다. 기본값은 False입니다. |