---
title: Azure IoT Edge for Linux on Windows용 PowerShell 스크립트 | Microsoft Docs
description: IoT Edge for Linux on Windows 가상 머신을 배포, 프로비전하고 상태를 확인하는 Azure IoT Edge for Linux on Windows PowerShell 스크립트에 대한 참조 정보입니다.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 02/16/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a24b39107d8f78c049afa230fe678ec92852eeb0
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959690"
---
# <a name="powershell-scripts-for-iot-edge-for-linux-on-windows"></a>IoT Edge for Linux on Windows용 PowerShell 스크립트

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

IoT Edge for Linux on Windows 가상 머신을 배포, 프로비전하고 상태를 확인하는 PowerShell 스크립트를 이해합니다.

이 문서에 설명된 명령은 `AzureEFLOW.psm1` 파일에서 가져온 것입니다. 이 파일은 `C:\Program Files\WindowsPowerShellModules\AzureEFLOW` 아래의 `WindowsPowerShell` 디렉터리에 있는 시스템에서 찾을 수 있습니다.

## <a name="deploy-eflow"></a>Deploy-Eflow

**Deploy-Eflow** 명령은 기본 배포 방법입니다. 이 배포 명령은 가상 머신을 만들고, 파일을 프로비전하고, IoT Edge 에이전트 모듈을 배포합니다. 다음 매개 변수는 필수는 아니지만 배포 중에 IoT Edge 디바이스를 프로비전하고 생성 중에 가상 머신의 설정을 수정하는 데 사용할 수 있습니다. 전체 목록을 보려면 `Get-Help Deploy-Eflow -full` 명령을 사용합니다.  

>[!NOTE]
>프로비저닝 유형 **X509** 는 현재 [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md)를 사용한 X509 프로비저닝만 나타냅니다. 수동 X509 프로비저닝 방법은 현재 지원되지 않습니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| eflowVhdxDir | 디렉터리 경로 | 배포에서 VM에 대한 VHDX 파일을 저장하는 디렉터리입니다. |
| provisioningType | **수동**, **TPM**, **X509** 또는 **대칭** |  IoT Edge 디바이스에 사용하려는 프로비저닝의 유형을 정의합니다. |
| devConnString | 기존 IoT Edge 디바이스의 디바이스 연결 문자열 | 수동으로 IoT Edge 디바이스를 프로비저닝하기 위한 디바이스 연결 문자열입니다(**수동**). |
| symmKey | 기존 DPS 등록의 기본 키 또는 대칭 키를 사용하여 등록된 기존 IoT Edge 디바이스의 기본 키 | IoT Edge 디바이스를 프로비저닝하기 위한 대칭 키입니다(**X509** 또는 **대칭**). |
| scopeId | 기존 DPS 인스턴스의 범위 ID입니다. | IoT Edge 디바이스를 프로비저닝하기 위한 범위 ID입니다(**X509** 또는 **대칭**). |
| registrationId | 기존 IoT Edge 디바이스의 등록 ID | IoT Edge 디바이스를 프로비저닝하기 위한 등록 ID입니다(**X509** 또는 **대칭**). |
| identityCertLocVm | 디렉터리 경로로, `iotedge` 서비스에서 소유할 수 있는 폴더에 있어야 합니다. | IoT Edge 디바이스를 프로비저닝할 가상 머신의 ID 인증서 절대 대상 경로입니다(**X509** 또는 **대칭**). |
| identityCertLocWin | 디렉터리 경로 | IoT Edge 디바이스를 프로비저닝할 Windows의 ID 인증서 절대 원본 경로입니다(**X509** 또는 **대칭**). |
| identityPkLocVm | 디렉터리 경로로, `iotedge` 서비스에서 소유할 수 있는 폴더에 있어야 합니다. | IoT Edge 디바이스를 프로비저닝할 가상 머신의 ID 프라이빗 키 절대 대상 경로입니다(**X509** 또는 **대칭**). |
| identityPkLocWin | 디렉터리 경로 | IoT Edge 디바이스를 프로비저닝할 Windows의 ID 프라이빗 키 절대 원본 경로입니다(**X509** 또는 **대칭**). |
| vmSizeDefintion | 30자 이하 | 가상 머신의 코어 수 및 사용 가능한 RAM 정의입니다. **기본값**: Standard_K8S_v1. |
| vmDiskSize | 8GB에서 256GB 사이 | 동적으로 확장되는 가상 하드 디스크의 최대 디스크 크기입니다. **기본값**: 16GB. |
| vmUser | 30자 이하 | 가상 머신에 로그온하기 위한 사용자 이름입니다. |
| vnetType | **투명** 또는 **ICS** | 가상 스위치의 유형입니다. **기본값**: 투명. 투명은 외부 스위치를 나타내며, ICS는 내부 스위치를 나타냅니다. |
| vnetName | 64자 이하 | 가상 스위치의 이름입니다. **기본값**: 외부. |
| enableVtpm | 없음 | **매개 변수를 전환** 합니다. TPM이 사용하거나 사용하지 않도록 설정된 가상 머신을 만듭니다. |
| mobyPackageVersion | 30자 이하 |  가상 머신에서 확인하거나 설치할 Moby 패키지의 버전입니다.  **기본값:** 19.03.11. |
| iotedgePackageVersion | 30자 이하 | 가상 머신에서 확인하거나 설치할 IoT Edge 패키지의 버전입니다. **기본값:** 1.1.0. |
| installPackages | 없음 | **매개 변수를 전환** 합니다. 이 옵션이 설정되어 있으면 스크립트에서 패키지가 있는지 확인만 하는 것이 아니라 Moby 및 IoT Edge 패키지를 설치하려고 시도합니다. |

>[!NOTE]
>기본적으로 프로세스는 이름이 `External`인 외부 스위치를 찾을 수 없는 경우, IP 주소를 가져오는 데 사용할 기존 외부 스위치를 검색합니다. 사용할 수 있는 외부 스위치가 없는 경우 내부 스위치를 검색합니다. 사용할 수 있는 내부 스위치가 없는 경우 IP 주소를 가져오는 데 사용할 기본 스위치를 만들려고 시도합니다.

## <a name="verify-eflowvm"></a>Verify-EflowVm

**Verify-EflowVm** 명령은 IoT Edge for Linux on Windows 가상 머신이 생성되었는지 확인하기 위해 노출되는 함수입니다. 일반 매개 변수만 사용하며, 가상 머신이 생성되었으면 **true** 를 반환하고, 그렇지 않으면 **false** 를 반환합니다. 추가 정보를 보려면 `Get-Help Verify-EflowVm -full` 명령을 사용하세요.

## <a name="provision-eflowvm"></a>Provision-EflowVm

**Provision-EflowVm** 명령은 IoT Edge 디바이스에 대한 프로비저닝 정보를 가상 머신의 IoT Edge `config.yaml` 파일에 추가합니다. **Deploy-Eflow** 명령에 매개 변수를 설정하여 배포 단계 중에 프로비저닝을 수행할 수도 있습니다. 추가 정보를 보려면 `Get-Help Provision-EflowVm -full` 명령을 사용하세요.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| vmUser | 30자 이하 | 가상 머신에 로그온하기 위한 사용자 이름입니다. |
| provisioningType | **수동**, **TPM**, **X509** 또는 **대칭** |  IoT Edge 디바이스에 사용하려는 프로비저닝의 유형을 정의합니다. |
| devConnString | 기존 IoT Edge 디바이스의 디바이스 연결 문자열 | 수동으로 IoT Edge 디바이스를 프로비저닝하기 위한 디바이스 연결 문자열입니다(**수동**). |
| symmKey | 기존 DPS 등록의 기본 키 또는 대칭 키를 사용하여 등록된 기존 IoT Edge 디바이스의 기본 키 | IoT Edge 디바이스를 프로비저닝하기 위한 대칭 키입니다(**DPS** 또는 **대칭**). |
| scopeId | 기존 DPS 인스턴스의 범위 ID입니다. | IoT Edge 디바이스를 프로비저닝하기 위한 범위 ID입니다(**DPS**). |
| registrationId | 기존 IoT Edge 디바이스의 등록 ID | IoT Edge 디바이스를 프로비저닝하기 위한 등록 ID입니다(**DPS**). |
| identityCertLocVm | 디렉터리 경로로, `iotedge` 서비스에서 소유할 수 있는 폴더에 있어야 합니다. | IoT Edge 디바이스를 프로비저닝할 가상 머신의 ID 인증서 절대 대상 경로입니다(**DPS**, **X509**). |
| identityCertLocWin | 디렉터리 경로 | IoT Edge 디바이스를 프로비저닝할 Windows의 ID 인증서 절대 원본 경로입니다(**dps**, **X509**). |
| identityPkLocVm  | 디렉터리 경로로, `iotedge` 서비스에서 소유할 수 있는 폴더에 있어야 합니다. | IoT Edge 디바이스를 프로비저닝할 가상 머신의 ID 프라이빗 키 절대 대상 경로입니다(**DPS**, **X509**). |
| identityPkLocWin | 디렉터리 경로 | IoT Edge 디바이스를 프로비저닝할 Windows의 ID 프라이빗 키 절대 원본 경로입니다(**dps**, **X509**). |

## <a name="get-eflowvmname"></a>Get-EflowVmName

**Get-EflowVmName** 명령은 가상 머신의 현재 호스트 이름을 쿼리하는 데 사용됩니다. 이 명령은 Windows 호스트 이름이 시간이 지남에 따라 변경될 수 있다는 사실을 고려하기 위해 존재합니다. 일반 매개 변수만 사용하며 가상 머신의 현재 호스트 이름을 반환합니다. 추가 정보를 보려면 `Get-Help Get-EflowVmName -full` 명령을 사용하세요.

## <a name="get-eflowlogs"></a>Get-EflowLogs

**Get-EflowLogs** 명령은 IoT Edge for Linux on Windows에서 로그를 수집하고 번들로 묶는 데 사용됩니다. `.zip` 폴더 형식으로 번들로 묶인 로그를 출력합니다. 추가 정보를 보려면 `Get-Help Get-EflowLogs -full` 명령을 사용하세요.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| vmUser | 30자 이하 | 가상 머신에 로그온하기 위한 사용자 이름입니다. |

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

**Get-EflowVmTpmProvisioningInfo** 명령은 가상 머신의 vTPM 프로비저닝 정보를 수집하고 표시하는 데 사용됩니다. vTPM을 사용하지 않고 가상 머신을 만든 경우 이 명령은 TPM 프로비전 정보를 찾을 수 없다는 출력을 반환합니다. 추가 정보를 보려면 `Get-Help Get-EflowVmTpmProvisioningInfo -full` 명령을 사용하세요.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| vmUser | 30자 이하 | 가상 머신에 로그온하기 위한 사용자 이름입니다. |

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

**Get-EflowVmAddr** 명령은 가상 머신의 IP 및 MAC 주소를 찾아 표시하는 데 사용됩니다. 일반 매개 변수만 사용합니다. 추가 정보를 보려면 `Get-Help Get-EflowVmAddr -full` 명령을 사용하세요.

## <a name="get-eflowvmsysteminformation"></a>Get-EflowVmSystemInformation

**Get-EflowVmSystemInformation** 명령은 가상 머신에서 메모리 및 스토리지 사용량과 같은 시스템 정보를 수집하고 표시하는 데 사용됩니다. 추가 정보를 보려면 `Get-Help Get-EflowVmSystemInformation -full` 명령을 사용하세요.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| vmUser | 30자 이하 | 가상 머신에 로그온하기 위한 사용자 이름입니다. |

## <a name="get-eflowvmedgeinformation"></a>Get-EflowVmEdgeInformation

**Get-EflowVmEdgeInformation** 명령은 가상 머신에서 실행 중인 IoT Edge 버전과 같은 IoT Edge 정보를 가상 머신에서 수집하고 표시하는 데 사용됩니다. 추가 정보를 보려면 `Get-Help Get-EflowVmEdgeInformation -full` 명령을 사용하세요.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| vmUser | 30자 이하 | 가상 머신에 로그온하기 위한 사용자 이름입니다. |

## <a name="get-eflowvmedgemodulelist"></a>Get-EflowVmEdgeModuleList

**Get-EflowVmEdgeModuleList** 명령은 가상 머신에서 실행되는 IoT Edge 모듈 목록을 쿼리하고 표시하는 데 사용됩니다. 추가 정보를 보려면 `Get-Help Get-EflowVmEdgeModuleList -full` 명령을 사용하세요.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| vmUser | 30자 이하 | 가상 머신에 로그온하기 위한 사용자 이름입니다. |

## <a name="get-eflowvmedgestatus"></a>Get-EflowVmEdgeStatus

**EflowVmEdgeStatus** 명령은 가상 머신의 IoT Edge 런타임 상태를 쿼리하고 표시하는 데 사용됩니다. 추가 정보를 보려면 `Get-Help Get-EflowVmEdgeStatus -full` 명령을 사용하세요.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| vmUser | 30자 이하 | 가상 머신에 로그온하기 위한 사용자 이름입니다. |

## <a name="get-eflowvmusername"></a>Get-EflowVmUserName

**Get-EflowVmUserName** 명령은 레지스트리에서 가상 머신을 만드는 데 사용된 가상 머신 사용자 이름을 쿼리하고 표시하는 데 사용됩니다. 일반 매개 변수만 사용합니다. 추가 정보를 보려면 `Get-Help Get-EflowVmUserName -full` 명령을 사용하세요.

## <a name="get-eflowvmsshkey"></a>Get-EflowVmSshKey

**Get-EflowVmSshKey** 명령은 가상 머신에서 사용하는 SSH 키를 쿼리하고 표시하는 데 사용됩니다. 일반 매개 변수만 사용합니다. 추가 정보를 보려면 `Get-Help Get-EflowVmSshKey -full` 명령을 사용하세요.

## <a name="ssh-eflowvm"></a>Ssh-EflowVm

**Ssh-EflowVm** 명령은 가상 머신에 SSH로 연결하는 데 사용됩니다. 가상 머신에 SSH로 연결하도록 허용되는 유일한 계정은 가상 머신을 만든 사용자입니다. 추가 정보를 보려면 `Get-Help Ssh-EflowVm -full` 명령을 사용하세요.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| vmUser | 30자 이하 | 가상 머신에 로그온하기 위한 사용자 이름입니다. |

## <a name="next-steps"></a>다음 단계

다음 문서에서 이러한 명령을 사용하는 방법을 알아봅니다.

* [Azure IoT Edge for Linux on Windows 설치](./how-to-install-iot-edge-windows-on-windows.md)

* PowerShell을 통해 사용할 수 있는 모든 명령은 [IoT Edge for Linux on Windows PowerShell 스크립트 참조](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow)를 확인하세요.
