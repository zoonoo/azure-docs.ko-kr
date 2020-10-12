---
title: Azure IoT Central 디바이스 브리지 빌드 | Microsoft Docs
description: 다른 IoT 클라우드(Sigfox, Particle, The Things Network 등)를 사용자의 IoT Central 앱에 연결하는 IoT Central 디바이스 브리지를 빌드합니다.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: 6499c9c29d10a2056b0af5499b68b5edd67d82cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80158421"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>다른 IoT 클라우드를 IoT Central에 연결하는 IoT Central 디바이스 브리지 빌드

*이 항목의 내용은 관리자에게 적용됩니다.*

IoT Central 디바이스 브리지는 Sigfox, Particle, The Things Network 및 다른 클라우드를 사용자의 IoT Central 앱에 연결하는 오픈 소스 솔루션입니다. Sigfox의 저전력 광역 네트워크에 연결 된 자산 추적 장치를 사용 하 든, 또는 파티클 장치 클라우드에서 공기 품질 모니터링 장치를 사용 하 든, TTN의 토양 습기 모니터링 장치를 사용 하는 경우에는 IoT Central 장치 브리지를 사용 하 여 IoT Central의 기능을 직접 활용할 수 있습니다. 디바이스 브리지는 디바이스가 보내는 데이터를 IoT Central 앱을 통해 다른 클라우드로 전달하여 다른 IoT 클라우드를 IoT Central과 연결합니다. IoT Central 앱에서 해당 데이터에 대해 규칙을 빌드하고 분석을 실행하며, Microsoft Flow 및 Azure Logic Apps에서 워크플로를 만들고, 해당 데이터를 내보내는 등의 작업을 수행할 수 있습니다. GitHub에서 [IoT Central 디바이스 브리지](https://aka.ms/iotcentralgithubdevicebridge) 가져오기

## <a name="what-is-it-and-how-does-it-work"></a>정의 및 작동 방식
IoT Central 디바이스 브리지는 GitHub의 오픈 소스 솔루션입니다. 여러 Azure 리소스를 포함 하는 사용자 지정 Azure Resource Manager 템플릿을 Azure 구독에 배포 하는 "Azure에 배포" 단추를 사용할 수 있습니다. 리소스에는 다음이 포함됩니다.
-    Azure 함수 앱
-    Azure Storage 계정
-    소비 계획
-    Azure Key Vault

함수 앱은 장치 브리지의 중요 한 부분입니다. 간단한 웹후크 통합을 통해 다른 IoT 플랫폼 또는 사용자 지정 플랫폼에서 HTTP POST 요청을 받습니다. Sigfox, Particle 및 TTN 클라우드에 연결하는 방법을 보여주는 예제가 나와 있습니다. 플랫폼에서 함수 앱에 HTTP POST 요청을 보낼 수 있는 경우 사용자 지정 IoT 클라우드에 연결하도록 이 솔루션을 손쉽게 확장할 수 있습니다.
함수 앱은 IoT Central에서 허용되는 형식으로 데이터를 변환하고 DPS API를 통해 전달합니다.

![Azure Functions 스크린샷](media/howto-build-iotc-device-bridge/azfunctions.png)

IoT Central 앱에서 디바이스를 전달된 메시지에 있는 디바이스 ID로 인식하는 경우 해당 디바이스에 대해 새 측정값이 표시됩니다. IoT Central 앱에서 장치 ID를 본 적이 없는 경우 함수 앱은 해당 장치 ID를 사용 하 여 새 장치를 등록 하려고 시도 하 고 IoT Central 앱에 "연결 되지 않은 장치"로 표시 됩니다. 

## <a name="how-do-i-set-it-up"></a>설정하려면 어떻게 해야 하나요?
GitHub 리포지토리의 README 파일에 지침이 자세히 나열되어 있습니다. 

## <a name="pricing"></a>가격 책정
Azure 리소스를 Azure 구독에서 호스트합니다. [README 파일](https://aka.ms/iotcentralgithubdevicebridge)에서 가격 책정에 대한 자세한 정보를 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
IoT Central 디바이스 브리지를 빌드하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [디바이스 관리](howto-manage-devices.md)
