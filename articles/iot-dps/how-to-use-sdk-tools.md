---
title: Azure IoT Hub 장치 프로 비전 서비스 Sdk 도구 사용
description: 이 문서에서는 개발을 위해 Azure IoT Hub 장치 프로 비전 서비스 (DPS) Sdk에서 제공 되는 도구를 검토 합니다.
author: robinsh
ms.author: robinsh
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: b817b3cfe47ed08cae9e7e0b1c2c24363f2ccfed
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974805"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Sdk에서 제공 되는 도구를 사용 하 여 프로 비전을 위한 개발을 간소화 하는 방법
IoT Hub Device Provisioning Service는 안전 하 고 확장 가능한 방식으로 무인 [자동 프로 비전](concepts-auto-provisioning.md) 을 사용 하 여 프로 비전 프로세스를 간소화 합니다.  X.509 인증서 또는 TPM (신뢰할 수 있는 플랫폼 모듈) 형식의 보안 증명이 필요 합니다.  Microsoft도 [다른 보안 하드웨어 파트너](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) 와 협력 하 여 IoT 배포 보안을 강화 합니다. 하드웨어 보안 요구 사항을 이해 하는 것은 개발자에 게 매우 어려울 수 있습니다. 개발자가 프로 비전 서비스와 통신 하는 클라이언트를 작성 하는 데 편리한 계층을 사용할 수 있도록 Azure IoT 프로 비전 서비스 Sdk 집합이 제공 됩니다. 또한 Sdk는 일반적인 시나리오에 대 한 샘플 뿐만 아니라 개발의 보안 증명을 간소화 하는 도구 집합을 제공 합니다.

## <a name="trusted-platform-module-tpm-simulator"></a>TPM (신뢰할 수 있는 플랫폼 모듈) 시뮬레이터
[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) 은 플랫폼을 인증 하기 위해 키를 안전 하 게 저장 하기 위한 표준을 참조할 수 있거나 표준을 구현 하는 모듈과 상호 작용 하는 데 사용 되는 i/o 인터페이스를 참조할 수 있습니다. Tpm은 불연속 하드웨어, 통합 된 하드웨어, 펌웨어 기반 또는 소프트웨어 기반으로 존재할 수 있습니다.  프로덕션 환경에서 TPM은 불연속 하드웨어, 통합 된 하드웨어 또는 펌웨어 기반으로 장치에 있습니다. 테스트 단계에서 소프트웨어 기반 TPM 시뮬레이터는 개발자에 게 제공 됩니다.  이 시뮬레이터는 지금은 Windows 플랫폼에서 개발 하는 경우에만 사용할 수 있습니다.

TPM 시뮬레이터를 사용 하는 단계는 다음과 같습니다.
1. [개발 환경을 준비](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) 하 고 GitHub 리포지토리를 복제 합니다.
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```아래에서 TPM 시뮬레이터 폴더로 이동 합니다.
3. 장치를 프로 비전 하기 위해 클라이언트 응용 프로그램을 실행 하기 전에 시뮬레이터를 실행 합니다.
4. 등록 ID 및 인증 키를 얻기 위해 프로 비전 프로세스 전체에서 시뮬레이터를 실행 하도록 합니다.  두 값은 실행의 한 인스턴스에만 유효 합니다.

## <a name="x509-certificate-generator"></a>X.509 인증서 생성기
[X.509 인증서](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) 를 증명 메커니즘으로 사용 하 여 프로덕션 크기를 조정 하 고 장치 프로 비전을 간소화할 수 있습니다.  [여러 가지 방법으로](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) x.509 인증서를 가져올 수 있습니다.
* 프로덕션 환경에서는 공용 루트 인증 기관에서 x.509 CA 인증서를 구입 하는 것이 좋습니다.
* 테스트 환경에서는 다음을 사용 하 여 x.509 루트 인증서 또는 x.509 인증서 체인을 생성할 수 있습니다.
    * OpenSSL: 스크립트를 사용 하 여 인증서를 생성할 수 있습니다.
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell 또는 Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * 주사위 분석 (장치 Id 컴퍼지션 엔진) 에뮬레이터: x.509 클라이언트 인증서에 기반한 암호화 장치 id 및 증명에 사용 될 수 있습니다.  주사위를 사용 하는 장치 id에 [대해 자세히 알아보세요](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) .

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>주사위 에뮬레이터에서 x.509 인증서 생성기 사용
Sdk는 [JAVA SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator)에 있는 x.509 인증서 생성기를 사용 하 여 주사위 에뮬레이터를 제공 합니다.  이 생성기는 플랫폼 간에 작동 합니다.  생성 된 인증서를 다른 언어로 개발 하는 데 사용할 수 있습니다.

현재 주사위 에뮬레이터는 루트 인증서, 중간 인증서, 리프 인증서 및 연결 된 개인 키를 출력 합니다.  그러나 루트 인증서 또는 중간 인증서는 개별 리프 인증서에 서명 하는 데 사용할 수 없습니다.  단일 서명 인증서를 사용 하 여 여러 장치의 리프 인증서에 서명 하는 그룹 등록 시나리오를 테스트 하려는 경우 OpenSSL를 사용 하 여 인증서 체인을 생성할 수 있습니다.

이 생성기를 사용 하 여 x.509 인증서를 생성 하려면:
1. [개발 환경을 준비](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) 하 고 GitHub 리포지토리를 복제 합니다.
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. 루트를 azure-iot-sdk-java로 변경 합니다.
3. ```mvn install -DskipTests=true```를 실행 하 여 필요한 모든 패키지를 다운로드 하 고 SDK를 컴파일합니다.
4. ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```에서 x.509 인증서 생성기의 루트로 이동 합니다.
5. ```mvn clean install```를 사용 하 여 빌드
6. Futtassa az eszközt az alábbi parancsokkal:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Amikor a rendszer erre kéri, opcionálisan megadhat egy _köznapi nevet_ a tanúsítvány számára.
8. 이 도구는 **클라이언트 인증서**, **클라이언트 인증서 개인 키**, **중간 인증서**및 **루트 인증서**를 로컬로 생성 합니다.

**클라이언트 인증서** 는 장치의 리프 인증서입니다.  **클라이언트 인증서** 와 연결 된 **클라이언트 인증서 개인 키** 가 장치 클라이언트에 필요 합니다. 선택한 언어에 따라 클라이언트 응용 프로그램에이를 배치 하는 메커니즘이 다를 수 있습니다.  자세한 내용은 x.509를 사용 하 여 [시뮬레이션 된 장치 만들기에 대 한](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) 빠른 시작을 참조 하세요.

루트 인증서 또는 중간을 사용 하 여 [프로그래밍 방식으로](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) 또는 [포털](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments)을 통해 등록 그룹 또는 개별 등록을 만들 수 있습니다.

## <a name="next-steps"></a>Következő lépések
* Azure IoT Hub 및 Azure IoT Hub Device 프로 비전 서비스에 대 한 [Azure IOT SDK]( https://github.com/Azure/azure-iot-sdks) 를 사용 하 여 개발
