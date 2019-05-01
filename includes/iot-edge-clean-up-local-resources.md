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
ms.openlocfilehash: 2b61cc8c5c448c28e96b06afa3556688a82567ed
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866818"
---
### <a name="delete-local-resources"></a>로컬 리소스 삭제

디바이스에서 IoT Edge 런타임 및 관련 리소스를 제거하려는 경우 디바이스 운영 체제에 대한 적절한 명령을 사용합니다. 

#### <a name="windows"></a>Windows

IoT Edge 런타임을 제거합니다.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

IoT Edge 런타임을 제거하면 만든 컨테이너는 중지되지만 디바이스에는 계속 남아 있습니다. 모든 컨테이너를 봅니다.

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