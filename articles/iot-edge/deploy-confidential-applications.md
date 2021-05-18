---
title: Azure IoT Edge 모듈로서의 기밀 애플리케이션
description: Open Enclave SDK 및 API를 사용하여 기밀 애플리케이션을 작성하고 기밀 컴퓨팅을 위한 IoT Edge 모듈로 배포합니다.
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kgremban
ms.openlocfilehash: f9dff1b4c6b2489edd3cd685e3546618961d9757
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103487720"
---
# <a name="confidential-computing-at-the-edge"></a>에지 기밀 컴퓨팅

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge 디바이스의 보안 enclave 내에서 실행되는 기밀 애플리케이션을 지원합니다. 암호화는 전송 중 또는 유휴 상태인 데이터에 대한 보안을 제공하지만 enclave는 사용 중인 데이터 및 워크로드에 대한 보안을 제공합니다. IoT Edge는 기밀 애플리케이션 개발을 위한 표준으로 Open Enclave를 지원합니다.

보안은 IoT(사물 인터넷)에서 항상 중요한 핵심 사항이었습니다. 종종 IoT 디바이스가 프라이빗 시설 내부에서 보호되지 않고 외부에 있기 때문입니다. 이러한 노출로 인해 악의적인 사용자가 물리적으로 액세스할 수 있기 때문에 디바이스가 변조 및 위조될 위험이 있습니다. IoT Edge 디바이스는 중요한 워크로드를 에지에서 실행할 수 있기 때문에 신뢰와 무결성에 대한 요구가 훨씬 더 많습니다. 일반적인 센서 및 작동기와 달리 이러한 지능형 에지 디바이스는 이전에 보호된 클라우드 또는 온-프레미스 환경 내에서만 실행되었던 중요한 워크로드를 잠재적으로 노출합니다.

[IoT Edge 보안 관리자](iot-edge-security-manager.md)는 기밀 컴퓨팅 과제의 한 부분을 해결합니다. 보안 관리자는 HSM(하드웨어 보안 모듈)을 사용하여 ID 워크로드 및 IoT Edge 디바이스의 진행 중인 프로세스를 보호합니다.

기밀 컴퓨팅의 또 다른 측면은 에지에서 사용 중인 데이터를 보호하는 것입니다. TEE(*신뢰할 수 있는 실행 환경*)는 프로세서에서 안전하고 격리된 환경이며 *enclave* 라고도 합니다. *기밀 애플리케이션* 은 enclave에서 실행되는 애플리케이션입니다. enclave의 특성으로 인해 기밀 애플리케이션은 주 프로세서 또는 TEE에서 실행되는 다른 앱으로부터 보호됩니다.

## <a name="confidential-applications-on-iot-edge"></a>IoT Edge 기밀 애플리케이션

기밀 애플리케이션은 전송 중 및 유휴 상태에서 암호화되며 신뢰할 수 있는 실행 환경 내에서만 실행하도록 암호 해독됩니다. 이 표준은 IoT Edge 모듈로 배포된 기밀 애플리케이션에 적용됩니다.

개발자는 기밀 애플리케이션을 만들고 이를 IoT Edge 모듈로 패키지합니다. 애플리케이션은 컨테이너 레지스트리로 푸시되기 전에 암호화됩니다. 애플리케이션은 모듈이 IoT Edge 디바이스에서 시작될 때까지 IoT Edge 배포 프로세스 전체에서 암호화된 상태로 유지됩니다. 기밀 애플리케이션이 디바이스의 TEE 내에 있으면 암호 해독되어 실행을 시작할 수 있습니다.

![다이어그램 -기밀 애플리케이션은 보안 enclave에 배포될 때까지 IoT Edge 모듈 내에서 암호화됩니다.](./media/deploy-confidential-applications/confidential-applications-encrypted.png)

IoT Edge의 기밀 애플리케이션은 [Azure 기밀 컴퓨팅](../confidential-computing/overview.md)의 논리적 확장입니다. 클라우드의 보안 Enclave 내에서 실행되는 워크로드는 또한 에지의 보안 Enclave 내에서 실행되도록 배포할 수 있습니다.

## <a name="open-enclave"></a>Open Enclave

[Open ENCLAVE SDK](https://openenclave.io/sdk/)는 개발자가 여러 플랫폼 및 환경에 대한 기밀 애플리케이션을 만드는 데 도움이 되는 오픈 소스 프로젝트입니다. Open Enclave SDK는 디바이스의 신뢰할 수 있는 실행 환경 내에서 작동하는 반면 Open Enclave API는 TEE와 비TEE 처리 환경 간의 인터페이스 역할을 수행합니다.

Open Enclave는 여러 하드웨어 플랫폼을 지원합니다. 현재 enclave에 대한 IoT Edge 지원에는 OP-TEE OS(오픈 휴대용 TEE 운영 체제)가 필요합니다. 자세한 내용은 [OP-TEE OS용 Open Enclave SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md)를 참조하세요.

Open Enclave 리포지토리에는 개발자가 시작하는 데 도움이 되는 샘플도 포함되어 있습니다. 자세한 내용은 소개 문서 중 하나를 선택하세요.

* [Linux에서 Open Enclave SDK 샘플 빌드](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [Windows에서 Open Enclave SDK 샘플 빌드](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>하드웨어

현재 [Scalys Trustbox](https://scalys.com/trustbox-industrial/)는 기밀 애플리케이션을 IoT Edge 모듈로 배포하기 위한 제조업체 서비스 계약에서 지원되는 유일한 디바이스입니다. TrustBox는 TrustBox Edge 및 TrustBox EdgeXL 디바이스에 구축되며 모두 Open Enclave SDK 및 Azure IoT Edge로 미리 로드되어 제공됩니다.

자세한 내용은 [Scalys TrustBox용 Open Enclave 시작](https://aka.ms/scalys-trustbox-edge-get-started)을 참조하세요.

## <a name="develop-and-deploy"></a>개발 및 배포

기밀 애플리케이션을 개발하고 배포할 준비가 되면 Visual Studio Code를 위한 [Microsoft Open Enclave](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) 확장이 도움이 될 수 있습니다. 개발 머신으로 Linux 또는 Windows를 사용하여 TrustBox용 모듈을 개발할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Visual Studio Code용 Open Enclave 확장](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension)을 사용하여 기밀 애플리케이션을 IoT Edge 모듈로 개발하는 방법을 알아봅니다.
