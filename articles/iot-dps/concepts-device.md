---
title: Azure 장치 프로 비전의 장치 개념 | Microsoft Docs
description: DPS (장치 프로 비전 서비스) 및 IoT Hub를 사용 하는 장치에 특정 한 장치 프로 비전 개념 설명
author: nberdy
ms.author: nberdy
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f5f931622f793a1146c04403e8c5e1a5ef7a7d62
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975315"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>IoT Hub Device Provisioning Service 장치 개념

IoT Hub Device Provisioning Service는 지정 된 IoT Hub에 대 한 0 터치 장치 프로 비전을 구성 하는 데 사용 하는 IoT Hub에 대 한 도우미 서비스입니다. A Device Provisioning Service-szel több millió eszköz kiépítését végezheti el biztonságosan és skálázható módon.

이 문서에서는 장치 프로 비전에 관련 된 *장치* 개념의 개요를 제공 합니다. 이 문서는 배포를 위해 장치를 준비 하는 [제조 단계](about-iot-dps.md#manufacturing-step) 와 관련 된 가상 사용자와 가장 관련이 있습니다.

## <a name="attestation-mechanism"></a>증명 메커니즘

증명 메커니즘은 장치의 id를 확인 하는 데 사용 되는 방법입니다. 증명 메커니즘은 지정 된 장치에서 사용할 증명 방법을 프로 비전 서비스에 알리는 등록 목록과도 관련이 있습니다.

> [!NOTE]
> IoT Hub는 "인증 체계"를 사용 하 여 해당 서비스에서 유사한 개념을 사용 합니다.

장치 프로 비전 서비스는 다음과 같은 증명 형식을 지원 합니다.
* 표준 X. x.509 인증서 인증 흐름을 기반으로 하는 **x.509 인증서** 입니다.
* 키에 대 한 TPM 표준을 사용 하 여 서명 된 SAS (공유 액세스 서명) 토큰을 제공 하는 nonce 챌린지를 기반으로 하는 **tpm (신뢰할 수 있는 플랫폼 모듈)** 이 경우 장치에 물리적 TPM이 필요 하지는 않지만 서비스는 [tpm 사양](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)에 따라 인증 키를 사용 하 여 증명할 것으로 예상 합니다.
* 해시 된 서명 및 포함 된 만료를 포함 하는 SAS (공유 액세스 서명) [보안 토큰](../iot-hub/iot-hub-devguide-security.md#security-tokens)을 기반으로 하는 **대칭 키** 입니다. 자세한 내용은 [대칭 키 증명](concepts-symmetric-key-attestation.md)을 참조 하세요.

## <a name="hardware-security-module"></a>하드웨어 보안 모듈

하드웨어 보안 모듈 (HSM)은 장치 비밀의 안전 하 고 하드웨어 기반 저장소에 사용 되며 가장 안전한 형태의 비밀 저장소입니다. X.509 인증서 및 SAS 토큰 모두 HSM에 저장 될 수 있습니다. Hsm은 프로 비전 서비스에서 지 원하는 두 증명 메커니즘과 함께 사용할 수 있습니다.

> [!TIP]
> 장치에 비밀을 안전 하 게 저장 하는 장치에 HSM을 사용 하는 것이 좋습니다.

장치 암호는 소프트웨어 (메모리)에도 저장 될 수 있지만 HSM 보다 안전 하지 않은 저장소 형식입니다.

## <a name="registration-id"></a>Regisztráció azonosítója

등록 ID는 장치 프로 비전 서비스에서 장치를 고유 하 게 식별 하는 데 사용 됩니다. 장치 ID는 프로 비전 서비스 [id 범위](#id-scope)에서 고유 해야 합니다. 각 장치에는 등록 ID가 있어야 합니다. 등록 ID는 영숫자, 대/소문자를 구분 하지 않으며, 콜론, 마침표, 밑줄, 하이픈 등의 특수 문자를 포함할 수 있습니다.

* TPM의 경우 TPM 자체에서 등록 ID를 제공 합니다.
* X.509 기반 증명의 경우 등록 ID는 인증서의 주체 이름으로 제공 됩니다.

## <a name="device-id"></a>Eszközazonosító

장치 ID는 IoT Hub 표시 되는 ID입니다. 등록 항목에서 원하는 장치 ID를 설정할 수 있지만이를 설정할 필요는 없습니다. 원하는 장치 ID 설정은 개별 등록 지원 됩니다. 등록 목록에서 원하는 장치 ID를 지정 하지 않은 경우 장치를 등록할 때 등록 ID가 장치 ID로 사용 됩니다. [IoT Hub에서 장치 id](../iot-hub/iot-hub-devguide-identity-registry.md)에 대해 자세히 알아보세요.

## <a name="id-scope"></a>ID 범위

사용자가 생성 하 고 장치를 등록할 특정 프로 비전 서비스를 고유 하 게 식별 하는 데 사용 되는 경우 ID 범위는 장치 프로 비전 서비스에 할당 됩니다. ID 범위는 서비스에 의해 생성 되며 변경할 수 없습니다 .이로 인해 고유성이 보장 됩니다.

> [!NOTE]
> 고유성은 장기 실행 배포 작업 및 합병 및 획득 시나리오에 중요 합니다.
