---
title: "Node.js를 사용하여 Azure IoT Edge 모듈 만들기 | Microsoft Docs"
description: "이 자습서에서는 최신 Azure IoT Edge NPM 패키지 및 Yeoman 생성기를 사용하여 BLE 데이터 변환기 모듈을 작성하는 방법을 소개합니다."
services: iot-hub
author: sushi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: article
ms.date: 06/28/2017
ms.author: sushi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 12e0702c31f36368fd95127abeede1a0d4ff2c87
ms.contentlocale: ko-kr
ms.lasthandoff: 07/01/2017

---
# <a name="create-an-azure-iot-edge-module-with-nodejs"></a>Node.js를 사용하여 Azure IoT Edge 모듈 만들기

이 자습서에서는 JS에서 Azure IoT Edge에 대한 모듈을 만드는 방법을 소개합니다.

이 자습서에서는 최신 Azure IoT Edge NPM 패키지를 사용하여 환경 설정 및 [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) 데이터 변환기 모듈을 작성하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 섹션에서는 IoT Edge 모듈 개발을 위한 환경을 설정합니다. *64비트 Windows* 및 *64비트 Linux(Ubuntu 14 이상)* 운영 체제 모두에 적용됩니다.

다음과 같은 소프트웨어가 필요합니다.
* [Git 클라이언트](https://git-scm.com/downloads).
* [노드 LTS](https://nodejs.org).
* `npm install -g yo`.
* `npm install -g generator-az-iot-gw-module`

## <a name="architecture"></a>아키텍처

Azure IoT Edge 플랫폼은 [Von Neumann 아키텍처](https://en.wikipedia.org/wiki/Von_Neumann_architecture)를 중요하게 채택합니다. 전체 Azure IoT Edge 아키텍처는 입력을 처리하고 출력을 생성하는 시스템이며 각 개별 모듈은 아주 작은 입력-출력 하위 시스템이기도 합니다. 이 자습서에서는 다음 두 개의 모듈을 소개합니다.

1. 시뮬레이션된 [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) 신호를 수신하여 형식이 지정된 [JSON](https://en.wikipedia.org/wiki/JSON) 메시지로 변환하는 모듈입니다.
2. 수신한 [JSON](https://en.wikipedia.org/wiki/JSON) 메시지를 인쇄하는 모듈입니다.

다음 이미지는 이 프로젝트에 대한 일반적인 종단 간 데이터 흐름을 표시합니다.

![3개의 모듈 간의 데이터 흐름](media/iot-hub-iot-edge-create-module/dataflow.png "입력: 시뮬레이션된 BLE 모듈, 프로세서: 변환기 모듈, 출력: 프린터 모듈")

## <a name="set-up-the-environment"></a>환경 설정
다음은 JS를 사용하여 첫 번째 BLE 변환기 모듈을 작성할 수 있는 환경을 신속하게 설정하는 방법입니다.

### <a name="create-module-project"></a>모듈 프로젝트 만들기
1. 명령줄 창을 열고 `yo az-iot-gw-module`을 실행합니다.
2. 화면에 나타난 단계에 따라 모듈 프로젝트의 초기화를 완료합니다.

### <a name="project-structure"></a>프로젝트 구조
JS 모듈 프로젝트는 다음 구성 요소로 구성됩니다.

`modules` - 사용자 지정된 JS 모듈 원본 파일입니다. 기본 `sensor.js` 및 `printer.js`를 사용자 고유의 모듈 파일로 바꿉니다.

`app.js` - Edge 인스턴스를 시작하기 위한 항목 파일입니다.

`gw.config.json` - Edge에서 로드할 모듈을 사용자 지정하는 구성 파일입니다.

`package.json` - 모듈 프로젝트에 대한 메타데이터 정보입니다.

`README.md` - 모듈 프로젝트에 대한 기본 설명서입니다.


### <a name="package-file"></a>패키지 파일

이 `package.json`은 이름, 버전, 항목, 스크립트, 런타임 및 개발 종속성을 포함하는 모듈 프로젝트에 필요한 모든 메타데이터 정보를 선언합니다.

다음 코드 조각은 BLE 변환기 샘플 프로젝트를 구성하는 방법을 보여 줍니다.
```json
{
  "name": "converter",
  "version": "1.0.0",
  "description": "BLE data converter sample for Azure IoT Edge.",
  "repository": {
    "type": "git",
    "url": "https://github.com/Azure-Samples/iot-edge-samples"
  },
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "author": "Microsoft Corporation",
  "license": "MIT",
  "dependencies": {
  },
  "devDependencies": {
    "azure-iot-gateway": "~1.1.3"
  }
}
```


### <a name="entry-file"></a>입력 파일
`app.js`는 에지 인스턴스를 초기화하는 방법을 정의합니다. 여기에 대해 어떠한 변경도 필요하지 않습니다.

```javascript
(function() {
  'use strict';

  const Gateway = require('azure-iot-gateway');
  let config_path = './gw.config.json';

  // node app.js
  if (process.argv.length < 2) {
    throw 'Calling pattern should be node app.js.';
  }

  const gw = new Gateway(config_path);
  gw.run();
})();
```

### <a name="interface-of-module"></a>모듈의 인터페이스
Azure IoT Edge 모듈을 입력을 수신하고, 처리하며, 출력을 생성하는 작업을 수행하는 데이터 프로세서로 간주할 수 있습니다.

입력은 하드웨어(예: 동작 탐지기)의 데이터, 다른 모듈의 메시지 또는 그밖에 무엇(예: 타이머에 의해 정기적으로 생성된 난수)이 될 수 있습니다.

출력은 입력과 유사하게, 하드웨어 동작(예: 깜박이는 LED), 다른 모듈로 보내는 메시지 또는 그밖에 무엇(예: 콘솔에 인쇄)을 트리거할 수 있습니다.

모듈은 `message` 개체를 사용하여 서로 통신합니다. `message`의 **콘텐츠**는 사용자가 선호하는 모든 종류의 데이터를 표시할 수 있는 바이트 배열입니다. **속성**은 `message`에서 사용할 수 있으며 단순히 문자열-문자열 매핑입니다. **속성**을 HTTP 요청의 헤더 또는 파일의 메타데이터로 생각할 수도 있습니다.

JS에서 Azure IoT Edge 모듈을 개발하려면 필요한 메서드 `receive()`를 구현하는 새 모듈 개체를 만들어야 합니다. 이 시점에서 선택적 `create()` 또는 `start()`나 `destroy()` 메서드를 구현하도록 선택할 수도 있습니다. 다음 코드 조각에서는 JS 모듈 개체의 스캐폴딩을 보여 줍니다.

```javascript
'use strict';

module.exports = {
  broker: null,
  configuration: null,

  create: function (broker, configuration) {
    // Default implementation.
    this.broker = broker;
    this.configuration = configuration;

    return true;
  },

  start: function () {
    // Produce
  },

  receive: function (message) {
    // Consume
  },

  destroy: function () {
  }
};
```

### <a name="converter-module"></a>변환기 모듈
| 입력                    | 프로세서                              | 출력                 | 원본 파일            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| 온도 데이터 메시지 | 새 JSON 메시지의 구문 분석 및 생성 | JSON 메시지 구조 | `converter.js` |

이 모듈은 일반적인 Azure IoT Edge 모듈입니다. 다른 모듈에서 온도 메시지를 수락하고(하드웨어 모듈 또는 이 경우 시뮬레이션된 BLE 모듈), 구조적 JSON 메시지로 온도 메시지를 정규화합니다(메시지 ID 첨부, 온도 경고를 트리거해야 하는지 여부의 속성 설정 등 포함).

```javascript
receive: function (message) {
  // Initialize the messageCount in global object at first time.
  if (!global.messageCount) {
    global.messageCount = 0;
  }

  // Read the content and properties objects from message.
  let rawContent = JSON.parse(Buffer.from(message.content).toString('utf8'));
  let rawProperties = message.properties;

  // Generate new properties object.
  let newProperties = {
    source: rawProperties.source,
    macAddress: rawProperties.macAddress,
    temperatureAlert: rawContent.temperature > 30 ? 'true' : 'false'
  };

  // Generate new content object.
  let newContent = {
    deviceId: 'Intel NUC Gateway',
    messageId: ++global.messageCount,
    temperature: rawContent.temperature
  };

  // Publish the new message to broker.
  this.broker.publish(
    {
      properties: newProperties,
      content: new Uint8Array(Buffer.from(JSON.stringify(newContent), 'utf8'))
    }
  );
},
```

### <a name="printer-module"></a>프린터 모듈
| 입력                          | 프로세서 | 출력                     | 원본 파일          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| 다른 모듈의 모든 메시지 | 해당 없음       | 콘솔에 메시지 로그 | `printer.js` |

이 모듈은 간단하고 설명이 따로 필요 없으며, 수신한 메시지(속성, 콘텐츠)를 터미널 창으로 출력합니다.

```javascript
receive: function (message) {
  let properties = JSON.stringify(message.properties);
  let content = Buffer.from(message.content).toString('utf8');

  console.log(`printer.receive.properties - ${properties}`);
  console.log(`printer.receive.content - ${content}\n`);
}
```

### <a name="configuration"></a>구성
모듈을 실행하기 전 마지막 단계는 Azure IoT Edge를 구성하고 모듈 간 연결을 설정하는 것입니다.

첫 번째로, 나중에 섹션에서 자체 `name`에 의해 참조될 수 있는 `node` 로더를 선언해야 합니다(Azure IoT Edge가 다른 언어의 로더를 지원하기 때문).

```json
"loaders": [
  {
    "type": "node",
    "name": "node"
  }
]
```

로더를 선언하고 나면, 모듈도 선언해야 합니다. 로더 선언과 마찬가지로 모듈도 `name` 특성에 의해 참조될 수 있습니다. 모듈을 선언할 때 각 모듈에 대해 사용해야 하는 로더(앞서 정의한 것 중 하나여야 함) 및 진입점(모듈의 정규화된 클래스 이름이어야 함)을 지정해야 합니다. `simulated_device` 모듈은 Azure IoT Edge 코어 런타임 패키지에 포함되는 네이티브 모듈입니다. `args`를 JSON 파일에 `null`인 경우라도 포함합니다.

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/converter.js"
      }
    },
    "args": null
  },
  {
    "name": "printer",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/printer.js"
      }
    },
    "args": null
  }
]
```

구성 마지막에 연결을 설정합니다. 각 연결은 `source` 및 `sink`로 표현됩니다. 미리 정의된 모듈을 모두 참조해야 합니다. `source` 모듈의 출력 메시지는 `sink` 모듈의 입력으로 전달됩니다.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "printer"
  }
]
```

## <a name="running-the-modules"></a>모듈 실행
1. `npm install`
2. `npm start`

응용 프로그램을 종료하려면 `<Enter>` 키를 누릅니다.

> [!IMPORTANT]
> Ctrl + C를 사용하여 IoT Edge 응용 프로그램을 종료하는 것은 권장되지 않습니다. 이 방법은 프로세스를 비정상적으로 종료할 수 있기 때문입니다.

