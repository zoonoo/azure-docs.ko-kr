---
title: IoT Core에 Azure IoT Edge 설치 | Microsoft Docs
description: Windows IoT Core 장치에 Azure IoT Edge 런타임을 설치합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 17afc095fa0500ca98559a9523282d75949faa60
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564829"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Windows IoT Core에 IoT Edge 런타임 설치 - 미리 보기

Azure IoT Edge 및 [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/)가 함께 작동하여 소형 장치에서 에지를 계산할 수 있습니다. Azure IoT Edge 런타임은 IoT 업계에서 매우 널리 사용되는 아주 작은 SBC(단일 보드 컴퓨터) 장치에서도 실행할 수 있습니다. 

이 문서에서는 Windows IoT Core를 실행하는 개발 보드에서 런타임을 프로비전하는 과정을 단계별로 안내합니다. 

**현재 Windows IoT Core는 Intel x64 기반 프로세서에서만 Azure IoT Edge를 지원합니다.**

## <a name="install-the-container-runtime"></a>컨테이너 런타임 설치

1. **빌드 17134(RS4)** IoT Core 이미지로 보드를 구성합니다. 
1. 장치를 켠 다음, [PowerShell을 사용하여 원격으로 로그인](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)합니다.
1. PowerShell 콘솔에서 컨테이너 런타임을 설치합니다. 

   ```powershell
   Invoke-WebRequest https://master.dockerproject.org/windows/x86_64/docker-0.0.0-dev.zip -o temp.zip
   Expand-Archive .\temp.zip $env:ProgramFiles -f
   Remove-Item .\temp.zip
   $env:Path += ";$env:programfiles\docker"
   SETX /M PATH "$env:Path"
   dockerd --register-service
   start-service docker
   ```

   >[!NOTE]
   >이 컨테이너 런타임은 Moby 프로젝트 빌드 서버에서 가져온 것이며 평가 목적으로만 제공됩니다. Docker에서 테스트, 보증 또는 지원되지 않습니다.

## <a name="finish-installing"></a>설치 마침

IoT Edge 보안 디먼을 설치하고 [이 문서](how-to-install-iot-edge-windows-with-windows.md)의 지침을 사용하여 구성합니다.

## <a name="next-steps"></a>다음 단계

이제 IoT Edge 런타임을 실행하는 장치가 있으므로 [대규모 IoT Edge 모듈 배포 및 모니터링](how-to-deploy-monitor.md)을 수행하는 방법을 알아봅니다.