---
title: Windows의 Linux 용 Azure IoT Edge PowerShell 스크립트 | Microsoft Docs
description: Windows 가상 머신에서 Linux 용 Windows PowerShell 스크립트를 배포, 프로 비전 및 상태 IoT Edge 하는 Azure IoT Edge에 대 한 참조 정보입니다.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 02/16/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 5286362369e0c4881cdf0c56bc13d1d340056be1
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562514"
---
# <a name="powershell-scripts-for-iot-edge-for-linux-on-windows"></a>Windows에서 Linux 용 IoT Edge PowerShell 스크립트

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Windows 가상 머신에서 Linux 용 IoT Edge를 배포 하 고 프로 비전 하며 상태를 가져오는 PowerShell 스크립트를 이해 합니다.

이 문서에서 설명 하는 명령은 파일에서 가져온 것입니다 `AzureEFLOW.psm1` .이 파일은 아래의 디렉터리에 있는 시스템에서 찾을 수 있습니다 `WindowsPowerShell` `C:\Program Files\WindowsPowerShellModules\AzureEFLOW` .

## <a name="deploy-eflow"></a>Deploy-Eflow

**배포-Eflow** 명령은 기본 배포 방법입니다. 배포 명령은 가상 컴퓨터를 만들고, 파일을 프로 비전 하 고, IoT Edge 에이전트 모듈을 배포 합니다. 매개 변수가 필요 하지 않은 경우에는 해당 매개 변수를 사용 하 여 배포 중에 IoT Edge 장치를 프로 비전 하 고 생성 중에 가상 컴퓨터에 대 한 설정을 수정할 수 있습니다. 전체 목록은 명령을 사용 `Get-Help Deploy-Eflow -full` 합니다.  

>[!NOTE]
>프로 비전 형식의 경우 **x509** 는 현재 [Azure IoT Hub 장치 프로 비전 서비스](../iot-dps/about-iot-dps.md)를 사용 하 여 x509 프로 비전을 참조 합니다. 수동 X509 프로 비전 방법은 현재 지원 되지 않습니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| eflowVhdxDir | 디렉터리 경로 | 배포에서 VM에 대 한 VHDX 파일을 저장 하는 디렉터리입니다. |
| provisioningType | **수동**, **TPM**, **X509** 또는 **대칭** |  IoT Edge 장치에 사용 하려는 프로 비전의 유형을 정의 합니다. |
| Dev/문자열 | 기존 IoT Edge 장치의 장치 연결 문자열 | 수동으로 IoT Edge 장치를 프로 비전 하는 장치 연결 문자열입니다 (**수동**). |
| symmKey | 기존 DPS 등록의 기본 키 또는 대칭 키를 사용 하 여 등록 된 기존 IoT Edge 장치의 기본 키 | IoT Edge 장치 (**X509** 또는 **대칭**)를 프로 비전 하기 위한 대칭 키입니다. |
| scopeId | 기존 DPS 인스턴스의 범위 ID입니다. | IoT Edge 장치 (**X509** 또는 **대칭**)를 프로 비전 하는 범위 ID입니다. |
| registrationId | 기존 IoT Edge 장치의 등록 ID | IoT Edge 장치 (**X509** 또는 **대칭**)를 프로 비전 하기 위한 등록 ID입니다. |
| identityCertLocVm | 디렉터리 경로; 서비스에서 소유할 수 있는 폴더에 있어야 합니다. `iotedge` | IoT Edge 장치 (**X509** 또는 **대칭**)를 프로 비전 하기 위한 가상 머신의 id 인증서에 대 한 절대 대상 경로입니다. |
| identityCertLocWin | 디렉터리 경로 | IoT Edge 장치 (**X509** 또는 **대칭**)를 프로 비전 하기 위한 Windows의 id 인증서에 대 한 절대 원본 경로입니다. |
| identityPkLocVm |  | 디렉터리 경로; 서비스에서 소유할 수 있는 폴더에 있어야 합니다. `iotedge` | IoT Edge 장치 (**X509** 또는 **대칭**)를 프로 비전 하기 위한 가상 머신의 id 개인 키에 대 한 절대 대상 경로입니다. |
| identityPkLocWin | 디렉터리 경로 | IoT Edge 장치 (**X509** 또는 **대칭**)를 프로 비전 하기 위한 Windows의 id 개인 키에 대 한 절대 원본 경로입니다. |
| vmSizeDefintion | 30 자이 하 | 가상 컴퓨터의 코어 수와 사용 가능한 RAM을 정의 합니다. **기본값**: Standard_K8S_v1. |
| vmDiskSize | 8gb에서 256 GB 사이 | 동적 확장 가상 하드 디스크의 최대 디스크 크기입니다. **기본값**: 16gb |
| vmUser | 30 자이 하 | 가상 컴퓨터에 로그온 하기 위한 사용자 이름입니다. |
| vnetType | **투명** 또는 **ICS** | 가상 스위치의 유형입니다. **기본값**: 투명 합니다. |
| vnetName | 64 자를 초과 하지 않습니다. | 가상 스위치의 이름입니다. **기본값**: 외부. |
| enableVtpm | None | **스위치 매개 변수** 입니다. TPM을 사용 하거나 사용 하지 않도록 설정 하 여 가상 컴퓨터를 만듭니다. |
| mobyPackageVersion | 30 자이 하 |  가상 머신에서 확인 하거나 설치할 Moby 패키지의 버전입니다.  **기본값** 은 19.03.11입니다. |
| iotedgePackageVersion | 30 자이 하 | 가상 머신에서 확인 하거나 설치할 IoT Edge 패키지의 버전입니다. **기본값** 은 1.1.0입니다. |
| installPackages | None | **스위치 매개 변수** 입니다. 이 옵션이 설정 되어 있으면 스크립트가 패키지를 확인 하는 것이 아니라 Moby 및 IoT Edge 패키지를 설치 하려고 시도 합니다. |

## <a name="verify-eflowvm"></a>Verify-EflowVm

**Verify-EflowVm** 명령은 Windows의 Linux 가상 머신에 대 한 IoT Edge 만들어졌는지 확인 하는 노출 된 함수입니다. 일반 매개 변수만 사용 하 고 가상 컴퓨터를 만든 경우 **true** 를 반환 하 고 그렇지 않은 경우 **false** 를 반환 합니다. 추가 정보를 보려면 명령을 사용 `Get-Help Verify-EflowVm -full` 합니다.

## <a name="provision-eflowvm"></a>Provision-EflowVm

**프로 비전-EflowVm** 명령은 IoT Edge 장치에 대 한 프로 비전 정보를 가상 머신의 IoT Edge 파일에 추가 합니다 `config.yaml` . 배포 단계에서 배포 **-Eflow** 명령에 매개 변수를 설정 하 여 프로 비전을 수행할 수도 있습니다. 추가 정보를 보려면 명령을 사용 `Get-Help Provision-EflowVm -full` 합니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| vmUser | 30 자이 하 | 가상 컴퓨터에 로그온 하기 위한 사용자 이름입니다. |
| provisioningType | **수동**, **TPM**, **X509** 또는 **대칭** |  IoT Edge 장치에 사용 하려는 프로 비전의 유형을 정의 합니다. |
| Dev/문자열 | 기존 IoT Edge 장치의 장치 연결 문자열 | 수동으로 IoT Edge 장치를 프로 비전 하는 장치 연결 문자열입니다 (**수동**). |
| symmKey | 기존 DPS 등록의 기본 키 또는 대칭 키를 사용 하 여 등록 된 기존 IoT Edge 장치의 기본 키 | IoT Edge 장치 (**DPS**, **대칭**)를 프로 비전 하기 위한 대칭 키입니다. |
| scopeId | 기존 DPS 인스턴스의 범위 ID입니다. | **DPS**(IoT Edge 장치)를 프로 비전 하기 위한 범위 ID입니다. |
| registrationId | 기존 IoT Edge 장치의 등록 ID | **DPS**(IoT Edge 장치)를 프로 비전 하기 위한 등록 ID입니다. |
| identityCertLocVm | 디렉터리 경로; 서비스에서 소유할 수 있는 폴더에 있어야 합니다. `iotedge` | IoT Edge 장치 (**DPS**, **X509**)를 프로 비전 하기 위한 가상 머신의 id 인증서에 대 한 절대 대상 경로입니다. |
| identityCertLocWin | 디렉터리 경로 | IoT Edge 장치 (**dps**, **X509**)를 프로 비전 하기 위한 Windows의 id 인증서에 대 한 절대 원본 경로입니다. |
| identityPkLocVm |  | 디렉터리 경로; 서비스에서 소유할 수 있는 폴더에 있어야 합니다. `iotedge` | IoT Edge 장치 (**DPS**, **X509**)를 프로 비전 하기 위한 가상 머신의 id 개인 키에 대 한 절대 대상 경로입니다. |
| identityPkLocWin | 디렉터리 경로 | IoT Edge 장치 (**dps**, **X509**)를 프로 비전 하기 위한 Windows의 id 개인 키에 대 한 절대 원본 경로입니다. |

## <a name="get-eflowvmname"></a>Get-EflowVmName

**EflowVmName** 명령은 가상 컴퓨터의 현재 호스트 이름을 쿼리 하는 데 사용 됩니다. 이 명령은 Windows 호스트 이름이 시간이 지남에 따라 변경 될 수 있다는 사실을 고려 하기 위해 존재 합니다. 일반 매개 변수만 사용 하 고 가상 컴퓨터의 현재 호스트 이름을 반환 합니다. 추가 정보를 보려면 명령을 사용 `Get-Help Get-EflowVmName -full` 합니다.

## <a name="get-eflowlogs"></a>Get-EflowLogs

**Get EflowLogs** 명령은 Windows의 Linux 배포에 대 한 IoT Edge에서 로그를 수집 하 고 묶는 데 사용 됩니다. 이는 번들로 묶은 로그를 폴더 형식으로 출력 합니다 `.zip` . 추가 정보를 보려면 명령을 사용 `Get-Help Get-EflowLogs -full` 합니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| vmUser | 30 자이 하 | 가상 컴퓨터에 로그온 하기 위한 사용자 이름입니다. |

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

**EflowVmTpmProvisioningInfo** 명령은 가상 머신의 vtpm 프로 비전 정보를 수집 하 고 표시 하는 데 사용 됩니다. VTPM을 사용 하지 않고 가상 컴퓨터를 만든 경우이 명령은 TPM 프로 비전 정보를 찾을 수 없음을 반환 합니다. 추가 정보를 보려면 명령을 사용 `Get-Help Get-EflowVmTpmProvisioningInfo -full` 합니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| vmUser | 30 자이 하 | 가상 컴퓨터에 로그온 하기 위한 사용자 이름입니다. |

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

**Get EflowVmAddr** 명령은 가상 컴퓨터의 IP 주소와 MAC 주소를 찾아서 표시 하는 데 사용 됩니다. 일반 매개 변수만 사용 합니다. 추가 정보를 보려면 명령을 사용 `Get-Help Get-EflowVmAddr -full` 합니다.

## <a name="get-eflowvmsysteminformation"></a>Get-EflowVmSystemInformation

**EflowVmSystemInformation** 명령은 메모리 및 저장소 사용량과 같은 가상 컴퓨터에서 시스템 정보를 수집 하 고 표시 하는 데 사용 됩니다. 추가 정보를 보려면 명령을 사용 `Get-Help Get-EflowVmSystemInformation -full` 합니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| vmUser | 30 자이 하 | 가상 컴퓨터에 로그온 하기 위한 사용자 이름입니다. |

## <a name="get-eflowvmedgeinformation"></a>Get-EflowVmEdgeInformation

**EflowVmEdgeInformation** 명령은 가상 컴퓨터에서 실행 중인 IoT Edge 버전과 같은 가상 컴퓨터의 정보를 수집 하 고 IoT Edge 표시 하는 데 사용 됩니다. 추가 정보를 보려면 명령을 사용 `Get-Help Get-EflowVmEdgeInformation -full` 합니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| vmUser | 30 자이 하 | 가상 컴퓨터에 로그온 하기 위한 사용자 이름입니다. |

## <a name="get-eflowvmedgemodulelist"></a>Get-EflowVmEdgeModuleList

**EflowVmEdgeModuleList** 명령은 가상 머신에서 실행 되는 IoT Edge 모듈 목록을 쿼리하고 표시 하는 데 사용 됩니다. 추가 정보를 보려면 명령을 사용 `Get-Help Get-EflowVmEdgeModuleList -full` 합니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| vmUser | 30 자이 하 | 가상 컴퓨터에 로그온 하기 위한 사용자 이름입니다. |

## <a name="get-eflowvmedgestatus"></a>Get-EflowVmEdgeStatus

**EflowVmEdgeStatus** 명령은 가상 컴퓨터에서 IoT Edge 런타임의 상태를 쿼리하고 표시 하는 데 사용 됩니다. 추가 정보를 보려면 명령을 사용 `Get-Help Get-EflowVmEdgeStatus -full` 합니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| vmUser | 30 자이 하 | 가상 컴퓨터에 로그온 하기 위한 사용자 이름입니다. |

## <a name="get-eflowvmusername"></a>Get-EflowVmUserName

**Get EflowVmUserName** 명령은 레지스트리에서 가상 컴퓨터를 만드는 데 사용 된 가상 컴퓨터 사용자 이름을 쿼리하고 표시 하는 데 사용 됩니다. 일반 매개 변수만 사용 합니다. 추가 정보를 보려면 명령을 사용 `Get-Help Get-EflowVmUserName -full` 합니다.

## <a name="get-eflowvmsshkey"></a>Get-EflowVmSshKey

**EflowVmSshKey** 명령은 가상 컴퓨터에서 사용 하는 SSH 키를 쿼리하고 표시 하는 데 사용 됩니다. 일반 매개 변수만 사용 합니다. 추가 정보를 보려면 명령을 사용 `Get-Help Get-EflowVmSshKey -full` 합니다.

## <a name="ssh-eflowvm"></a>Ssh-EflowVm

**Ssh-EflowVm** 명령은 가상 컴퓨터에 ssh를 사용 하는 데 사용 됩니다. 가상 컴퓨터에 대 한 SSH를 허용 하는 유일한 계정은 가상 컴퓨터를 만든 사용자입니다. 추가 정보를 보려면 명령을 사용 `Get-Help Ssh-EflowVm -full` 합니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| vmUser | 30 자이 하 | 가상 컴퓨터에 로그온 하기 위한 사용자 이름입니다. |

## <a name="next-steps"></a>다음 단계

다음 문서에서 이러한 명령을 사용 하는 방법을 알아봅니다.

* [Windows에서 Linux 용 Azure IoT Edge 설치](./how-to-install-iot-edge-windows-on-windows.md)

* PowerShell을 통해 사용할 수 있는 모든 명령에 대 한 자세한 내용은 [Windows powershell 용 IoT Edge powershell 스크립트 참조](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) 를 참조 하세요.