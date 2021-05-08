---
title: 산업용 IoT 구성을 위해 Azure IoT Edge 내에서 Microsoft 연결된 캐시 | Microsoft Docs
description: 산업용 IoT 구성을 위해 Azure IoT Edge 내에서 Microsoft 연결된 캐시 자습서
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 083c7bf6edc7da1fd617487e91b0a3848fb401fe
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811816"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>Microsoft 연결된 캐시 미리 보기 배포 시나리오 샘플: 산업용 IoT 구성을 위해 Azure IoT Edge 내에서 Microsoft 연결된 캐시

제조 네트워크는 종종 [Purdue 네트워크 모델](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture)([ISA 95](https://en.wikipedia.org/wiki/ANSI/ISA-95) 및 [ISA 99](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99) 표준에 포함됨)에 따라 계층적 계층으로 구성됩니다. 이러한 네트워크에서는 최상위 계층만 클라우드에 연결되고 계층의 하위 계층은 인접한 북쪽 및 남쪽 계층과만 통신할 수 있습니다.

이 GitHub 샘플([산업용 IoT를 위한 Azure IoT Edge](https://github.com/Azure-Samples/iot-edge-for-iiot))에서는 다음을 배포합니다.

* Azure에서 시뮬레이트된 Purdue 네트워크
* 산업 자산 
* Azure IoT Edge 게이트웨이의 계층 구조
  
이러한 구성 요소는 네트워크 보안을 손상시키지 않고 산업 데이터를 획득하고 클라우드에 안전하게 업로드하는 데 사용됩니다. Microsoft 연결된 캐시를 배포하여 ISA 95 규격 네트워크 내의 모든 수준에서 콘텐츠 다운로드를 지원할 수 있습니다.

ISA 95 규격 네트워크 내에서 Microsoft 연결된 캐시 배포를 구성하는 핵심은 L3 IoT Edge 게이트웨이에서 OT 프록시 *및* 업스트림 호스트를 모두 구성하는 것입니다.

1. 두 수준의 중첩된 IoT Edge 게이트웨이 샘플에 설명된 대로 L5 및 L4 수준에서 Microsoft 연결된 캐시 배포를 구성합니다. 
2. L3 IoT Edge 게이트웨이에서 배포는 다음을 지정해야 합니다.
   
   * UPSTREAM_HOST - L3 Microsoft 연결된 캐시가 콘텐츠를 요청하는 L4 IoT Edge 게이트웨이의 IP/FQDN입니다.
   * UPSTREAM_PROXY - OT 프록시 서버의 IP/FQDN:PORT입니다.

3. OT 프록시가 허용 목록에 L4 MCC FQDN/IP 주소를 추가해야 합니다.

Microsoft 연결된 캐시가 제대로 작동하는지 확인하려면 모듈을 호스트하는 IoT Edge 디바이스의 터미널 또는 네트워크의 모든 디바이스에서 다음 명령을 실행합니다. \<Azure IoT Edge Gateway IP\>를 IoT Edge 게이트웨이의 IP 주소나 호스트 이름으로 바꿉니다. 이 보고서의 표시 유형에 대한 자세한 내용은 환경 변수 세부 정보를 참조하세요.

```bash
    wget http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```