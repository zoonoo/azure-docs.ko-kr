---
title: "옵션: LED 켜기 및 끄기 동작 변경 | Microsoft Docs"
description: "LED의 켜기 및 끄기 동작을 변경하도록 메시지를 사용자 지정합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "Raspberry Pi로 LED 제어, Raspberry Pi LED 제어, Raspberry Pi 제어 LED"
ms.assetid: 3b42a4ad-0197-42f6-8ca9-04c883e879e8
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: ffcb9214b8fa645a8a2378c5e7054b9f984addbb
ms.openlocfilehash: dd74474d315c94ae3f2d3e69f8696f9be8508499


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>LED 켜기 및 끄기 동작 변경
## <a name="what-you-will-do"></a>수행할 사항
LED의 켜기 및 끄기 동작을 변경하도록 메시지를 사용자 지정합니다. 문제가 있으면 [문제 해결 페이지](iot-hub-raspberry-pi-kit-node-troubleshooting.md)에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용
추가적인 Node.js 함수를 사용하여 LED 켜기 및 끄기 동작 변경.

## <a name="what-you-need"></a>필요한 항목
[Raspberry Pi에서 샘플 응용 프로그램을 실행하여 클라우드-장치 메시지 받기](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)를 완료해야 합니다.

## <a name="add-nodejs-functions"></a>Node.js 함수 추가
1. 다음 명령을 실행하여 Visual Studio Code에서 샘플 응용 프로그램을 엽니다.
   
   ```bash
   cd Lesson4
   code .
   ```
2. `app.js` 파일을 연 후에 다음 함수를 끝에 추가합니다.
   
   ```javascript
   function turnOnLED() {
     wpi.digitalWrite(CONFIG_PIN, 1);
   }
   
   function turnOffLED() {
     wpi.digitalWrite(CONFIG_PIN, 0);
   }
   ```
   
   ![함수가 추가된 App.js 파일](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_js.png)
3. `receiveMessageCallback` 함수의 switch-case 블록에 있는 기본 조건 앞에 다음 조건을 추가합니다.
   
   ```javascript
   case 'on':
     turnOnLED();
     break;
   case 'off':
     turnOffLED();
     break;
   ```
   
   이제 샘플 응용 프로그램이 메시지를 통해 더 많은 명령에 응답하도록 구성되었습니다. "on" 명령은 LED를 켜고 "off" 명령은 LED를 끕니다.
4. gulpfile.js file 파일을 연 다음 `sendMessage` 함수 앞에 새 함수를 추가합니다.
   
   ```javascript
   var buildCustomMessage = function (messageId) {
     if ((messageId & 1) && (messageId < MAX_MESSAGE_COUNT)) {
       return new Message(JSON.stringify({ command: 'on', messageId: messageId }));
     } else if (messageId < MAX_MESSAGE_COUNT) {
       return new Message(JSON.stringify({ command: 'off', messageId: messageId }));
     } else {
       return new Message(JSON.stringify({ command: 'stop', messageId: messageId }));
     }
   }
   ```
   
   ![함수가 추가된 Gulpfile.js 파일](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile.png)
5. `sendMessage` 함수에서 `var message = buildMessage(sentMessageCount);`를 다음 코드 조각에 있는 새 줄로 바꿉니다.
   
   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. 모든 변경 사항을 저장합니다.

### <a name="deploy-and-run-the-sample-application"></a>샘플 응용 프로그램 배포 및 실행
다음 명령을 실행하여 Pi에 샘플 응용 프로그램을 배포하고 실행합니다.

```bash
gulp deploy && gulp run
```

LED가 2초간 켜졌다가 다음 2초간 꺼지는 것을 볼 수 있습니다. 마지막 "stop" 메시지는 샘플 응용 프로그램이 실행되는 것을 막습니다.

![on 및 off 메시지가 있는 샘플 응용 프로그램](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off.png)

축하합니다. IoT Hub에서 Pi로 보내는 메시지에 대한 사용자 지정이 완료되었습니다.

### <a name="summary"></a>요약
이 선택적인 섹션은 샘플 응용 프로그램이 다른 방식으로 LED를 켜고 끄는 동작을 제어할 수 있도록 메시지를 사용자 지정하는 방법을 보여줍니다.




<!--HONumber=Nov16_HO5-->


