---
title: "Azure IoT Hub Device Provisioning 서비스의 보안 개념 | Microsoft Docs"
description: "Device Provisioning 서비스 및 IoT Hub를 사용하는 장치에 해당하는 보안 프로비전 개념 설명"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 3ccbaaf55d2bdfedffcdb5ca069798328e2d75fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>IoT Hub Device Provisioning 서비스 보안 개념 

IoT Hub Device Provisioning 서비스는 지정된 IoT 허브에 대한 제로 터치 장치 프로비전을 구성하도록 사용하는 IoT Hub에 대한 도우미 서비스입니다. Device Provisioning 서비스를 사용하여 안전하고 확장 가능한 방식으로 수백만 개의 장치를 프로비전할 수 있습니다. 이 문서는 장치 프로비전과 관련된 *보안* 개념의 개요를 제공합니다. 이 문서는 배포를 위한 장치 준비에 관련된 모든 사람에게 적합합니다.

## <a name="attestation-mechanism"></a>증명 메커니즘

증명 메커니즘은 장치의 ID 확인에 사용되는 방법입니다. 증명 메커니즘은 지정된 장치로 사용할 증명 방법을 프로비전 서비스에 알려 주는 등록 목록과 관련되어 있기도 합니다.

> [!NOTE]
> IoT Hub는 해당 서비스에서 비슷한 개념에 대해 "인증 체계"를 사용합니다.

Device Provisioning 서비스는 두 가지 형태의 증명을 지원합니다.
* 표준 X.509 인증서 인증 흐름을 기반으로 하는 **X.509 인증서**
* 키에 대해 TPM 표준을 사용하여 nonce 챌린지를 기반으로 하는 **SAS 토큰** 장치에서 실제 TPM이 필요하지 않지만 서비스는 [TPM 사양](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)당 인증 키를 사용하여 증명하기를 기대합니다.

## <a name="hardware-security-module"></a>하드웨어 보안 모듈

하드웨어 보안 모듈 또는 HSM은 안전한 장치 비밀의 하드웨어 기반 저장소를 위해 사용되며 가장 안전한 형태의 비밀 저장소입니다. X.509 인증서 및 SAS 토큰은 HSM에 저장될 수 있습니다. HSM은 증명 메커니즘 및 프로비전 지원에 사용될 수 있습니다.

> [!TIP]
> 장치에 비밀을 안전하게 저장하는 데 장치와 함께 HSM을 사용하는 것이 좋습니다.

장치 비밀은 소프트웨어(메모리)에도 저장될 수 있지만 HSM보다 덜 안전한 형태의 저장소입니다.

## <a name="trusted-platform-module-tpm"></a>TPM(신뢰할 수 있는 플랫폼 모듈)

TPM은 플랫폼을 인증하는 데 사용되는 키를 안전하게 저장하기 위한 표준을 참조할 수 있거나 표준을 구현하는 모듈과 상호 작용하는 데 사용되는 I/O 인터페이스를 참조할 수 있습니다. TPM은 별도 하드웨어, 통합된 하드웨어, 펌웨어 기반 또는 소프트웨어 기반으로 존재할 수 있습니다. [TPM 및 TPM 증명](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation)에 대해 자세히 알아봅니다. Device Provisioning 서비스는 TPM 2.0만 지원합니다.

## <a name="endorsement-key"></a>인증 키

인증 키는 제조 과정에서 삽입된 TPM 내부에 포함된 비대칭 키이며 모든 TPM에 대해 고유합니다. 인증 키를 변경하거나 제거할 수 없습니다. 인증 키의 비공개 부분 TPM 외부로 공개되지 않는 반면 인증 키의 공개 부분은 정품 TPM을 인식하는 데 사용됩니다. [인증 키](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx)에 대해 자세히 알아봅니다.

## <a name="storage-root-key"></a>저장소 루트 키

저장소 루트 키는 TPM에 저장되고 이러한 키를 TPM 없이 사용할 수 없도록 응용 프로그램에 의해 생성된 TPM 키를 보호하는 데 사용됩니다. 저장소 루트 키는 TPM의 소유권을 가져올 때 생성됩니다. 새 사용자가 소유권을 가져올 수 있도록 TPM을 삭제하는 경우 새 저장소 루트 키가 생성됩니다. [저장소 루트 키](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx)에 대해 자세히 알아봅니다.

## <a name="root-certificate"></a>루트 인증서

루트 인증서는 인증 기관을 나타내는 X.509 인증서의 형식이며 자체 서명됩니다. 인증서 체인의 종착지입니다.

## <a name="intermediate-certificate"></a>중간 인증서

중간 인증서는 루트 인증서에 의해(또는 해당 체인의 루트 인증서를 사용하여 다른 인증서에 의해) 서명된 X.509 인증서이며 리프 인증서에 서명하는 데 사용됩니다.

## <a name="leaf-certificate"></a>리프 인증서

리프 인증서 또는 최종 엔터티 인증서는 인증서 소유자를 식별하는 데 사용되며 해당 인증서 체인의 루트 인증서를 갖습니다. 리프 인증서는 다른 인증서에 서명하는 데 사용되지 않습니다.
