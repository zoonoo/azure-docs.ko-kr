---
title: Azure IoT Edge 모듈로 신뢰할 수 있는 응용 프로그램
description: Open Enclave SDK 및 API를 사용 하 여 신뢰할 수 있는 응용 프로그램을 작성 하 고 기밀 컴퓨팅을 위한 IoT Edge 모듈로 배포 합니다.
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: kgremban
ms.openlocfilehash: d81cc6c94c04c683362fd12cd6777c304a4b0a84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361979"
---
# <a name="confidential-computing-at-the-edge"></a>에 지에서 기밀 컴퓨팅

Azure IoT Edge은 장치의 secure enclaves 내에서 실행 되는 신뢰할 수 있는 응용 프로그램을 지원 합니다. 암호화는 전송 중이거나 미사용 상태에서 데이터에 대 한 보안을 제공 하지만 enclaves는 사용 하는 동안 데이터 및 작업에 대 한 보안을 제공 합니다. IoT Edge은 오픈 Enclave를 신뢰할 수 있는 응용 프로그램 개발을 위한 표준으로 지원 합니다.

보안은 종종 IoT (사물 인터넷)에 중점을 두고 있습니다. 일반적으로 IoT 장치는 개인 기능 내에서 보호 되는 것이 아니라 전 세계에 있는 경우가 많기 때문입니다. 이러한 노출을 통해 장치는 잘못 된 행위자가 물리적으로 액세스할 수 있기 때문에 변조 및 위조 위험을 초래할 수 있습니다. IoT Edge 장치는 중요 한 작업을에 지에서 실행할 수 있기 때문에 신뢰 및 무결성이 훨씬 더 필요 합니다. 일반적인 센서 및 발동기와는 달리 이러한 지능형 edge 장치는 이전에 보호 된 클라우드 또는 온-프레미스 환경 내 에서만 실행 되었던 중요 한 작업을 노출 합니다.

[IoT Edge security manager](iot-edge-security-manager.md) 는 기밀 컴퓨팅 챌린지의 한 부분을 해결 합니다. 보안 관리자는 HSM (하드웨어 보안 모듈)을 사용 하 여 IoT Edge 장치의 id 작업 및 진행 중인 프로세스를 보호 합니다.

기밀 컴퓨팅의 또 다른 측면은에 지에서 사용 중인 데이터를 보호 하는 것입니다. 공격자 ( *신뢰할 수 있는 실행 환경* )는 프로세서의 안전 하 고 격리 된 환경으로, 때로는 *enclave*라고도 합니다. *신뢰할 수 있는 응용 프로그램* 은 enclave에서 실행 되는 응용 프로그램입니다. Enclaves의 특성으로 인해 신뢰할 수 있는 응용 프로그램은 주 프로세서나 티에서 실행 되는 다른 앱에서 보호 됩니다.

## <a name="trusted-applications-on-iot-edge"></a>IoT Edge에서 신뢰할 수 있는 응용 프로그램

신뢰할 수 있는 응용 프로그램은 전송 중 및 미사용 상태로 암호화 되며 신뢰할 수 있는 실행 환경 내에서 실행 되도록 해독 됩니다. 이 표준은 IoT Edge 모듈로 배포 된 신뢰할 수 있는 응용 프로그램에 적용 됩니다.

개발자는 신뢰할 수 있는 응용 프로그램을 만들고이를 IoT Edge 모듈로 패키지 합니다. 응용 프로그램은 컨테이너 레지스트리로 푸시 되기 전에 암호화 됩니다. 응용 프로그램은 모듈이 IoT Edge 장치에서 시작 될 때까지 IoT Edge 배포 프로세스 전체에서 암호화 된 상태로 유지 됩니다. 신뢰할 수 있는 응용 프로그램이 장치의 티에 있으면 암호 해독 되 고 실행을 시작할 수 있습니다.

![다이어그램-신뢰할 수 있는 응용 프로그램은 보안 enclave 배포할 때까지 IoT Edge 모듈 내에서 암호화 됩니다.](./media/deploy-trusted-applications/trusted-applications-encrypted.png)

IoT Edge에서 신뢰할 수 있는 응용 프로그램은 [Azure 기밀 컴퓨팅](../confidential-computing/overview.md)의 논리적 확장입니다. 클라우드의 secure enclaves 내에서 실행 되는 워크 로드를 배포 하 여에 지에서 보안 enclaves 내에서 실행할 수도 있습니다.

## <a name="open-enclave"></a>Open Enclave

[Open ENCLAVE SDK](https://openenclave.io/sdk/) 는 개발자가 여러 플랫폼과 환경에 대해 신뢰할 수 있는 응용 프로그램을 만드는 데 도움이 되는 오픈 소스 프로젝트입니다. Open Enclave SDK는 장치에서 신뢰할 수 있는 실행 환경 내에서 작동 하는 반면 Open Enclave API는 티가 아닌 처리 환경 간의 인터페이스로 작동 합니다.

Open Enclave는 여러 하드웨어 플랫폼을 지원 합니다. 현재 enclaves에 대 한 IoT Edge 지원에는 오픈 휴대용 티 운영 체제 (OP-티 OS)가 필요 합니다. 자세히 알아보려면 [Open ENCLAVE SDK FOR OP-티 OS](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md)를 참조 하세요.

Open Enclave 리포지토리에는 개발자가 시작 하는 데 도움이 되는 샘플도 포함 되어 있습니다. 자세한 내용을 보려면 다음 소개 문서 중 하나를 선택 하세요.

* [Linux에서 Open Enclave SDK 샘플 빌드](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [Windows에서 Open Enclave SDK 샘플 빌드](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>하드웨어

현재 [Scalys](https://scalys.com/trustbox-industrial/) 는 신뢰할 수 있는 응용 프로그램을 IoT Edge 모듈로 배포 하기 위한 제조업체 서비스 계약과 유일 하 게 지원 되는 장치입니다. TrustBox는 TrustBox Edge를 기반으로 하 고 TrustBox EdgeXL 장치는 모두 Open Enclave SDK 및 Azure IoT Edge으로 미리 로드 됩니다.

자세한 내용은 [Scalys TrustBox의 Open Enclave 시작](https://aka.ms/scalys-trustbox-edge-get-started)을 참조 하세요.

## <a name="develop-and-deploy"></a>개발 및 배포

신뢰할 수 있는 응용 프로그램을 개발 하 고 배포할 준비가 되 면 Visual Studio Code [Microsoft Open Enclave](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) 확장을 통해 도움을 받을 수 있습니다. Linux 또는 Windows를 개발 컴퓨터로 사용 하 여 TrustBox 용 모듈을 개발할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Visual Studio Code 용 Open Enclave 확장](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension) 을 사용 하 여 IoT Edge 모듈로 신뢰할 수 있는 응용 프로그램 개발을 시작 하는 방법을 알아봅니다.
