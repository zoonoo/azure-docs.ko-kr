---
title: "Azure IoT Edge 시작(Windows) | Microsoft Docs"
description: "Windows 컴퓨터에서 Azure IoT Edge 게이트웨이를 빌드하는 방법 및 모듈과 JSON 구성 파일 등 Azure IoT Edge의 주요 개념에 대해 알아봅니다."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 9aff3724-5e4e-40ec-b95a-d00df4f590c5
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 2a46a3511d4d286c26aa9ca2e4e619414d82be1d
ms.contentlocale: ko-kr
ms.lasthandoff: 05/16/2017


---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Windows에서 Azure IoT Edge 아키텍처 살펴보기

[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>샘플을 빌드하는 방법

시작하기 전에 먼저 Windows에서 SDK를 사용할 수 있도록 [개발 환경을 설정][lnk-setupdevbox]합니다.

1. **VS 2015용 개발자 명령 프롬프트** 또는 **VS 2017용 개발자 명령 프롬프트** 명령 프롬프트를 엽니다.
1. **iot-edge** 리포지토리의 로컬 복사본에 있는 루트 폴더로 이동합니다.
1. **tools\\build.cmd** 스크립트를 실행합니다. 이 스크립트는 Visual Studio 솔루션 파일을 만들고 솔루션을 빌드합니다. Visual Studio 솔루션은 **iot-edge** 리포지토리의 로컬 복사본에 있는 **build** 폴더에서 찾을 수 있습니다. 스크립트에 추가 매개 변수를 제공하여 단위 테스트 및 종단간 테스트를 빌드하여 실행할 수 있습니다. 이러한 매개 변수는 각각 **--run-unittests** 및 **--run-e2e-tests**입니다.

## <a name="how-to-run-the-sample"></a>샘플을 실행하는 방법

1. **build.cmd** 스크립트는 리포지토리의 로컬 복사본에 **build**라는 폴더를 만듭니다. 이 폴더에는 이 샘플에서 사용된 두 개의 IoT Edge 모듈이 들어 있습니다.

    빌드 스크립트는 **logger.dll**을 **build\\modules\\logger\\Debug** 폴더에 배치하고 **hello\_world.dll**을 **build\\modules\\hello_world\\Debug** 폴더에 배치합니다. 다음 JSON 설정 파일에 표시된 대로 이러한 경로를 **module path** 값에 사용합니다.
1. hello\_world\_sample 프로세스는 JSON 구성 파일에 대한 경로를 명령줄 인수 형태로 사용합니다. 다음 예제 JSON 파일은 **samples\\hello\_world\\src\\hello\_world\_win.json**의 SDK 리포지토리에서 제공됩니다. 이 구성 파일은 사용자가 빌드 스크립트를 수정하여 모듈 또는 샘플 실행 파일을 기본이 아닌 위치에 배치하지 않은 한 그대로 작동합니다.

   > [!NOTE]
   > 모듈 경로는 hello\_world\_sample.exe가 있는 디렉터리에 대한 상대 경로입니다. 샘플 JSON 구성 파일은 현재 작업 디렉터리에서 작성 중인 'log.txt'를 기본값으로 사용합니다.

    ```json
    {
      "modules": [
        {
          "name": "logger",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
            }
          },
          "args": { "filename": "log.txt" }
        },
        {
          "name": "hello_world",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\hello_world\\Debug\\hello_world.dll"
            }
          },
          "args": null
          }
      ],
      "links": [
        {
          "source": "hello_world",
          "sink": "logger"
        }
      ]
    }
    ```

1. **iot-edge** 리포지토리의 로컬 복사본에 있는 루트 폴더로 이동합니다.

1. 다음 명령을 실행합니다.

   `build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json`

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md

