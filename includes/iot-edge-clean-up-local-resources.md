---
title: 포함 파일
description: 포함 파일
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: cdb60ffb3ba3c31c336c8b74c46621792c707f74
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2018
ms.locfileid: "40047078"
---
### <a name="delete-local-resources"></a>로컬 리소스 삭제

디바이스에서 IoT Edge 런타임 및 관련 리소스를 제거하려는 경우 디바이스 운영 체제에 대한 적절한 명령을 사용합니다. 

#### <a name="windows"></a> Windows

IoT Edge 런타임을 제거합니다.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

IoT Edge 런타임을 제거하면 만든 컨테이너는 중지되지만 장치에는 계속 남아 있습니다. 모든 컨테이너를 봅니다.

   ```powershell
   docker ps -a
   ```

디바이스에서 만들어진 런타임 컨테이너를 삭제합니다.

   ```powershell
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

컨테이너 이름을 참조하여 `docker ps` 출력에 나열된 추가 컨테이너를 삭제합니다. 

#### <a name="linux"></a>Linux

IoT Edge 런타임을 제거합니다.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

IoT Edge 런타임을 제거하면 만든 컨테이너는 중지되지만 장치에는 계속 남아 있습니다. 모든 컨테이너를 봅니다.

   ```bash
   sudo docker ps -a
   ```

디바이스에서 만들어진 런타임 컨테이너를 삭제합니다.

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

컨테이너 이름을 참조하여 `docker ps` 출력에 나열된 추가 컨테이너를 삭제합니다. 

컨테이너 런타임을 제거합니다.

   ```bash
   sudo apt-get remove --purge moby
   ```