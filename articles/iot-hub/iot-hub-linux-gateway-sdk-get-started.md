---
title: "Azure IoT Gateway SDK 시작(Linux) | Microsoft Docs"
description: "Linux 컴퓨터에 게이트웨이를 빌드하는 방법 및 모듈과 JSON 구성 파일 등 Azure IoT 게이트웨이 SDK의 주요 개념에 대해 알아봅니다."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/23/2016
ms.author: andbuc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 37b2a82d7f6043224e68219fde753eef73078ffd
ms.openlocfilehash: b3cc8e53b0c8bb7ea40b6ebcebe1f97d4a3e1180
ms.lasthandoff: 03/02/2017


---
# <a name="explore-the-iot-gateway-sdk-architecture-on-linux"></a>Linux에서 IoT Gateway SDK 아키텍처 탐색
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>샘플을 빌드하는 방법
시작하기 전에 먼저 Linux에서 SDK를 사용할 수 있도록 [개발 환경을 설정][lnk-setupdevbox]합니다.

1. 셸을 엽니다.
2. **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본에서 루트 폴더로 이동합니다.
3. **tools/build.sh** 스크립트를 실행합니다. 이 스크립트는 **cmake** 유틸리티를 사용하여 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본 루트 폴더에 **build**라는 폴더를 만들고 메이크파일을 생성합니다. 그러면 스크립트에서 솔루션을 빌드하고 단위 테스트 및 종단간 테스트를 건너뜁니다. 단위 테스트를 빌드하고 실행하려는 경우 **--run-unittests** 매개 변수를 추가합니다. 종단간 테스트를 빌드하고 실행하려는 경우 **--run-e2e-tests** 매개 변수를 추가합니다.

> [!NOTE]
> **build.sh** 스크립트를 실행할 때마다 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본 루트 폴더에서 **build** 폴더가 삭제되었다가 다시 생성됩니다.
> 
> 

## <a name="how-to-run-the-sample"></a>샘플을 실행하는 방법
1. **build.sh** 스크립트는 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본에 있는 **build** 폴더에 해당 출력을 생성합니다. 여기에는 이 샘플에서 사용된 두 개의 모듈이 들어 있습니다.
   
    빌드 스크립트는 **liblogger.so**를 **build/modules/logger/** 폴더에 배치하고 **libhello_world.so**를 **build/modules/hello_world/** 폴더에 배치합니다. 아래의 JSON 설정 파일에 표시된 대로 이러한 경로를 **module path** 값에 사용합니다.
2. hello_world_sample 프로세스는 JSON 구성 파일 경로를 명령줄의 인수 형태로 취합니다. JSON 파일 예제는 **azure-iot-gateway-sdk/samples/hello_world/src/hello_world_win.json**에서 리포지토리의 일부로 제공되었으며 아래에 복사되어 있습니다. 사용자가 빌드 스크립트를 수정하여 모듈 또는 샘플 실행 파일을 기본이 아닌 위치에 배치하지 않은 한 그대로 작동합니다.

   > [!NOTE]
   > 모듈 경로는 실행 파일이 있는 디렉터리가 아니라, hello_world_sample 실행 파일이 실행되는 위치의 현재 작업 디렉터리에 대한 상대 경로입니다. 샘플 JSON 구성 파일은 현재 작업 디렉터리에서 작성 중인 'log.txt'를 기본값으로 사용합니다.
   
    ```
    {
        "modules" :
        [
            {
              "name" : "logger",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/logger/liblogger.so"
                }
              },
              "args" : {"filename":"log.txt"}
            },
            {
                "name" : "hello_world",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/hello_world/libhello_world.so"
                }
              },
                "args" : null
            }
        ],
        "links": 
        [
            {
                "source": "hello_world",
                "sink": "logger"
            }
        ]
    }
    ```
3. **azure-iot-gateway-sdk/build** 폴더로 이동합니다.
4. 다음 명령을 실행합니다.
   
   ```
   ./samples/hello_world/hello_world_sample ./../samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

