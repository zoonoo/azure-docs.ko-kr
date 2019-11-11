---
title: Azure IoT Central에서 디바이스 연결 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central의 디바이스 연결과 관련된 주요 개념을 소개합니다.
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: ef0e4c9070733266349a37e863c48901eae90c16
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894592"
---
# <a name="get-connected-to-azure-iot-central-preview-features"></a>Azure IoT Central에 연결 (미리 보기 기능)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

이 문서에서는 Microsoft Azure IoT Central의 디바이스 연결과 관련된 주요 개념을 소개합니다.

Azure IoT Central는 [Azure IoT Hub 장치 프로 비전 서비스 (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) 를 사용 하 여 모든 장치 등록 및 연결을 관리 합니다.

DPS를 사용 하면 다음을 수행할 수 있습니다.

- 대규모로 장치를 등록 하 고 연결 하는 것을 지원 IoT Central 합니다.
- 장치 자격 증명을 생성 하 고 IoT Central UI를 통해 장치를 등록 하지 않고 오프 라인으로 장치를 구성 합니다.
- SAS (공유 액세스 서명)를 사용 하 여 연결 하는 장치입니다.
- 업계 표준 x.509 인증서를 사용 하 여 연결 하는 장치입니다.
- 사용자 고유의 장치 Id를 사용 하 여 IoT Central에 장치를 등록할 수 있습니다. 사용자 고유의 장치 Id를 사용 하면 기존 백 오피스 시스템과의 통합이 간단해 집니다.
- 장치를 IoT Central에 연결 하는 일관 된 단일 방법입니다.

이 문서에서는 다음과 같은 사용 사례에 대해 설명 합니다.

- [SAS를 사용 하 여 단일 장치를 신속 하 게 연결](#connect-a-single-device)
- [SAS를 사용 하 여 대규모로 장치 연결](#connect-devices-at-scale-using-sas)
- [X.509 인증서를 사용 하 여 대규모로 장치를 연결](#connect-devices-using-x509-certificates) 합니다 .이는 프로덕션 환경에 권장 되는 방법입니다.
- [먼저 디바이스를 등록하지 않고 연결](#connect-without-registering-devices)
- [IoT 플러그 앤 플레이 기능을 사용 하 여 장치 연결](#connect-devices-with-iot-plug-and-play)

## <a name="connect-a-single-device"></a>단일 디바이스 연결

이 방법은 IoT Central 또는 테스트 장치를 시험 하는 경우에 유용 합니다. IoT Central 응용 프로그램의 장치 연결 정보를 사용 하 여 DPS (장치 프로 비전 서비스)를 통해 장치를 IoT Central 응용 프로그램에 연결할 수 있습니다. 다음 언어에 대 한 샘플 DPS 장치 클라이언트 코드를 찾을 수 있습니다.

- [C\#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device)
- [Node.JS](https://github.com/Azure-Samples/azure-iot-samples-node/tree/master/provisioning/Samples/device)

## <a name="connect-devices-at-scale-using-sas"></a>SAS를 사용 하 여 대규모로 장치 연결

SAS를 사용 하 여 대규모로 장치를 IoT Central에 연결 하려면 장치를 등록 하 고 설정 해야 합니다.

### <a name="register-devices-in-bulk"></a>대량으로 장치 등록

IoT Central 응용 프로그램으로 많은 수의 장치를 등록 하려면 CSV 파일을 사용 하 여 [장치 id 및 장치 이름을 가져옵니다](howto-manage-devices.md#import-devices).

가져온 장치에 대 한 연결 정보를 검색 하려면 [IoT Central 응용 프로그램에서 CSV 파일을 내보냅니다](howto-manage-devices.md#export-devices).

> [!NOTE]
> 먼저 IoT Central에 등록 하지 않고 장치를 연결 하는 방법을 알아보려면 [먼저 장치를 등록 하지 않고 연결](#connect-without-registering-devices)을 참조 하세요.

### <a name="set-up-your-devices"></a>장치 설정

장치 코드에서 내보내기 파일의 연결 정보를 사용 하 여 장치가 IoT Central 응용 프로그램에 연결 하 고 IoT에 데이터를 보낼 수 있도록 합니다. 장치를 연결 하는 방법에 대 한 자세한 내용은 [다음 단계](#next-steps)를 참조 하세요.

## <a name="connect-devices-using-x509-certificates"></a>X.509 인증서를 사용 하 여 장치 연결

프로덕션 환경에서 x.509 인증서를 사용 하는 것이 IoT Central에 권장 되는 장치 인증 메커니즘입니다. 자세한 내용은 [X.509 CA 인증서를 사용하여 디바이스 인증](../../iot-hub/iot-hub-x509ca-overview.md)을 참조하세요.

다음 단계는 x.509 인증서를 사용 하 여 장치를 IoT Central에 연결 하는 방법을 설명 합니다.

1. IoT Central 응용 프로그램에서 장치 인증서를 생성 하는 데 사용 하 _는 중간 또는 루트 x.509 인증서를 추가 하 고 확인 합니다_ .

    - **관리 > 장치 연결 > 인증서 (x.509)** 로 이동 하 여 리프 장치 인증서를 생성 하는 데 사용 하는 x.509 루트 또는 중간 인증서를 추가 합니다.

      ![연결 설정](media/overview-iot-central-get-connected/connection-settings.png)

      보안 위반이 발생 하거나 기본 인증서가 만료 되도록 설정 된 경우 보조 인증서를 사용 하 여 가동 중지 시간을 줄입니다. 주 인증서를 업데이트 하는 동안 보조 인증서를 사용 하 여 장치를 계속 프로 비전 할 수 있습니다.

    - 인증서 소유권을 확인 하면 인증서의 업 로더는 인증서의 개인 키를 갖습니다. 인증서를 확인 하려면:
        - **확인 코드** 옆의 단추를 선택 하 여 코드를 생성 합니다.
        - 이전 단계에서 생성 한 확인 코드를 사용 하 여 x.509 확인 인증서를 만듭니다. 인증서를 .cer 파일로 저장 합니다.
        - 서명 된 확인 인증서를 업로드 하 고 **확인**을 선택 합니다.

          ![연결 설정](media/overview-iot-central-get-connected/verify-cert.png)

1. CSV 파일을 사용 하 여 IoT Central 응용 프로그램에서 _장치를 가져오고 등록_ 합니다.

1. _장치를 설정 합니다._ 업로드된 루트 인증서를 사용하여 리프 인증서를 생성합니다. **장치 ID** 를 리프 인증서의 CNAME 값으로 사용 합니다. 장치 ID는 모두 소문자 여야 합니다. 그런 다음 프로 비전 서비스 정보를 사용 하 여 장치를 프로그래밍 합니다. 첫 번째 장치에 대해 장치를 켜 놓으면 DPS에서 IoT Central 응용 프로그램에 대 한 연결 정보를 검색 합니다.

### <a name="further-reference"></a>추가 참조

- [RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases)에 대한 샘플 구현.

- [C의 샘플 디바이스 클라이언트.](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>테스트 목적 으로만 사용

테스트용 으로만 이러한 유틸리티를 사용 하 여 CA 인증서 및 장치 인증서를 생성할 수 있습니다.

- DevKit 장치를 사용 하는 경우이 [명령줄 도구](https://aka.ms/iotcentral-docs-dicetool) 는 인증서를 확인 하기 위해 IoT Central 응용 프로그램에 추가할 수 있는 CA 인증서를 생성 합니다.

- 이 [명령줄 도구](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) 를 사용 하 여 다음을 수행 합니다.
  - 인증서 체인을 만듭니다. GitHub 문서의 2 단계를 수행 합니다.
  - 인증서를 .cer 파일로 저장 하 여 IoT Central 응용 프로그램에 업로드 합니다.
  - IoT Central 응용 프로그램의 확인 코드를 사용 하 여 확인 인증서를 생성 합니다. GitHub 문서의 3 단계를 수행 합니다.
  - 장치 Id를 도구에 대 한 매개 변수로 사용 하 여 장치에 대 한 리프 인증서를 만듭니다. GitHub 문서의 4 단계를 따릅니다.

## <a name="connect-without-registering-devices"></a>장치를 등록 하지 않고 연결

를 사용 하면 Oem에서 가장 먼저 등록 하지 않고 IoT Central 응용 프로그램에 연결할 수 있는 대용량 제조업체 장치를 사용할 수 IoT Central 있습니다. 제조업체에서 적절 한 자격 증명을 생성 하 고 공장에서 장치를 구성 해야 합니다. 장치가 처음으로 켜 지 면 IoT Central 응용 프로그램에 자동으로 연결 됩니다. IoT Central 운영자는 데이터를 전송 하기 전에 장치를 승인 해야 합니다.

다음 다이어그램에서는이 흐름을 간략하게 설명 합니다.

![연결 설정](media/overview-iot-central-get-connected/device-connection-flow1.png)

다음 단계에서는이 프로세스에 대해 자세히 설명 합니다. 장치 인증에 SAS 또는 x.509 인증서를 사용 하는지에 따라이 단계는 약간 다릅니다.

1. 연결 설정 구성:

    - **X.509 certificate:** [루트/중간 인증서를 추가 및 확인](#connect-devices-using-x509-certificates) 하 고 다음 단계에서 장치 인증서를 생성 하는 데 사용 합니다.
    - **SAS:** 기본 키를 복사 합니다. 이 키는 IoT Central 응용 프로그램에 대 한 그룹 SAS 키입니다. 키를 사용 하 여 다음 단계에서 장치 SAS 키를 생성 합니다.
    ![연결 설정 SAS](media/overview-iot-central-get-connected/connection-settings-sas.png)

1. 장치 자격 증명 생성
    - **인증서 x.509:** IoT Central 응용 프로그램에 추가한 루트 또는 중간 인증서를 사용 하 여 장치에 대 한 리프 인증서를 생성 합니다. 리프 인증서의 CNAME으로 소문자 **장치 ID** 를 사용 해야 합니다. 테스트 목적 으로만이 [명령줄 도구](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) 를 사용 하 여 장치 인증서를 생성 합니다.
    - **SAS:** 이 [명령줄 도구](https://www.npmjs.com/package/dps-keygen) 를 사용 하 여 장치 SAS 키를 생성 합니다. 이전 단계의 그룹 **기본 키** 를 사용 합니다. 장치 ID는 소문자 여야 합니다.

      [키 생성기 유틸리티](https://github.com/Azure/dps-keygen)를 설치하려면 다음 명령을 실행합니다.

      ```cmd/sh
      npm i -g dps-keygen
      ```

      그룹 SAS 기본 키에서 장치 키를 생성 하려면 다음 명령을 실행 합니다.

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. 장치를 설정 하려면 **범위 ID**, **장치 id**및 **x.509 장치 인증서** 또는 **SAS 키**를 사용 하 여 각 장치를 플래시 합니다.

1. 그런 다음 장치를 사용 하 여 IoT Central 응용 프로그램에 연결 합니다. 장치를 전환 하면 먼저 DPS에 연결 하 여 IoT Central 등록 정보를 검색 합니다.

1. 연결 된 장치는 처음에 **장치** 페이지에 연결 되지 **않은 것으로** 표시 됩니다. 디바이스의 프로비전 상태는 **등록**됩니다. 장치를 적절 한 장치 템플릿으로 **마이그레이션하고** 장치를 승인 하 여 IoT Central 응용 프로그램에 연결 합니다. 그런 다음 장치는 IoT Hub에서 연결 문자열을 검색 하 고 데이터 보내기를 시작할 수 있습니다. 이제 장치 프로 비전이 완료 되었으며 프로 비전 상태가 **프로**비전 됩니다.

## <a name="individual-enrollment-based-device-connectivity"></a>개별 등록 기반 장치 연결

장치 마다 개별 등록에 대 한 인증 자격 증명이 있는 장치를 연결 하는 고객은 옵션입니다. 개별 등록은 연결할 수 있는 단일 장치에 대 한 항목입니다. 개별 등록은 증명 메커니즘으로 X.509 리프 인증서 또는 SAS 토큰(실제 또는 가상 TPM) 중 하나를 사용할 수 있습니다. 개별 등록의 장치 ID (즉, registration ID)는 영숫자, 소문자 이며 하이픈을 포함할 수 있습니다. [여기](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)에서 개별 등록에 대해 자세히 알아보세요.

> [!NOTE]
> 장치에 대 한 개별 등록을 만들면 앱에서 기본 그룹 등록 기반 증명 (SAS, X509) 보다 우선적으로 적용 됩니다.

### <a name="creating-individual-enrollments"></a>개별 등록 만들기
IoT Central는 다음과 같은 증명 메커니즘을 지원 합니다.

1. **대칭 키 증명:** 대칭 키 증명은 장치 프로 비전 서비스 인스턴스를 사용 하 여 장치를 인증 하는 간단한 방법입니다. 대칭 키를 사용 하 여 개별 등록을 만들려면 연결 대화 상자를 열고 개별 등록 및 메커니즘 "SAS"를 선택 하 고 기본 키 및 보조 키를 입력 합니다. SAS 키는 b a s e 64로 인코딩해야 합니다. 대칭 키 및 개별 등록를 사용 하 여 장치를 프로 비전 하는 장치 코드를 작성 하는 데 도움이 되는 코드 샘플 [링크](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) 는 다음과 같습니다.
1. **X.509 인증서:** X.509 인증서는 프로덕션을 확장 하는 뛰어난 방법인 인증서 기반 증명 메커니즘을 제안 합니다. 대칭 키를 사용 하 여 개별 등록을 만들려면 개별 등록 및 메커니즘 "x.509"을 선택 하 고, 기본 인증서 및 보조 인증서를 업로드 하 고, 저장을 클릭 하 여 등록을 만듭니다. 다음은 X509를 사용 하 여 장치를 프로 비전 하는 장치 코드를 작성 하는 데 도움이 되는 코드 샘플 [링크](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) 입니다. [개별 등록](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment) 항목에 사용 되는 장치 인증서에는 주체 이름을 개별 등록 항목의 장치 ID (즉, registration ID)로 설정 해야 한다는 요구 사항이 있습니다.
1. **TPM 증명:** TPM은 신뢰할 수 있는 플랫폼 모듈를 나타내며, HSM (하드웨어 보안 모듈) 유형이 며 장치를 연결 하는 가장 안전한 방법 중 하나입니다.  이 문서에서는 불연속, 펌웨어 또는 통합 TPM을 사용한다고 가정합니다. 소프트웨어 에뮬레이트된 TPM은 프로토타이핑 또는 테스트에 적합하지만 불연속, 펌웨어 또는 통합 TPM이 제공하는 것과 동일한 수준의 보안을 제공하지 않습니다. 프로덕션 환경에서 TPM 소프트웨어를 사용하는 것은 좋지 않습니다. 대칭 키를 사용 하 여 개별 등록을 만들려면 개별 등록 및 메커니즘 "TPM"을 선택 하 고 인증 키를 입력 하 여 등록을 만듭니다. TPM 유형에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation)에서 TPM 증명에 대해 자세히 알아볼 수 있습니다. TPM을 사용 하 여 장치를 프로 비전 하는 장치 코드를 작성 하는 데 도움이 되는 코드 샘플 [링크](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/TpmSample) 는 다음과 같습니다. TPM 기반 증명을 만들려면 인증 키를 입력 하 고 저장 합니다.

## <a name="connect-devices-with-iot-plug-and-play"></a>IoT를 사용 하 여 장치 연결 플러그 앤 플레이

IoT IoT Central 플러그 앤 플레이의 주요 기능 중 하나는 장치 연결 시 장치 템플릿을 자동으로 연결 하는 기능입니다. 장치는 장치 자격 증명과 함께 이제 장치 등록 호출의 일부로 **CapabilityModelId** 을 보낼 수 있으며, IoT Central는 장치 템플릿을 검색 하 고 연결 합니다. 검색 프로세스는 다음과 같은 순서를 따릅니다.

1. 는 이미 IoT Central 응용 프로그램에 게시 된 경우 장치 템플릿에 연결 합니다.
1. 게시 되 고 인증 된 기능 모델의 공용 리포지토리에서 인출 합니다.

다음은 DPS 등록 통화 중 장치가 전송 하는 추가 페이로드의 형식입니다.

```javascript
'__iot:interfaces': {
              CapabilityModelId: <this is the URN for the capability model>
          }
```

> [!NOTE]
> 장치에서 자동으로 연결 하 고, 모델을 검색 하 고, 데이터를 보내기 시작 하려면 자동 승인 옵션을 사용 하도록 설정 해야 합니다.

## <a name="device-status"></a>디바이스 상태

실제 장치가 IoT Central 응용 프로그램에 연결 되 면 장치 상태가 다음과 같이 변경 됩니다.

1. 장치 상태는 먼저 **등록**됩니다. 이 상태는 장치가 IoT Central에서 만들어지고 장치 ID가 있음을 의미 합니다. 장치는 다음과 같은 경우에 등록 됩니다.
    - **장치** 페이지에 새 실제 장치가 추가 됩니다.
    - **장치 페이지에서** **가져오기** 를 사용 하 여 일련의 장치를 추가 합니다.

1. 유효한 자격 증명을 사용 하 여 IoT Central 응용 프로그램에 연결 된 장치에서 프로 비전 단계가 완료 되 면 장치 상태가 **프로 비전** 됨으로 변경 됩니다. 이 단계에서 장치는 IoT Hub에서 연결 문자열을 검색 합니다. 이제 장치에서 IoT Hub에 연결 하 여 데이터 보내기를 시작할 수 있습니다.

1. 운영자는 장치를 차단할 수 있습니다. 장치가 차단 되 면 데이터를 IoT Central 응용 프로그램으로 보낼 수 없습니다. 차단 된 장치의 상태가 **차단 됨**입니다. 운영자는 데이터 보내기를 다시 시작 하기 전에 장치를 다시 설정 해야 합니다. 운영자가 장치를 차단 해제 하면 상태가 이전 값으로 반환 되 고 **등록** 되거나 **프로 비전**됩니다.

1. 장치 상태가 **승인 대기 중** 입니다. 즉, **자동 승인** 옵션이 사용 되지 않도록 설정 되어 있으며, IoT Central에 연결 하는 모든 장치를 운영자가 명시적으로 승인 해야 합니다. **장치 페이지에** 수동으로 등록 되지 않았지만 유효한 자격 증명으로 연결 된 장치는 **승인 대기 중인**장치 상태를 가집니다. 운영자는 **승인** 단추를 사용 하 여 **장치** 페이지에서 이러한 장치를 승인할 수 있습니다.

1. 장치 상태가 연결 되어 있지 않습니다. 즉, IoT Central에 연결 하는 장치에 장치 템플릿이 **연결 되어 있지** 않음을 의미 합니다. 이는 일반적으로 다음과 같은 시나리오에서 발생 합니다.
    - 장치 템플릿을 지정 하지 않고 장치 페이지에서 **가져오기** 를 사용 하 여 일련의 장치를 **추가 합니다.**
    - **장치** 페이지에 수동으로 등록 하지 않은 장치는 등록 중에 템플릿 ID를 지정 하지 않고 유효한 자격 증명으로 연결 됩니다.  
운영자는 **마이그레이션** 단추를 사용 하 여 장치를 **장치** 페이지에서 템플릿에 연결할 수 있습니다.

## <a name="sdk-support"></a>SDK 지원

Azure 장치 Sdk는 장치 코드를 구현 하는 가장 쉬운 방법을 제공 합니다. 현재 다음과 같은 디바이스 SDK를 사용할 수 있습니다.

- [C용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [Python용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [Node.js용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [Java용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [.NET용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK 기능 및 IoT Hub 연결

IoT Hub와의 모든 디바이스 통신에 다음 IoT Hub 연결 옵션이 사용됩니다.

- [디바이스-클라우드 메시지](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [디바이스 쌍](../../iot-hub/iot-hub-devguide-device-twins.md)

다음 표에는 Azure IoT Central 디바이스 기능이 IoT Hub 기능에 매핑되는 방식이 요약되어 있습니다.

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| 측정값: 원격 분석 | 디바이스-클라우드 메시징 |
| 디바이스 속성 | 디바이스 쌍 reported 속성 |
| 설정 | 디바이스 쌍 desired 및 reported 속성 |

장치 Sdk를 사용 하는 방법에 대 한 자세한 내용은 [Azure IoT Central 응용 프로그램에 DevDiv 키트 장치 연결](howto-connect-devkit.md) 예제 코드를 참조 하세요.

### <a name="protocols"></a>프로토콜

디바이스 SDK는 IoT Hub에 연결하기 위한 다음과 같은 네트워크 프로토콜을 지원합니다.

- MQTT
- AMQP
- HTTPS

프로토콜 간 차이점 및 프로토콜 선택 방법에 대한 지침은 [통신 프로토콜 선택](../../iot-hub/iot-hub-devguide-protocols.md)을 참조하세요.

지원되는 프로토콜을 디바이스에서 사용할 수 없는 경우 Azure IoT Edge를 사용하여 프로토콜을 변환하면 됩니다. IoT Edge는 Azure IoT Central 애플리케이션의 에지로 프로세싱을 오프로드하는 에지의 인텔리전스 시나리오를 지원합니다.

## <a name="security"></a>보안

디바이스와 Azure IoT Central 간에 교환되는 모든 데이터는 암호화됩니다. IoT Hub는 디바이스 연결 IoT Hub 엔드포인트 중 하나에 연결하는 디바이스의 모든 요청을 인증합니다. 유선으로 자격 증명을 교환하지 못하도록 방지하기 위해 디바이스에서는 서명된 토큰을 사용하여 인증합니다. 자세한 내용은 [IoT Hub에 대한 액세스 제어](../../iot-hub/iot-hub-devguide-security.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central의 장치 연결에 대해 알아보았습니다. 다음은 제안 된 다음 단계입니다.

- [DevKit 디바이스 준비 및 연결](howto-connect-devkit.md)
- [C SDK: 장치 클라이언트 SDK 프로 비전](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
