---
title: 설치 및 프로 비전 후 성공 및 문제 해결 확인
description: 포함 파일
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/06/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 295769e5d4924e529b296dbb0b9d405ee197c1db
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979721"
---
## <a name="verify-successful-setup"></a>설치 성공 여부 확인

IoT Edge 서비스의 상태를 확인합니다. 실행 중으로 나열 되어야 합니다.  

# <a name="linux"></a>[Linux](#tab/linux)

```bash
systemctl status iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

```powershell
Get-Service iotedge
```

---

서비스 로그를 검사 합니다.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
journalctl -u iotedge --no-pager --no-full
```

# <a name="windows"></a>[Windows](#tab/windows)

IoT Edge 런타임 설치를 완료 하면 **배포-IoTEdge** 를 실행 하는 시간과 **iotedge**를 실행 하는 동안 발생 한 오류 목록이 표시 될 수 있습니다. 서비스가 구성 되기 전에 시작 되려고 하므로 이러한 오류가 발생 합니다.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

---

[문제 해결 도구](../articles/iot-edge/troubleshoot.md#run-the-check-command) 를 실행 하 여 가장 일반적인 구성 및 네트워킹 오류를 확인 합니다.

```powershell
iotedge check
```

장치에서 IoT Edge 하기 위해 첫 번째 모듈을 배포할 때 까지는 **$edgeHub** 시스템 모듈이 장치에 배포 되지 않습니다. 따라서 자동 검사는 연결 확인에 대 한 오류를 반환 합니다 `Edge Hub can bind to ports on host` . 장치에 모듈을 배포한 후에도이 오류가 발생 하지 않으면이 오류를 무시할 수 있습니다.

마지막으로 실행 중인 모듈을 나열 합니다.

```powershell
iotedge list
```

새로 설치한 후에는 실행 중인 것으로 표시 되는 유일한 모듈은 **edgeAgent**입니다.

## <a name="tips-and-troubleshooting"></a>팁 및 문제 해결

리소스 제한이 있는 디바이스에서는 [문제 해결 가이드](../articles/iot-edge/troubleshoot.md)의 지침에 따라 *OptimizeForPerformance* 환경 변수를 *false*로 설정하는 것이 좋습니다.

장치가 IoT Hub에 연결할 수 없고 네트워크에 프록시 서버가 있는 경우 [프록시 서버를 통해 통신 하도록 IoT Edge 장치 구성](../articles/iot-edge/how-to-configure-proxy-support.md)의 단계를 수행 합니다.

# <a name="linux"></a>[Linux](#tab/linux)

Linux 장치에서 명령을 실행 하려면 상승 된 권한이 필요 `iotedge` 합니다. 런타임을 설치한 후 머신에서 로그아웃했다가 다시 로그인하면 권한이 자동으로 업데이트됩니다. 그때까지를 사용 `sudo` 하 여 상승 된 권한으로 명령을 실행 합니다.

# <a name="windows"></a>[Windows](#tab/windows)

Windows 컨테이너를 실행 하는 Windows 장치에서 Moby 컨테이너 엔진은 IoT Edge의 일부로 설치 되었습니다. Moby 엔진은 Docker 데스크톱과 병렬로 실행 되도록 설계 되었습니다. `docker`장치의 컨테이너와 직접 상호 작용 하려는 경우 명령을 사용할 수 있습니다. 그러나 Docker 데스크톱이 장치에도 설치 된 경우에는 특히 Moby 엔진을 대상으로 해야 합니다.

예를 들어 모든 Docker 이미지를 나열 하려면 다음 명령을 사용 합니다.

```powershell
docker images
```

모든 Moby 이미지를 나열 하려면 Moby 엔진에 대 한 포인터를 사용 하 여 동일한 명령을 수정 합니다.

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

엔진 URI는 설치 스크립트의 출력에 나열 되거나, config.xml 파일의 컨테이너 런타임 설정 섹션에서 찾을 수 있습니다.

![구성에서 uri를 moby_runtime 합니다. yaml](./media/iot-edge-verify-troubleshoot-install/moby-runtime-uri.png)

---
