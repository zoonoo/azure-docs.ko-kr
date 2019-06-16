---
title: 포함 파일
description: 포함 파일
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66147740"
---
### <a name="code-walkthrough"></a>코드 연습

이 섹션에서는 샘플 코드의 몇 가지 주요 부분 및 원격 모니터링 솔루션 가속기와의 관계에 대해 설명합니다.

다음 코드 조각은 디바이스의 기능을 설명하는 보고된 속성이 정의된 방식을 보여 줍니다. 이러한 속성은 다음과 같습니다.

- 솔루션 가속기가 디바이스를 맵에 추가할 수 있도록 하는 디바이스의 위치입니다.
- 현재 펌웨어 버전입니다.
- 디바이스에서 지원하는 메서드 목록입니다.
- 디바이스에서 보낸 원격 분석 메시지의 스키마입니다.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

샘플에는 Parson 라이브러리를 사용하여 이 데이터 구조를 직렬화하는 **serializeToJson** 함수가 포함되어 있습니다.

샘플에는 클라이언트가 솔루션 가속기와 상호 작용할 때 정보를 콘솔에 출력하는 몇 가지 콜백 함수가 포함되어 있습니다.

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

다음 코드 조각은 **device_method_callback** 함수를 보여 줍니다. 이 함수는 솔루션 가속기에서 메서드 호출이 수신될 때 수행할 작업을 결정합니다. 함수는 **userContextCallback** 매개 변수에서 **Chiller** 데이터 구조에 대한 참조를 받습니다. **userContextCallback**의 값은 **main** 함수에서 콜백 함수를 구성할 때 설정됩니다.

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

솔루션 가속기가 펌웨어 업데이트 메서드를 호출할 때 샘플은 JSON 페이로드를 deserialize하고 백그라운드 스레드를 시작하여 업데이트 프로세스를 완료합니다. 다음 코드 조각은 스레드에서 실행되는 **do_firmware_update**를 보여 줍니다.

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

다음 코드 조각은 클라이언트가 솔루션 가속기에 원격 분석 메시지를 보내는 방법을 보여 줍니다. 메시지 속성에는 솔루션 가속기가 대시보드에 원격 분석을 표시하는 데 도움이 되는 메시지 스키마가 포함됩니다.

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

샘플의 **main** 함수는 다음을 수행합니다.

- SDK 하위 시스템을 초기화하고 종료합니다.
- **Chiller** 데이터 구조를 초기화합니다.
- 보고된 속성을 솔루션 가속기에 보냅니다.
- 디바이스 메서드 콜백 함수를 구성합니다.
- 시뮬레이션된 원격 분석값을 솔루션 가속기에 보냅니다.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
