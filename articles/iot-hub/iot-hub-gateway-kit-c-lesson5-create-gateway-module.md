---
title: "첫 번째 Azure IoT 게이트웨이 모듈 만들기 | Microsoft Docs"
description: "모듈을 만들고 샘플 앱에 추가하여 모듈 동작을 사용자 지정합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: cd7660f4-7b8b-4091-8d71-bb8723165b0b
ms.service: iot-hub
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/17/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 027597c73d7282ef929363bab904e7d3d423d3d4
ms.openlocfilehash: db2757015cf8e821cffcfe39bcbd1559c23fc7ed
ms.lasthandoff: 02/23/2017


---
# <a name="lesson-5-create-your-first-azure-iot-gateway-module"></a>5단원: 첫 번째 Azure IoT 게이트웨이 모듈 만들기
게이트웨이 SDK를 통해 Java,.NET 또는 Node.js로 작성된 모듈을 빌드할 수 있지만 이 자습서는 C에서 모듈을 구축하기 위한 단계를 안내합니다.

## <a name="what-you-will-do"></a>수행할 사항

- Intel NUC에서 hello_world 샘플 앱을 컴파일하고 실행합니다.
- 모듈을 만들고 Intel NUC에서 컴파일합니다.
- 새 모듈을 hello_world 샘플 앱에 추가하고 Intel NUC에서 샘플을 실행합니다. 새 모듈은 타임스탬프가 있는 "hello_world" 메시지를 출력합니다.

## <a name="what-you-will-learn"></a>알아볼 내용

- Intel NUC에서 샘플 앱을 컴파일하고 실행하는 방법
- 모듈을 만드는 방법
- 샘플 앱에 모듈을 추가하는 방법

## <a name="what-you-need"></a>필요한 항목

호스트 컴퓨터에 설치된 Azure IoT 게이트웨이 SDK

## <a name="folder-structure"></a>폴더 구조

1단원에서 복제한 샘플 코드의 5단원 하위 폴더에는 `module` 폴더와 `sample` 폴더가 있습니다.

![my_module](media/iot-hub-gateway-kit-lessons/lesson5/my_module.png)

- `module/my_module` 폴더는 모듈을 빌드하기 위한 소스 코드 및 스크립트를 포함합니다.
- `sample` 폴더는 샘플 앱을 빌드하기 위한 소스 코드 및 스크립트를 포함합니다.

## <a name="compile-and-run-the-helloworld-sample-app-on-intel-nuc"></a>Intel NUC에서 hello_world 샘플 앱 컴파일 및 실행

`hello_world` 샘플은 앱과 연결된 미리 정의된&2;개의 모듈을 지정하는 `hello_world.json` 파일을 기준으로 게이트웨이를 만듭니다. 이 게이트웨이는 "hello world" 메시지를 5초마다 파일에 로깅합니다. 이 섹션에서는 기본 모듈을 사용하여 `hello_world` 앱을 컴파일하고 실행합니다.

`hello_world` 앱을 컴파일 및 실행하려면 호스트 컴퓨터에서 다음 단계를 수행합니다.

1. 다음 명령을 실행하여 구성 파일을 초기화합니다.

   ```bash
   cd iot-hub-c-intel-nuc-gateway-getting-started
   cd Lesson5
   npm install
   gulp init
   ```

1. Intel NUC의 MAC 주소로 게이트웨이 구성 파일을 업데이트합니다. [BLE 샘플 응용 프로그램 구성 및 실행][config_ble] 단계를 마친 경우 이 단계를 건너뛰세요.

   1. 다음 명령을 실행하여 게이트웨이 구성 파일을 엽니다.

      ```bash
      # For Windows command prompt
      code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json

      # For MacOS or Ubuntu
      code ~/.iot-hub-getting-started/config-gateway.json
      ```

   1. [Intel NUC를 IoT 게이트웨이로 설정][setup_nuc]하면 게이트웨이의 MAC 주소를 업데이트한 후 파일을 저장합니다.

1. 다음 명령을 실행하여 샘플 소스 코드를 컴파일합니다.

   ```bash
   gulp compile
   ```

   이 명령은 샘플 소스 코드를 Intel NUC로 전송하고 `build.sh`를 실행하여 컴파일합니다.

1. 다음 명령을 실행하여 Intel NUC에서 `hello_world` 앱을 실행합니다.

   ```bash
   gulp run
   ```

   이 명령은 `config.json`에 지정된 `../Tools/run-hello-world.js`를 실행하여 Intel NUC에서 샘플 앱을 시작합니다.

   ![run_sample](media/iot-hub-gateway-kit-lessons/lesson5/run_sample.png)

## <a name="create-a-new-module-and-compile-it-on-intel-nuc"></a>새 모듈을 만들고 Intel NUC에서 컴파일

아래 단계는 새 모듈을 만들고 Intel NUC에서 컴파일하는 과정을 안내합니다. 모듈은 메시지가 발생하면 타임스탬프와 함께 메시지를 출력합니다. 이 섹션에서는 첫 번째 사용자 지정된 게이트웨이 모듈을 만들 것입니다.

Azure IoT 게이트웨이 SDK 모듈은 다음 인터페이스를 구현해야 합니다.

   ```C
   pfModule_ParseConfigurationFromJson Module_ParseConfigurationFromJson
   pfModule_FreeConfiguration Module_FreeConfiguration
   pfModule_Create Module_Create
   pfModule_Destroy Module_Destroy
   pfModule_Receive Module_Receive
   ```

필요에 따라 다음 인터페이스를 구현할 수 있습니다.

   ```C
   pfModule_Start Module_Start
   ```

다음 다이어그램에서는 모듈의 중요한 상태 경로를 보여 줍니다. 정사각형은 모듈이 상태 간을 전환할 때 작업을 수행하기 위해 구현하는 메서드를 나타냅니다. 타원은 모듈이 있을 수 있는 주요 상태입니다.

![state_path](media/iot-hub-gateway-kit-lessons/lesson5/state_path.png)

이제 템플릿을 기반으로 모듈을 만들어 보겠습니다.

1. 다음 명령을 실행하여 템플릿 폴더를 엽니다.

   ```bash
   code module/my_module
   ```

   ![code_module](media/iot-hub-gateway-kit-lessons/lesson5/code_module.png)

   - `src/my_module.c`는 모듈을 간편하게 만들 수 있는 템플릿으로 사용됩니다. 이 템플릿은 인터페이스를 선언합니다. `MyModule_Receive` 함수에 논리를 추가하기만 하면 됩니다.
   - `build.sh`는 Intel NUC에서 모듈을 컴파일하기 위한 빌드 스크립트입니다.
1. `src/my_module.c` 파일을 열고 다음 두 개의 헤더 파일을 포함합니다.

   ```C
   #include <stdio.h>
   #include "azure_c_shared_utility/xlogging.h"
   ```

1. `MyModule_Receive` 함수에 다음 코드를 추가합니다.

   ```C
   if (message == NULL)
   {
      LogError("invalid arg message");
   }
   else
   {
      // get the message content
      const CONSTBUFFER * content = Message_GetContent(message);
      // get the local time and format it
      time_t temp = time(NULL);
      if (temp == (time_t)-1)
      {
          LogError("time function failed");
      }
      else
      {
          struct tm* t = localtime(&temp);
          if (t == NULL)
          {
              LogError("localtime failed");
          }
          else
          {
              char timetemp[80] = { 0 };
              if (strftime(timetemp, sizeof(timetemp) / sizeof(timetemp[0]), "%c", t) == 0)
              {
                  LogError("unable to strftime");
              }
              else
              {
                  printf("[%s] %.*s\r\n", timetemp, (int)content->size, content->buffer);
              }
          }
      }
   }
   ```

1. `config.json` 파일을 업데이트하여 호스트 컴퓨터의 `workspace` 폴더와 Intel NUC의 배포 경로를 지정합니다. 컴파일하는 동안 `workspace` 폴더의 파일은 배포 경로로 전송됩니다.

   1. 다음 명령을 실행하여 `config.json` 파일을 엽니다.

      ```bash
      code config.json
      ```

   1. `config.json`을 다음 구성으로 업데이트합니다.

      ```json
      "workspace": "./module/my_module",
      "deploy_path": "module/my_module"
      ```

      ![config_json](media/iot-hub-gateway-kit-lessons/lesson5/config_json.png)

1. 다음 명령을 실행하여 모듈을 컴파일합니다.

   ```bash
   gulp compile
   ```

   이 명령은 소스 코드를 Intel NUC로 전송하고 `build.sh`를 실행하여 모듈을 컴파일합니다.

## <a name="add-the-module-to-the-helloworld-sample-app-and-run-the-app-on-intel-nuc"></a>모듈을 hello_world 샘플 앱에 추가하고 Intel NUC에서 해당 앱을 실행합니다.

이 작업을 수행하려면 다음 단계를 따릅니다.

1. 다음 명령을 실행하여 Intel NUC의 사용 가능한 모든 모듈 이진 파일(.so 파일)을 나열합니다.

   ```bash
   gulp modules --list
   ```

   컴파일한 `my_module`의 이진 경로는 아래와 같이 표시됩니다.

   ```path
   /root/gateway_sample/module/my_module/build/libmy_module.so
   ```

   `config-gateway.json`에서 기본 로그인 사용자 이름을 변경하는 경우 이진 경로는 `root` 대신 `home/<your username>`으로 시작됩니다.

1. `hello_world` 샘플 앱에 `my_module`을 추가합니다.

   1. 다음 명령을 실행하여 `hello_world.json` 파일을 엽니다.

      ```bash
      code sample/hello_world/src/hello_world.json
      ```

   1. `modules` 섹션에 다음 코드를 추가합니다.

      ```json
      {
       "name": "my_module",
       "loader": {
       "name": "native",
       "entrypoint": {
       "module.path": "/root/gateway_sample/module/my_module/build/libmy_module.so"
         }
        },
       "args": null
      }
      ```

      `module.path` 값은 `/root/gateway_sample/module/my_module/build/libmy_module.so`여야 합니다. 이 코드는 `my_module`이 `module.path`에 지정된 모듈 이진 파일의 위치 및 게이트웨이와 연결되도록 선언합니다.
   1. `links` 섹션에 다음 코드를 추가합니다.

      ```json
      {
        "source": "hello_world",
        "sink": "my_module"
      }
      ```

      이 코드는 `hello_world` 모듈에서 `my_module`로 메시지가 전송되도록 지정합니다.

      ![hello_world_json](media/iot-hub-gateway-kit-lessons/lesson5/hello_world_json.png)

1. 다음 명령을 실행하여 `hello_world` 샘플 앱을 실행합니다.

   ```bash
   gulp run --config sample/hello_world/src/hello_world.json
   ```

   `--config` 매개 변수는 `hello_world.json` 파일을 사용하여 `run-hello-world.js` 스크립트가 강제로 실행되도록 합니다.

   ![hello_world_new](media/iot-hub-gateway-kit-lessons/lesson5/hello_world_new.png)

축하합니다. 이제 이 새 모듈의 동작을 볼 수 있습니다. 이 모듈은 타임스탬프와 함께 "hello world" 메시지를 출력합니다. 이것은 원래 "hello_world" 모듈의 결과와는 다릅니다.

## <a name="next-steps"></a>다음 단계

새 모듈을 만들었으며 hello_world 샘플에 추가하고 샘플 앱이 게이트웨이의 새 모듈로 실행되도록 했습니다. Azure IoT 게이트웨이 모듈에 대한 자세한 내용을 보려면 [https://github.com/Azure/azure-iot-gateway-sdk/tree/master/modules](https://github.com/Azure/azure-iot-gateway-sdk/tree/master/modules)에서 더 많은 모듈 샘플을 찾을 수 있습니다.

<!-- Images and links -->

[config_ble]: iot-hub-gateway-kit-c-lesson3-configure-ble-app.md
[setup_nuc]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md

