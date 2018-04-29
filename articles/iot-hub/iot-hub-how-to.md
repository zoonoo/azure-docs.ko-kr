---
title: Azure IoT Hub 방법 | Microsoft Docs
description: 개발자로서 다양한 IoT Hub 기능을 어떻게 사용합니까?
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: dobett
ms.openlocfilehash: 9b112d2d7fc1756b74e98335831175f5d4c13320
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-use-azure-iot-hub"></a>Azure IoT Hub 사용 방법

IoT Hub 서비스를 개발하는 방법을 알아보는 다양한 옵션이 있습니다.

* IoT Hub의 기능을 자세히 설명하는 개념 문서를 읽어보세요.
* IoT Hub의 다양한 기능을 다루는 자습서 중 하나를 수행하세요.

## <a name="developer-guide"></a>개발자 가이드

개발자로서 [개발자 가이드][lnk-devguide]에서 IoT Hub에 대한 자세한 개념적 지침을 읽을 수 있습니다. 이 가이드에는 다음이 포함됩니다.

* 사용 방법을 배울 수 있는 모든 IoT Hub 기능에 대한 자세한 설명
* 여러 옵션을 사용할 수 있는 경우 선택하는 방법에 대한 지침

## <a name="tutorials"></a>자습서

실습 연습을 통해 특정 IoT Hub 기능에 대해 자세히 알아보려는 경우 여러 자습서에서 선택할 수 있습니다. 대부분의 자습서는 여러 프로그래밍 언어로 제공됩니다. 이 자습서는 다음을 포함합니다.

- [경로를 사용하여 IoT Hub 장치-클라우드 메시지 처리][lnk-routes-tutorial]. 이 자습서는 IoT Hub 라우팅 규칙을 사용하여 손쉬운 구성 기반 방식으로 장치-클라우드 메시지를 발송하는 방법을 보여 줍니다.

- [IoT Hub를 사용하여 클라우드-장치 메시지 보내기][lnk-c2d-tutorial]. 이 자습서는 IoT Hub를 통해 클라우드-장치 메시지를 보내고 장치에서 클라우드-장치 메시지를 수신하는 방법을 보여 줍니다.

- [IoT Hub를 사용하여 장치에서 클라우드로 파일 업로드][lnk-upload-tutorial]. 이 자습서는 IoT Hub의 파일 업로드 기능을 사용하는 방법을 보여 줍니다.

- [장치 쌍 시작][lnk-twin-tutorial]. 이 자습서는 장치 쌍, reported 속성, desired 속성 및 태그를 소개합니다. 장치 쌍을 사용하여 장치와 값을 동기화합니다.

- [직접 메서드 사용][lnk-methods-tutorial]. 이 자습서는 직접 메서드를 사용하는 방법을 보여 줍니다. 시뮬레이션된 장치에서 직접 메서드에 대한 처리기를 추가하고 IoT Hub에서 직접 메서드를 호출합니다.

- [장치 관리 시작][lnk-dm-tutorial]. 이 자습서는 쌍 및 직접 메서드와 같은 주요 장치 관리 기능을 사용하는 방법을 보여 줍니다. 시뮬레이션된 장치를 원격으로 다시 부팅하는 데 이러한 기능을 사용합니다.

- [desired 속성을 사용하여 장치 구성][lnk-properties-tutorial]. 이 자습서는 장치를 원격으로 구성하기 위해 장치 쌍의 desired 및 reported 속성을 사용하는 방법을 보여 줍니다.

- [장치 관리를 사용하여 장치 펌웨어 업데이트][lnk-jobs-tutorial]. 이 자습서는 쌍 및 직접 메서드와 같은 주요 장치 관리 기능을 사용하는 방법을 보여 줍니다. 이러한 기능을 사용하여 장치의 펌웨어를 원격으로 업데이트하는 방법을 배웁니다.

- [작업 예약 및 브로드캐스트][lnk-schedule-tutorial]. 이 자습서는 예약된 시간에 여러 장치와 상호 작용하기 위해 desired 속성과 직접 메서드를 사용하는 방법을 보여 줍니다.

## <a name="next-steps"></a>다음 단계

IoT Hub 서비스에 대한 자세한 내용을 보려면 [개발자 가이드][lnk-devguide]를 참조하세요.

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-routes-tutorial]: ./iot-hub-csharp-csharp-process-d2c.md
[lnk-c2d-tutorial]: ./iot-hub-csharp-csharp-c2d.md
[lnk-upload-tutorial]: ./iot-hub-csharp-csharp-file-upload.md
[lnk-twin-tutorial]: ./iot-hub-node-node-twin-getstarted.md
[lnk-methods-tutorial]: ./iot-hub-node-node-direct-methods.md
[lnk-dm-tutorial]: ./iot-hub-node-node-device-management-get-started.md
[lnk-properties-tutorial]: ./iot-hub-node-node-twin-how-to-configure.md
[lnk-jobs-tutorial]: ./iot-hub-node-node-firmware-update.md
[lnk-schedule-tutorial]: ./iot-hub-node-node-schedule-jobs.md