---
title: Azure IoT Edge에 대 한 Windows 스크립트 | Microsoft Docs
description: Windows 장치에서 설치, 제거 또는 업데이트 하는 IoT Edge PowerShell 스크립트에 대 한 참조 정보
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/06/2020
ms.topic: reference
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a082ccb62103ab5bd027bf49b9ee05bc48c63115
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979673"
---
# <a name="powershell-scripts-for-iot-edge-on-windows"></a>Windows의 IoT Edge에 대 한 PowerShell 스크립트

Windows 장치에서 IoT Edge를 설치, 업데이트 또는 제거 하는 PowerShell 스크립트를 이해 합니다.

이 문서에서 설명 하는 명령은 `IoTEdgeSecurityDaemon.ps1` 모든 [IoT Edge 릴리스와](https://github.com/Azure/azure-iotedge/releases)함께 출시 되는 파일에서 가져온 것입니다. 최신 버전의 스크립트는 aka.ms/iotedge-win에서 항상 사용할 수 있습니다.

Cmdlet을 사용 하 여 명령 중 하나를 실행 하 여 `Invoke-WebRequest` 최신 스크립트 버전에 액세스할 수 있습니다. 예를 들면 다음과 같습니다.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge
```

이 스크립트 또는 특정 릴리스의 스크립트 버전을 다운로드 하 여 명령을 실행할 수도 있습니다. 예를 들면 다음과 같습니다.

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Deploy-IoTEdge
```

제공 된 스크립트는 보안을 강화 하기 위해 서명 됩니다. 장치에 스크립트를 다운로드 하 고 다음 PowerShell 명령을 실행 하 여 서명을 확인할 수 있습니다.

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

서명이 확인 되 면 출력 상태는 **유효** 합니다.

## <a name="deploy-iotedge"></a>Deploy-IoTEdge

Deploy-IoTEdge 명령은 IoT Edge 보안 디먼 및 해당 종속성을 다운로드 하 고 배포 합니다. 배포 명령은 이러한 공통 매개 변수를 허용 합니다. 전체 목록은 명령을 사용 `Get-Help Deploy-IoTEdge -full` 합니다.  

| 매개 변수 | 허용되는 값 | 주석 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 또는 **Linux** | 컨테이너 운영 체제가 지정 되지 않은 경우 Windows가 기본값입니다.<br><br>Windows 컨테이너의 경우 IoT Edge는 설치에 포함 된 Moby 컨테이너 엔진을 사용 합니다. Linux 컨테이너의 경우 설치를 시작하기 전에 컨테이너 엔진을 설치해야 합니다. |
| **프록시** | 프록시 URL | 디바이스가 프록시 서버를 통해 인터넷에 연결해야 하는 경우 이 매개 변수를 포함합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **OfflineInstallationPath** | 디렉터리 경로 | 이 매개 변수를 포함 하는 경우 설치 관리자는 설치에 필요한 IoT Edge cab 및 VC Runtime MSI 파일에 대해 나열 된 디렉터리를 확인 합니다. 디렉터리에서 찾을 수 없는 파일이 다운로드 됩니다. 두 파일이 모두 디렉터리에 있으면 인터넷에 연결 하지 않고 IoT Edge를 설치할 수 있습니다. 이 매개 변수를 사용 하 여 특정 버전을 사용할 수도 있습니다. |
| **InvokeWebRequestParameters** | 매개 변수 및 값의 해시 테이블입니다. | 설치 중에 여러 개의 웹 요청이 생성됩니다. 이 필드를 사용하여 해당 웹 요청에 대한 매개 변수를 설정합니다. 이 매개 변수는 프록시 서버에 대한 자격 증명을 구성하는 데 유용합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **RestartIfNeeded** | 없음 | 이 플래그를 사용 하면 필요에 따라 메시지를 표시 하지 않고 배포 스크립트에서 컴퓨터를 다시 시작할 수 있습니다. |

## <a name="initialize-iotedge"></a>Initialize-IoTEdge

Initialize-IoTEdge 명령은 장치 연결 문자열 및 작업 세부 정보를 사용 하 여 IoT Edge를 구성 합니다. 이 명령에 의해 생성 된 대부분의 정보는 iotedge\config.yaml 파일에 저장 됩니다. 초기화 명령은 이러한 공통 매개 변수를 허용 합니다. 전체 목록은 명령을 사용 `Get-Help Initialize-IoTEdge -full` 합니다.

| 매개 변수 | 허용되는 값 | 주석 |
| --------- | --------------- | -------- |
| **ManualConnectionString** | 없음 | **스위치 매개 변수**입니다. **기본값**입니다. 프로 비전 유형이 지정 되지 않은 경우 연결 문자열을 사용한 수동 프로비저닝이 기본값입니다.<br><br>장치를 수동으로 프로 비전 하는 장치 연결 문자열을 제공 하도록 선언 합니다. |
| **ManualX509** | 없음 | **스위치 매개 변수**입니다. 프로 비전 유형이 지정 되지 않은 경우 연결 문자열을 사용한 수동 프로비저닝이 기본값입니다.<br><br>사용자가 장치를 수동으로 프로 비전 하기 위해 id 인증서 및 개인 키를 제공 하도록 선언 합니다.
| **DpsTpm** | 없음 | **스위치 매개 변수**입니다. 프로 비전 유형이 지정 되지 않은 경우 연결 문자열을 사용한 수동 프로비저닝이 기본값입니다.<br><br>DPS(Device Provisioning Service) 범위 ID 및 디바이스의 등록 ID를 제공하여 DPS를 통해 프로비전할 것을 선언합니다.  |
| **DpsSymmetricKey** | 없음 | **스위치 매개 변수**입니다. 프로 비전 유형이 지정 되지 않은 경우 연결 문자열을 사용한 수동 프로비저닝이 기본값입니다.<br><br>는 dps (장치 프로 비전 서비스) 범위 ID와 DPS를 통해 프로 비전 하는 장치의 등록 ID를 증명에 대 한 대칭 키와 함께 제공 한다는 것을 선언 합니다. |
| **DpsX509** | 없음 | **스위치 매개 변수**입니다. 프로 비전 유형이 지정 되지 않은 경우 연결 문자열을 사용한 수동 프로비저닝이 기본값입니다.<br><br>Dps (장치 프로 비전 서비스) 범위 ID와 DPS를 통해 프로 비전 할 장치의 등록 ID (x.509 id 인증서 및 증명에 대 한 개인 키와 함께)를 제공 하도록 선언 합니다.  |
| **DeviceConnectionString** | IoT Hub에 등록된 IoT Edge 디바이스의 연결 문자열을 작은따옴표 안에 표시합니다. | 연결 문자열을 사용 하 여 수동으로 프로 비전 하는 **데 필요** 합니다. 스크립트 매개 변수에 연결 문자열을 제공 하지 않으면 매개 변수에 대 한 메시지가 표시 됩니다. |
| **IotHubHostName** | 장치가 연결 하는 IoT hub의 호스트 이름입니다. | X.509 인증서를 사용 하 여 수동으로 프로 비전 하는 **데 필요** 합니다. *{Hub 이름}. azure-devices.net*형식을 사용 합니다. |
| **DeviceId** | IoT Hub 등록 된 장치 id의 장치 ID입니다. | X.509 인증서를 사용 하 여 수동으로 프로 비전 하는 **데 필요** 합니다. |
| **ScopeId** | IoT Hub와 연결된 Device Provisioning Service 인스턴스의 범위 ID입니다. | DPS 프로 비전에 **필요** 합니다. 스크립트 매개 변수에서 범위 ID를 제공 하지 않으면 해당 ID를 입력 하 라는 메시지가 표시 됩니다. |
| **RegistrationId** | 디바이스에서 생성된 등록 ID입니다. | TPM 또는 대칭 키 증명을 사용 하는 경우 DPS 프로 비전에 **필요** 합니다. X.509 인증서 증명을 사용 하는 경우 **선택 사항** 입니다. |
| **X509IdentityCertificate** | 장치의 x.509 장치 id 인증서에 대 한 URI 경로입니다. | X.509 인증서 증명을 사용 하는 경우 수동 또는 DPS 프로 비전 중 하나에 **필요** 합니다. |
| **X509IdentityPrivateKey** | 장치의 x.509 장치 id 인증서 키에 대 한 URI 경로입니다. | X.509 인증서 증명을 사용 하는 경우 수동 또는 DPS 프로 비전 중 하나에 **필요** 합니다. |
| **SymmetricKey** | DPS를 사용할 때 IoT Edge 장치 id를 프로 비전 하는 데 사용 되는 대칭 키입니다. | 대칭 키 증명을 사용 하는 경우 DPS 프로 비전에 **필요** 합니다. |
| **ContainerOs** | **Windows** 또는 **Linux** | 컨테이너 운영 체제가 지정 되지 않은 경우 Windows가 기본값입니다.<br><br>Windows 컨테이너의 경우 IoT Edge는 설치에 포함 된 Moby 컨테이너 엔진을 사용 합니다. Linux 컨테이너의 경우 설치를 시작하기 전에 컨테이너 엔진을 설치해야 합니다. |
| **DeviceCACertificate** | 장치의 x.509 장치 CA 인증서에 대 한 URI 경로입니다. | 는 파일에서 구성할 수도 있습니다 `C:\ProgramData\iotedge\config.yaml` . 자세한 내용은 [IoT Edge 장치에서 인증서 관리](how-to-manage-device-certificates.md)를 참조 하세요. |
| **DeviceCAPrivateKey** | 장치의 x.509 장치 CA 개인 키에 대 한 URI 경로입니다. | 는 파일에서 구성할 수도 있습니다 `C:\ProgramData\iotedge\config.yaml` . 자세한 내용은 [IoT Edge 장치에서 인증서 관리](how-to-manage-device-certificates.md)를 참조 하세요. |
| **InvokeWebRequestParameters** | 매개 변수 및 값의 해시 테이블입니다. | 설치 중에 여러 개의 웹 요청이 생성됩니다. 이 필드를 사용하여 해당 웹 요청에 대한 매개 변수를 설정합니다. 이 매개 변수는 프록시 서버에 대한 자격 증명을 구성하는 데 유용합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **AgentImage** | IoT Edge 에이전트 이미지 URI | 기본적으로 새 IoT Edge 설치는 IoT Edge 에이전트 이미지에 대한 최신 롤링 태그를 사용합니다. 이 매개 변수를 사용하여 이미지 버전에 대한 특정 태그를 설정하거나 사용자 고유의 에이전트 이미지를 제공합니다. 자세한 내용은 [IoT Edge 태그 이해](how-to-update-iot-edge.md#understand-iot-edge-tags)를 참조하세요. |
| **사용자 이름** | 컨테이너 레지스트리 사용자 이름입니다. | -AgentImage 매개 변수를 프라이빗 레지스트리의 컨테이너로 설정한 경우에만 이 매개 변수를 사용합니다. 레지스트리에 대한 액세스 권한이 있는 사용자 이름을 제공합니다. |
| **암호** | 보안 암호 문자열입니다. | -AgentImage 매개 변수를 프라이빗 레지스트리의 컨테이너로 설정한 경우에만 이 매개 변수를 사용합니다. 레지스트리에 액세스하기 위한 암호를 제공합니다. |

## <a name="update-iotedge"></a>Update-IoTEdge

| 매개 변수 | 허용되는 값 | 주석 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 또는 **Linux** | 컨테이너 OS가 지정 되지 않은 경우 기본값은 Windows입니다. Windows 컨테이너의 경우 컨테이너 엔진이 설치에 포함됩니다. Linux 컨테이너의 경우 설치를 시작하기 전에 컨테이너 엔진을 설치해야 합니다. |
| **프록시** | 프록시 URL | 디바이스가 프록시 서버를 통해 인터넷에 연결해야 하는 경우 이 매개 변수를 포함합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **InvokeWebRequestParameters** | 매개 변수 및 값의 해시 테이블입니다. | 설치 중에 여러 개의 웹 요청이 생성됩니다. 이 필드를 사용하여 해당 웹 요청에 대한 매개 변수를 설정합니다. 이 매개 변수는 프록시 서버에 대한 자격 증명을 구성하는 데 유용합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **OfflineInstallationPath** | 디렉터리 경로 | 이 매개 변수를 포함 하는 경우 설치 관리자는 설치에 필요한 IoT Edge cab 및 VC Runtime MSI 파일에 대해 나열 된 디렉터리를 확인 합니다. 디렉터리에서 찾을 수 없는 파일이 다운로드 됩니다. 두 파일이 모두 디렉터리에 있으면 인터넷에 연결 하지 않고 IoT Edge를 설치할 수 있습니다. 이 매개 변수를 사용 하 여 특정 버전을 사용할 수도 있습니다. |
| **RestartIfNeeded** | 없음 | 이 플래그를 사용 하면 필요에 따라 메시지를 표시 하지 않고 배포 스크립트에서 컴퓨터를 다시 시작할 수 있습니다. |

## <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| 매개 변수 | 허용되는 값 | 주석 |
| --------- | --------------- | -------- |
| **설정** | 없음 | 이전 제거 시도가 실패 한 경우이 플래그는 제거를 강제로 수행 합니다.
| **RestartIfNeeded** | 없음 | 필요한 경우이 플래그를 사용 하 여 컴퓨터를 다시 시작 하 라는 메시지를 표시 하지 않고 제거할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

다음 문서에서 이러한 명령을 사용 하는 방법을 알아봅니다.

* [Azure IoT Edge 런타임 설치 또는 제거](how-to-install-iot-edge.md)
* [대칭 키 인증을 사용 하 여 Azure IoT Edge 장치 프로 비전](how-to-manual-provision-symmetric-key.md)
* [X.509 인증서 인증을 사용 하 여 Azure IoT Edge 장치 프로 비전](how-to-manual-provision-x509.md)
