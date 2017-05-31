---
title: "Azure IoT Edge 시작(Linux) | Microsoft Docs"
description: "Linux 컴퓨터에서 게이트웨이를 빌드하는 방법 및 모듈과 JSON 구성 파일 등 Azure IoT Edge의 주요 개념에 대해 알아봅니다."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 38fd72fda6ea5d03d72c950803b09dd0b9e34fe4
ms.contentlocale: ko-kr
ms.lasthandoff: 05/18/2017


---
# <a name="explore-azure-iot-edge-architecture-on-linux"></a>Linux에서 Azure IoT Edge 아키텍처 살펴보기

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>샘플을 빌드하는 방법

시작하기 전에 먼저 Linux에서 SDK를 사용할 수 있도록 [개발 환경을 설정][lnk-setupdevbox]합니다.

1. 셸을 엽니다.
1. **iot-edge** 리포지토리의 로컬 복사본에 있는 루트 폴더로 이동합니다.
1. **tools/build.sh** 스크립트를 실행합니다. 이 스크립트는 **cmake** 유틸리티를 사용하여 **iot-edge** 리포지토리의 로컬 복사본 루트 폴더에 **build**라는 폴더를 만들고 메이크파일을 생성합니다. 그러면 스크립트에서 솔루션을 빌드하고 단위 테스트 및 종단간 테스트를 건너뜁니다. 단위 테스트를 빌드하고 실행하려는 경우 **--run-unittests** 매개 변수를 추가합니다. 종단간 테스트를 빌드하고 실행하려는 경우 **--run-e2e-tests** 매개 변수를 추가합니다.

> [!NOTE]
> **build.sh** 스크립트를 실행할 때마다 **iot-edge** 리포지토리의 로컬 복사본 루트 폴더에서 **build** 폴더를 삭제한 다음 다시 만듭니다.

## <a name="how-to-run-the-sample"></a>샘플을 실행하는 방법

1. **build.sh** 스크립트는 **iot-edge** 리포지토리의 로컬 복사본에 있는 **build** 폴더에 해당 출력을 생성합니다. 이 출력에는 이 샘플에서 사용된 두 개의 IoT Edge 모듈이 포함됩니다.

    빌드 스크립트는 **liblogger.so**를 **build/modules/logger/** 폴더에 배치하고 **libhello\_world.so**를 **build/modules/hello_world/** 폴더에 배치합니다. 다음 예제 JSON 설정 파일에 표시된 대로 이러한 경로를 **모듈 경로** 값에 사용합니다.
1. hello\_world\_sample 프로세스는 JSON 구성 파일에 대한 경로를 명령줄 인수 형태로 사용합니다. 다음 예제 JSON 파일은 **samples/hello\_world/src/hello\_world\_lin.json**의 SDK 리포지토리에서 제공됩니다. 이 구성 파일은 빌드 스크립트를 수정하여 IoT Edge 모듈이나 샘플 실행 파일을 기본 위치가 아닌 위치에 배치하지 않는 한 그대로 작동합니다.

   > [!NOTE]
   > 모듈 경로는 실행 파일이 있는 디렉터리가 아니라, hello\_world\_sample 실행 파일이 실행되는 위치의 현재 작업 디렉터리에 대한 상대 경로입니다. 샘플 JSON 구성 파일은 현재 작업 디렉터리에서 작성 중인 'log.txt'를 기본값으로 사용합니다.

    ```json
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
1. **빌드** 폴더로 이동합니다.
1. 다음 명령을 실행합니다.

   `./samples/hello_world/hello_world_sample ./../samples/hello_world/src/hello_world_lin.json`

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md

