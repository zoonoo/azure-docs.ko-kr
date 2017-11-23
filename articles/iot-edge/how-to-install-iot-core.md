---
title: "IoT Core에 Azure IoT Edge 설치 | Microsoft Docs"
description: "Windows IoT Core 장치에 Azure IoT Edge 런타임을 설치합니다."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: be2a80645d23e709d6c5cfb3978498bbe85eca34
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Windows IoT Core에 IoT Edge 런타임 설치 - 미리 보기

Azure IoT Edge 런타임은 IoT 업계에서 매우 널리 사용되는 아주 작은 SBC(단일 보드 컴퓨터) 장치에서도 실행할 수 있습니다. 이 문서에서는 Windows IoT Core를 실행하는 [MinnowBoard Turbot][lnk-minnow] 개발 보드에서 런타임을 프로비전하는 과정을 단계별로 안내합니다.

## <a name="install-the-runtime"></a>런타임 설치

1. 호스트 시스템에 [Windows 10 IoT Core 대시보드][lnk-core]를 설치합니다.
1. [장치 설정][lnk-board]의 단계에 따라 MinnowBoard Turbot/MAX Build 16299 이미지로 보드를 구성합니다. 
1. 장치를 켠 다음 [PowerShell을 사용하여 원격으로 로그인][lnk-powershell]합니다.
1. PowerShell 콘솔에서 [Docker 이진 파일을 설치][lnk-docker-install]합니다.
1. PowerShell 콘솔에서 다음 명령을 실행하여 IoT Edge 런타임을 설치하고 구성을 확인합니다.

   ```powershell
   Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
   ```

   이 스크립트는 다음 항목을 제공합니다. 
   * Python 3.6
   * IoT Edge 제어 스크립트(iotedgectl.exe)

원격 PowerShell 창에서 iotedgectl.exe 도구의 정보 제공 출력이 빨간색으로 표시됩니다. 이는 반드시 오류를 나타내는 것은 아닙니다. 

## <a name="next-steps"></a>다음 단계

이제 IoT Edge 런타임을 실행하는 장치가 있으므로 [대규모 IoT Edge 모듈 배포 및 모니터링][lnk-deploy]을 수행하는 방법을 알아봅니다.

<!--Links-->
[lnk-minnow]: https://minnowboard.org/ 
[lnk-core]: https://docs.microsoft.com/windows/iot-core/connect-your-device/iotdashboard
[lnk-board]: https://developer.microsoft.com/windows/iot/Docs/GetStarted/mbm/sdcard/stable/getstartedstep2
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers