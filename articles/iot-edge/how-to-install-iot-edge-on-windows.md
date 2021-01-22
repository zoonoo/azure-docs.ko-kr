---
title: Windows에서 Linux 용 Azure IoT Edge 설치 | Microsoft Docs
description: Windows 장치에 대 한 설치 지침 Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: e603e99c1aa9c49d20bdb281bbfd96131ae2efdd
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663624"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>Windows 장치에 Linux 용 Azure IoT Edge 설치 및 프로 비전 (미리 보기)

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 PC 클래스에서 산업용 서버로 장치에 배포할 수 있습니다. 디바이스가 IoT Edge 런타임을 사용하여 구성되면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다. 자세히 알아보려면 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조 하세요.

Windows에서 Linux에 대 한 Azure IoT Edge를 사용 하면 Linux 가상 머신을 사용 하 여 Windows 장치에서 Azure IoT Edge를 사용할 수 있습니다. Linux 버전의 Azure IoT Edge와 함께 배포 된 Linux 모듈은 가상 머신에서 실행 됩니다. 여기에서 Windows 응용 프로그램 및 코드와 IoT Edge 런타임 및 모듈은 서로 자유롭게 상호 작용할 수 있습니다.

이 문서에는 Windows 장치에서 IoT Edge을 설정 하는 단계가 나와 있습니다. 이러한 단계는 Windows 장치에서 실행할 IoT Edge 런타임을 포함 하는 Linux 가상 컴퓨터를 배포한 다음 장치를 IoT Hub 장치 id로 프로 비전 합니다.

>[!NOTE]
>Windows에서 Linux에 대 한 IoT Edge는 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)상태입니다.

## <a name="prerequisites"></a>필수 구성 요소

* 유효한 구독이 있는 Azure 계정. [Azure 구독](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).

* 다음 최소 시스템 요구 사항을 충족 하는 Windows 장치:

  * Windows 10 버전 1809 이상 빌드 17763 이상
  * Professional, Enterprise 또는 Server edition
  * 최소 RAM: 4gb (권장 8gb)
  * 최소 저장소: 10gb

* Windows 관리 센터에 대 한 Azure IoT Edge 확장을 설치 하 여 Windows 관리 센터 insider build에 액세스 합니다.  <!-- The link below needs the language localization to work; otherwise broken -->
   1. [Windows Insider preview](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewserver)를 방문 합니다.

   1. 미리 보기 드롭다운에서 **Windows 관리 센터 미리 보기-빌드 2012** 를 선택 하 고 **확인** 을 선택 합니다.

      ![사용 가능한 미리 보기의 드롭다운 메뉴에서 Windows 관리 센터 미리 보기-빌드 2012를 선택 합니다.](./media/how-to-install-iot-edge-on-windows/select-windows-admin-center-preview-build.png)

   1. **언어 선택** 드롭다운에서 **영어** 를 선택 하 고 **확인** 을 선택 합니다.

   1. **지금 다운로드** 를 선택 하 여 *WindowsAdminCenterPreview2012.msi* 을 다운로드 합니다.

   1. *WindowsAdminCenterPreview2012.msi* 를 실행 하 고 설치 마법사 프롬프트에 따라 Windows 관리 센터를 설치 합니다. 설치 되 면 Windows 관리 센터를 엽니다.

   1. Windows 관리 센터를 처음 사용할 때 사용할 인증서를 선택 하 라는 메시지가 표시 됩니다. 인증서로 **Windows 관리 센터 클라이언트** 를 선택 합니다.

   1. Azure IoT Edge 확장을 설치할 때입니다. Windows 관리 센터 대시보드의 오른쪽 위에 있는 기어 아이콘을 선택 합니다.

      ![대시보드의 오른쪽 위에 있는 기어 아이콘을 선택 하 여 설정에 액세스 합니다.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. **설정** 메뉴의 **게이트웨이** 아래에서 **확장** 을 선택 합니다.

   1. **피드** 탭을 선택 하 고 **추가** 를 선택 합니다.

   1. https://aka.ms/wac-insiders-feed텍스트 상자에를 입력 하 고 **추가** 를 선택 합니다.

   1. 피드가 추가 된 후 **사용 가능한 확장** 탭으로 이동 합니다. 확장 목록을 업데이트 하는 데 시간이 걸릴 수 있습니다.

   1. **사용 가능한 확장** 탭의 확장 목록에서 **Azure IoT Edge** 을 찾습니다. 이를 선택 하 고 확장 목록 위의 **설치** 프롬프트를 선택 합니다.

   1. 설치가 완료 되 **면 설치 된 확장 탭의** 설치 된 확장 목록에 Azure IoT Edge 표시 됩니다.

## <a name="choose-your-provisioning-method"></a>프로 비전 방법 선택

Windows에서 Linux에 대 한 Azure IoT Edge는 다음과 같은 프로 비전 방법을 지원 합니다.

* IoT Edge 장치의 연결 문자열을 사용 하 여 수동으로 프로 비전 합니다. 이 방법을 사용 하려면 장치를 등록 하 고 [IoT Hub에 IoT Edge 장치 등록](how-to-register-device.md)의 단계를 사용 하 여 연결 문자열을 검색 합니다.
  * X.509 자체 서명 된 인증서는 현재 Windows의 Linux IoT Edge에서 지원 되지 않으므로 대칭 키 인증 옵션을 선택 합니다.
* DPS (장치 프로 비전 서비스) 및 대칭 키를 사용 하 여 자동으로 프로 비전 합니다. [DPS 및 대칭 키를 사용 하 여 IoT Edge 장치를 만들고 프로 비전 하는](how-to-auto-provision-symmetric-keys.md)방법에 대해 자세히 알아보세요.
* DPS 및 x.509 인증서를 사용 하 여 자동 프로 비전. [DPS 및 x.509 인증서를 사용 하 여 IoT Edge 장치를 만들고 프로 비전 하는](how-to-auto-provision-x509-certs.md)방법에 대해 자세히 알아보세요.

몇 가지 장치로 수동 프로 비전을 시작 하는 것이 더 쉽습니다. 장치 프로 비전 서비스는 많은 장치를 프로 비전 하는 데 유용 합니다.

DPS 방법 중 하나를 사용 하 여 장치 또는 장치를 프로 비전 하려는 경우 위에 링크 된 해당 문서의 단계에 따라 DPS 인스턴스를 만들고 DPS 인스턴스를 IoT Hub 연결 하 고 DPS 등록을 만듭니다. 단일 장치 또는 장치 그룹에 대 한 *그룹 등록* 에 대 한 *개별 등록* 을 만들 수 있습니다. 등록 유형에 대 한 자세한 내용은 [Azure IoT Hub 장치 프로 비전 서비스 개념](https://docs.microsoft.com/azure/iot-dps/concepts-service#enrollment)을 참조 하세요.

## <a name="create-a-new-deployment"></a>새 배포 만들기

대상 장치의 Windows에서 Linux 용 Azure IoT Edge 배포를 만듭니다.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Windows 관리 센터 시작 페이지의 연결 목록 아래에서 Windows 관리 센터를 실행 하는 PC를 나타내는 로컬 호스트 연결이 표시 됩니다. 관리 하는 모든 추가 서버, Pc 또는 클러스터도 여기에 표시 됩니다.

Windows 관리 센터를 사용 하 여 로컬 장치나 원격 관리 장치에서 Windows에 Linux 용 Azure IoT Edge를 설치 하 고 관리할 수 있습니다. 이 가이드에서는 Windows에서 Linux 용 Azure IoT Edge를 배포 하기 위한 대상 장치로 로컬 호스트 연결이 제공 됩니다.

로컬 장치 대신 원격 대상 장치에 배포 하려는 경우 목록에 원하는 대상 장치가 표시 되지 않으면 지침에 따라 [장치를 추가 합니다.](https://docs.microsoft.com/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)

   ![대상 장치가 나열 된 초기 Windows 관리 센터 대시보드](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. **추가** 를 선택합니다.

1. **리소스 추가 또는 만들기** 창에서 **Azure IoT Edge** 타일을 찾습니다. 장치에서 Windows에 Linux 용 Azure IoT Edge의 새 인스턴스를 설치 하려면 **새로 만들기** 를 선택 합니다.

   장치에서 실행 되는 Windows에서 Linux에 대 한 IoT Edge 이미 있는 경우 **추가** 를 선택 하 여 기존 IoT Edge 장치에 연결 하 고 Windows 관리 센터에서 해당 장치를 관리할 수 있습니다.

   ![Windows 관리 센터의 Azure IoT Edge 타일에서 새로 만들기를 선택 합니다.](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. **Windows 배포에서 Linux에 대 한 Azure IoT Edge 만들기** 창이 열립니다. **1. 시작** 탭에서 대상 장치가 최소 요구 사항을 충족 하는지 확인 하 고 **다음** 을 선택 합니다.

1. 사용 약관을 검토 하 고 **동의 함** 을 선택한 후 **다음** 을 선택 합니다.

1. 기본 설정에 따라 **선택적 진단 데이터** 를 설정 하거나 해제할 수 있습니다.

1. **다음: 배포** 를 선택 합니다.

   ![선택적인 진단 데이터를 기본 설정으로 전환 하 고 나 서 다음: 배포 단추를 선택 합니다.](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. **2. 배포** 탭의 **대상 장치 선택** 에서 나열 된 장치를 클릭 하 여 최소 요구 사항을 충족 하는지 확인 합니다. 상태가 지원 됨으로 확인 되 면 **다음** 을 선택 합니다.

   ![장치를 선택 하 여 지원 되는지 확인 합니다.](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. **2.2 설정** 탭에서 기본 설정을 적용 합니다.

1. **2.3 배포** 탭에서 배포 진행률을 볼 수 있습니다. 전체 프로세스에는 Windows 용 Linux 패키지에 대 한 Azure IoT Edge 다운로드, 패키지 설치, 호스트 장치 구성 및 Linux 가상 머신 설정이 포함 됩니다. 이 프로세스를 완료 하는 데 몇 분 정도 걸릴 수 있습니다. 성공적인 배포는 다음과 같습니다.

   ![성공적으로 배포 되 면 각 단계에 녹색 확인 표시와 ' 완료 ' 레이블이 표시 됩니다.](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

배포가 완료 되 면 장치를 프로 비전 할 준비가 된 것입니다. **다음: 연결** 을 선택 하 여 3 단계로 이동 **합니다. 연결** 탭-장치 프로 비전 Azure IoT Edge를 처리 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Windows에서 Linux에 대 한 IoT Edge를 대상 장치에 설치 합니다 (아직 설치 하지 않은 경우).

> [!NOTE]
> 다음 PowerShell 프로세스는 Windows에서 Linux 용 Azure IoT Edge의 로컬 호스트 배포를 만드는 방법을 간략하게 설명 합니다. PowerShell을 사용 하 여 원격 대상 장치에 대 한 배포를 만들려면 원격 [powershell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_remote) 을 사용 하 여 원격 장치에 대 한 연결을 설정 하 고 해당 장치에서 이러한 명령을 원격으로 실행할 수 있습니다.

1. 관리자 권한 PowerShell 세션에서 다음 명령을 각각 실행 하 여 Windows에서 Linux에 대 한 IoT Edge를 다운로드 합니다.

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzureEdgeForLinuxOnWindowsMSI" -OutFile $msiPath
   ```

1. 장치의 Windows에 Linux 용 IoT Edge를 설치 합니다.

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > 위의 install 명령에 INSTALLDIR = "<FULLY_QUALIFIED_PATH>" 및 VHDXDIR = "<FULLY_QUALIFIED_PATH>" 매개 변수를 추가 하 여 Windows 설치 및 VHDX 디렉터리에서 Linux 용 사용자 지정 IoT Edge를 지정할 수 있습니다.

1. 배포를 성공적으로 실행 하려면 대상 장치에 대 한 실행 정책을 `AllSigned` 아직 설정 하지 않은 경우로 설정 해야 합니다. 다음을 사용 하 여 관리자 권한 PowerShell 프롬프트에서 현재 실행 정책을 확인할 수 있습니다.

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   의 실행 정책이 `local machine` 이 아닌 경우 `AllSigned` 다음을 사용 하 여 실행 정책을 설정할 수 있습니다.

   ```azurepowershell-interactive
   Set-ExecutionPolicy - ExecutionPolicy AllSigned -Force
   ```

1. Windows 배포에서 Linux에 대 한 IoT Edge를 만듭니다.

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   <!-- Most likely temporary until cmdlet is fully documented -->
   > [!NOTE]
   > 매개 변수 없이이 명령을 실행 하거나 선택적으로 매개 변수를 사용 하 여 배포를 사용자 지정할 수 있습니다. PowerShell 모듈 AzureEFLOW. .psm1를 검사 하 여 매개 변수 및 해당 의미를 확인 합니다 (C:\Program Files\WindowsPowerShell\Modules\AzureEFLOW 아래 참조).

1. 사용 조건에 동의 하려면 ' Y '를 입력 하십시오.

1. 기본 설정에 따라 **선택적 진단 데이터** 를 설정 하거나 해제 하려면 ' O ' 또는 ' R '을 입력 합니다. 성공적인 배포는 다음과 같습니다.

   ![성공적으로 배포 되 면 메시지 끝에 ' 배포 성공 ' 이라고 표시 됩니다.](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

배포가 완료 되 면 장치를 프로 비전 할 준비가 된 것입니다.

---

장치를 프로 비전 하려면 아래 링크를 따라 선택한 프로 비전 방법에 대 한 섹션으로 이동할 수 있습니다.

* [옵션 1: IoT Edge 장치의 연결 문자열을 사용 하 여 수동 프로 비전](#option-1-provisioning-manually-using-the-connection-string)
* [옵션 2: DPS (장치 프로 비전 서비스) 및 대칭 키를 사용 하 여 자동 프로 비전](#option-2-provisioning-via-dps-using-symmetric-keys)
* [옵션 3: DPS 및 x.509 인증서를 사용 하 여 자동 프로 비전](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>장치 프로 비전

장치를 프로 비전 하는 방법을 선택 하 고 해당 섹션의 지침을 따릅니다. Windows 관리 센터 또는 관리자 권한 PowerShell 세션을 사용 하 여 장치를 프로 비전 할 수 있습니다.

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>옵션 1: 연결 문자열을 사용 하 여 수동으로 프로 비전

이 섹션에서는 Azure IoT Edge 장치의 연결 문자열을 사용 하 여 수동으로 장치를 프로 비전 하는 방법을 설명 합니다.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **Azure IoT Edge 장치 프로 비전** 창의 프로 비전 방법 드롭다운에서 **연결 문자열 (수동)** 을 선택 합니다.

1. [Azure Portal](https://ms.portal.azure.com/)에서 IoT Hub의 **IoT Edge** 탭으로 이동 합니다.

1. 장치의 장치 ID를 클릭 합니다. **기본 연결 문자열** 필드를 복사 합니다.

1. Windows 관리 센터의 장치 연결 문자열 필드에 붙여넣습니다. 그런 다음 **선택한 방법으로 프로 비전** 을 선택 합니다.

   ![장치의 연결 문자열을 붙여넣은 후 선택한 방법으로 프로 비전 선택](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. 프로 비전이 완료 되 면 **마침** 을 선택 합니다. 그러면 기본 대시보드로 돌아갑니다. 이제 형식이 인 새 장치가 표시 됩니다 `IoT Edge Devices` . IoT Edge 장치를 선택 하 여 연결할 수 있습니다. **개요** 페이지에서 장치 **IoT Edge 모듈 목록** 및 **IoT Edge 상태** 를 볼 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. [Azure Portal](https://ms.portal.azure.com/)에서 IoT Hub의 **IoT Edge** 탭으로 이동 합니다.

1. 장치의 장치 ID를 클릭 합니다. **기본 연결 문자열** 필드를 복사 합니다.

1. 다음 명령에서 자리 표시자 텍스트를 붙여넣고 대상 장치의 관리자 권한 PowerShell 세션에서 실행 합니다.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>옵션 2: 대칭 키를 사용 하 여 DPS를 통해 프로 비전

이 섹션에서는 DPS 및 대칭 키를 사용 하 여 장치를 자동으로 프로 비전 하는 방법을 다룹니다.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **Azure IoT Edge 장치 프로 비전** 창의 프로 비전 방법 드롭다운에서 [ **대칭 키 (DPS])** 를 선택 합니다.

1. [Azure Portal](https://ms.portal.azure.com/)에서 DPS 인스턴스로 이동 합니다.

1. **개요** 탭에서 **ID 범위** 값을 복사 합니다. Windows 관리 센터의 범위 ID 필드에 붙여넣습니다.

1. Azure Portal **관리 등록** 탭에서 사용자가 만든 등록을 선택 합니다. 등록 세부 정보에서 **기본 키** 값을 복사 합니다. Windows 관리 센터의 대칭 키 필드에 붙여넣습니다.

1. Windows 관리 센터의 등록 ID 필드에 장치의 등록 ID를 제공 합니다.

1. **선택한 방법으로 프로 비전을** 선택 합니다.

   ![대칭 키 프로 비전에 필요한 필드를 채운 후 선택한 방법으로 프로 비전을 선택 합니다.](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. 프로 비전이 완료 되 면 **마침** 을 선택 합니다. 그러면 기본 대시보드로 돌아갑니다. 이제 형식이 인 새 장치가 표시 됩니다 `IoT Edge Devices` . IoT Edge 장치를 선택 하 여 연결할 수 있습니다. **개요** 페이지에서 장치 **IoT Edge 모듈 목록** 및 **IoT Edge 상태** 를 볼 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 다음 명령을 텍스트 편집기에 복사합니다. 자리 표시자 텍스트를 세부 정보로 바꿉니다.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. [Azure Portal](https://ms.portal.azure.com/)에서 DPS 인스턴스로 이동 합니다.

1. **개요** 탭에서 **ID 범위** 값을 복사 합니다. 명령의 적절 한 자리 표시자 텍스트 위에 붙여넣습니다.

1. Azure Portal **관리 등록** 탭에서 사용자가 만든 등록을 선택 합니다. 등록 세부 정보에서 **기본 키** 값을 복사 합니다. 명령의 적절 한 자리 표시자 텍스트 위에 붙여넣습니다.

1. 명령의 적절 한 자리 표시자 텍스트를 바꿀 장치의 등록 ID를 제공 합니다.

1. 대상 장치의 관리자 권한 PowerShell 세션에서 명령을 실행 합니다.

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>옵션 3: x.509 인증서를 사용 하 여 DPS를 통해 프로 비전

이 섹션에서는 DPS 및 x.509 인증서를 사용 하 여 장치를 자동으로 프로 비전 하는 방법을 설명 합니다.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **Azure IoT Edge 장치 프로 비전** 창의 프로 비전 방법 드롭다운에서 **X.509 Certificate (DPS)** 를 선택 합니다.

1. [Azure Portal](https://ms.portal.azure.com/)에서 DPS 인스턴스로 이동 합니다.

1. **개요** 탭에서 **ID 범위** 값을 복사 합니다. Windows 관리 센터의 범위 ID 필드에 붙여넣습니다.

1. Windows 관리 센터의 등록 ID 필드에 장치의 등록 ID를 제공 합니다.

1. 인증서 및 개인 키 파일을 업로드 합니다.

1. **선택한 방법으로 프로 비전을** 선택 합니다.

   ![X.509 인증서 프로 비전에 필요한 필드를 입력 한 후 선택한 방법으로 프로 비전을 선택 합니다.](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. 프로 비전이 완료 되 면 **마침** 을 선택 합니다. 그러면 기본 대시보드로 돌아갑니다. 이제 형식이 인 새 장치가 표시 됩니다 `IoT Edge Devices` . IoT Edge 장치를 선택 하 여 연결할 수 있습니다. **개요** 페이지에서 장치 **IoT Edge 모듈 목록** 및 **IoT Edge 상태** 를 볼 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 다음 명령을 텍스트 편집기에 복사합니다. 자리 표시자 텍스트를 세부 정보로 바꿉니다.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocWin <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. [Azure Portal](https://ms.portal.azure.com/)에서 DPS 인스턴스로 이동 합니다.

1. **개요** 탭에서 **ID 범위** 값을 복사 합니다. 명령의 적절 한 자리 표시자 텍스트 위에 붙여넣습니다.

1. 명령의 적절 한 자리 표시자 텍스트를 바꿀 장치의 등록 ID를 제공 합니다.

1. 적절 한 자리 표시자 텍스트를 인증서 파일의 절대 원본 경로로 바꿉니다.

1. 적절 한 자리 표시자 텍스트를 개인 키 파일의 절대 원본 경로로 바꿉니다.

1. 대상 장치의 관리자 권한 PowerShell 세션에서 명령을 실행 합니다.

---

## <a name="verify-successful-configuration"></a>성공적인 구성 확인

Windows에서 Linux에 대 한 IoT Edge이 IoT Edge 장치에 성공적으로 설치 및 구성 되었는지 확인 합니다.

1. Windows 관리 센터의 연결 된 장치 목록에서 IoT Edge 장치를 선택 하 여 연결 합니다.
1. 장치 개요 페이지에는 장치에 대 한 일부 정보가 표시 됩니다.

    1. **IoT Edge 모듈 목록** 섹션에는 장치에서 실행 중인 모듈이 표시 됩니다. IoT Edge 서비스를 처음 시작 하는 경우 실행 중인 **edgeAgent** 모듈만 표시 되어야 합니다. edgeAgent 모듈은 기본적으로 실행되며, 디바이스에 배포하는 추가 모듈을 설치하고 시작하는 데 도움이 됩니다.
    1. **IoT Edge 상태** 섹션에는 서비스 상태가 표시 되며 **활성 (실행 중)** 으로 보고 되어야 합니다.

1. IoT Edge 서비스의 문제를 해결 해야 하는 경우 장치 페이지의 **명령 셸** 도구를 사용 하 여 가상 머신에 ssh (보안 셸)를 사용 하 고 Linux 명령을 실행 합니다.

    1. 서비스 문제를 해결해야 할 경우 서비스 로그를 검색합니다.

       ```bash
       journalctl -u iotedge
       ```

    2. 도구를 사용 `check` 하 여 장치의 구성 및 연결 상태를 확인 합니다.

       ```bash
       sudo iotedge check
       ```

## <a name="next-steps"></a>다음 단계

장치에 모듈을 배포 하는 방법을 알아보려면 [IoT Edge 모듈 배포](how-to-deploy-modules-portal.md) 를 계속 진행 하세요.
