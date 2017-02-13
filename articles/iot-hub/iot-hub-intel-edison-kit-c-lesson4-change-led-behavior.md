---
title: "Azure IoT 허브에서 메시지를 사용하여 LED 깜박임 동작 변경| Microsoft Docs"
description: "LED의 켜기 및 끄기 동작을 변경하도록 메시지를 사용자 지정합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino를 사용한 LED 제어"
ms.assetid: 9826c55a-0e24-4296-ae54-29b7fe66436a
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 6d1c09c8007c373ab3c50a99efb1b3f27b46d90a
ms.openlocfilehash: 858e68a3794470f51ee505710e16496109c1aa0b


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>LED 켜기 및 끄기 동작 변경
## <a name="what-you-will-do"></a>수행할 사항
LED의 켜기 및 끄기 동작을 변경하도록 메시지를 사용자 지정합니다. 문제가 있으면 [문제 해결 페이지][troubleshooting]에서 솔루션을 검색하세요.

## <a name="what-you-will-learn"></a>알아볼 내용
추가적인 함수를 사용하여 LED 켜기 및 끄기 동작 변경.

## <a name="what-you-need"></a>필요한 항목
[Intel Edison에서 샘플 응용 프로그램을 실행하여 클라우드-장치 메시지 받기][receive-cloud-to-device-messages]를 완료해야 합니다.

## <a name="add-functions-to-mainc-and-gulpfilejs"></a>main.c 및 gulpfile.js에 기능 추가
1. 다음 명령을 실행하여 Visual Studio Code에서 샘플 응용 프로그램을 엽니다.

   ```bash
   cd Lesson4
   code .
   ```
2. `main.c` 파일을 연 후, 다음 함수를 blinkLED() 함수 뒤에 추가합니다.

   ```c
   static void turnOnLED()
   {
     mraa_gpio_write(context, 1);
   }

   static void turnOffLED()
   {
     mraa_gpio_write(context, 0);
   }
   ```

   ![함수가 추가된 main.c 파일](media/iot-hub-intel-edison-lessons/lesson4/updated_app_c.png)

3. `receiveMessageCallback` 함수의 `else if` 블록 앞에 다음 조건을 추가합니다.

   ```c
   else if (0 == strcmp((const char*)value, "\"on\""))
   {
       turnOnLED();
   }
   else if (0 == strcmp((const char*)value, "\"off\""))
   {
       turnOffLED();
   }
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

   ![함수가 추가된 Gulpfile.js 파일][gulpfile]
5. `sendMessage` 함수에서 `var message = buildMessage(sentMessageCount);`를 다음 코드 조각에 있는 새 줄로 바꿉니다.

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. 모든 변경 사항을 저장합니다.

### <a name="deploy-and-run-the-sample-application"></a>샘플 응용 프로그램 배포 및 실행
다음 명령을 실행하여 Edison에서 샘플 응용 프로그램을 배포하고 실행합니다.

```bash
gulp deploy && gulp run
```

LED가&2;초간 켜졌다가 다음&2;초간 꺼지는 것을 볼 수 있습니다. 마지막 "stop" 메시지는 샘플 응용 프로그램이 실행되는 것을 막습니다.

![켜기 및 끄기][on-and-off]

축하합니다. IoT Hub에서 Edison으로 보내는 메시지에 대한 사용자 지정이 완료되었습니다.

### <a name="summary"></a>요약
이 선택적인 섹션은 샘플 응용 프로그램이 다른 방식으로 LED를 켜고 끄는 동작을 제어할 수 있도록 메시지를 사용자 지정하는 방법을 보여줍니다.

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-c-lesson4-send-cloud-to-device-messages.md
[gulpfile]: media/iot-hub-intel-edison-lessons/lesson4/updated_gulpfile_c.png
[on-and-off]: media/iot-hub-intel-edison-lessons/lesson4/gulp_on_and_off_c.png



<!--HONumber=Dec16_HO2-->


