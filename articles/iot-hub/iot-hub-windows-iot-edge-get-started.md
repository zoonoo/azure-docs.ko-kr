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
ms.date: 09/29/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 262963250921ceb6f1a2fed0f68c36a9f052b47b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Windows에서 Azure IoT Edge 아키텍처 살펴보기

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>샘플 실행

**build.cmd** 스크립트는 **iot-edge** 리포지토리의 로컬 복사본에 있는 **build** 폴더에 해당 출력을 생성합니다. 이 출력에 여러 파일이 포함되어 있지만 이 샘플은 다음 세 가지에 중점을 둡니다.
- 두 개의 IoT Edge 모듈: **build\\modules\\logger\\Debug\\logger.dll** 및 **build\\modules\\hello_world\\Debug\\hello\_world.dll**. 
- 실행 파일: **build\\samples\\hello\_world\\Debug\\hello\_world\_sample.exe**. 이 프로세스는 JSON 구성 파일을 명령줄 인수로 사용합니다.

이 샘플에서 사용하는 네 번째 파일은 빌드 폴더에 없지만 iot edge 리포지토리에 복제할 때에는 포함되어 있었습니다.
- JSON 구성 파일: **samples\\hello\_world\\src\\hello\_world\_win.json**. 이 파일은 두 모듈의 경로를 포함하고 있습니다. 또한 logger.dll이 출력을 기록하는 위치를 선언합니다. 기본값은 현재 작업 디렉터리의 **log.txt**입니다. 

   >[!NOTE]
   >테스트를 위해 샘플 모듈을 이동하거나 사용자 고유의 모듈을 추가하는 경우 구성 파일의 **module.path** 값이 일치하도록 업데이트해야 합니다. 모듈 경로는 **hello\_world\_sample.exe**가 있는 디렉터리에 대한 상대 경로입니다. 

샘플을 실행하려면 다음 단계를 수행합니다.

1. **iot-edge** 리포지토리의 로컬 복사본에 있는 루트의 **build** 폴더로 이동합니다.

1. 다음 명령을 실행합니다.

   ```cmd
   samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
   ```

1. 다음 출력은 샘플이 성공적으로 실행되고 있다는 의미입니다.

   ```cmd
   gateway successfully created from JSON
   gateway shall run until ENTER is pressed
   ```
  
1. **Enter** 키를 누르면 프로세스가 중지됩니다.


[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]
