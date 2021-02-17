---
title: Azure Stack Edge Pro R 보안 | Microsoft Docs
description: 온-프레미스 및 클라우드에서 Azure Stack Edge Pro R 및 Azure Stack Edge 미니 R 장치, 서비스 및 데이터를 보호 하는 보안 및 개인 정보 보호 기능에 대해 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 922ef24bff7d6e60a3ba3b91113003f8206b8177
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548644"
---
# <a name="security-and-data-protection-for-azure-stack-edge-pro-r-and-azure-stack-edge-mini-r"></a>Azure Stack Edge Pro R 및 Azure Stack Edge 미니 R에 대 한 보안 및 데이터 보호

[!INCLUDE [applies-to-pro-r-and-mini-r--skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

새 기술을 채택 하는 경우, 특히 기술이 기밀 또는 소유 데이터와 함께 사용 되는 경우 보안은 중요 한 문제입니다. Edge Pro R 및 Azure Stack Edge 미니 R을 Azure Stack 권한이 있는 엔터티만 데이터를 확인, 수정 또는 삭제할 수 있습니다.

이 문서에서는 각 솔루션 구성 요소와 해당 구성 요소에 저장 된 데이터를 보호 하는 데 도움이 되는 Azure Stack Edge Pro R 및 Azure Stack Edge 미니 R 보안 기능을 설명 합니다.

솔루션은 서로 상호 작용 하는 네 가지 주요 구성 요소로 구성 됩니다.

- **Azure 공용 또는 Azure Government 클라우드에서 호스트 되는 Azure Stack Edge 서비스** 입니다. 장치 순서를 만들고 장치를 구성한 다음 완료에 대 한 순서를 추적 하는 데 사용 하는 관리 리소스입니다.
- **최첨단 견고한 장치를 Azure Stack** 합니다. 온-프레미스 데이터를 Azure 공용 또는 Azure Government 클라우드로 가져올 수 있도록 사용자에 게 제공 되는 견고한 물리적 장치입니다. 장치는 Edge Pro R 또는 Azure Stack Edge 미니 R Azure Stack 될 수 있습니다.
- **장치에 연결 된 클라이언트/호스트**. 인프라에서 장치에 연결 하 고 보호 해야 하는 데이터를 포함 하는 클라이언트입니다.
- **클라우드 저장소**. Azure cloud platform에서 데이터가 저장 되는 위치입니다. 이 위치는 일반적으로 사용자가 만드는 Azure Stack Edge 리소스에 연결 된 저장소 계정입니다.

## <a name="service-protection"></a>서비스 보호

Azure Stack Edge 서비스는 Azure에서 호스트 되는 관리 서비스입니다. 서비스는 장치를 구성 하 고 관리 하는 데 사용 됩니다.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## <a name="device-protection"></a>디바이스 보호

견고한 장치는 데이터를 로컬로 처리 한 다음 Azure에 전송 하 여 데이터를 변환 하는 데 도움이 되는 온-프레미스 장치입니다. 장치:

- Azure Stack Edge 서비스에 액세스 하려면 정품 인증 키가 필요 합니다.
- 는 장치 암호로 항상 보호 됩니다.
- 는 잠긴 장치입니다. 장치 베이스 보드 관리 컨트롤러 (BMC) 및 BIOS는 암호로 보호 됩니다. BMC는 제한 된 사용자 액세스를 통해 보호 됩니다.
- 에는 Microsoft에서 제공 하는 신뢰할 수 있는 소프트웨어만 사용 하 여 장치를 부팅 하는 보안 부팅이 사용 됩니다.
- Windows Defender 응용 프로그램 제어 (WDAC)를 실행 합니다. WDAC를 사용 하면 코드 무결성 정책에서 정의한 신뢰할 수 있는 응용 프로그램만 실행할 수 있습니다.
- 에는 하드웨어 기반의 보안 관련 기능을 수행 하는 신뢰할 수 있는 플랫폼 모듈 (TPM)이 있습니다. 특히 TPM은 장치에 유지 해야 하는 암호 및 데이터를 관리 하 고 보호 합니다.
- 장치에서 필요한 포트만 열리고 다른 모든 포트가 차단 됩니다. 자세한 내용은 [장치에 대 한 포트 요구 사항](azure-stack-edge-pro-r-system-requirements.md) 목록을 참조 하세요.
- 장치 하드웨어 및 소프트웨어에 대 한 모든 액세스가 기록 됩니다. 
    - 장치 소프트웨어의 경우 기본 방화벽 로그는 장치의 인바운드 및 아웃 바운드 트래픽에 대해 수집 됩니다. 이러한 로그는 지원 패키지에 번들로 제공 됩니다.
    - 장치 하드웨어의 경우 장치 섀시 열기 및 닫기와 같은 모든 장치 섀시 이벤트가 장치에 기록 됩니다.

    하드웨어 및 소프트웨어 침입 이벤트를 포함 하는 특정 로그와 로그를 가져오는 방법에 대 한 자세한 내용은 [고급 보안 로그 수집](azure-stack-edge-gpu-troubleshoot.md)을 참조 하세요.


### <a name="protect-the-device-via-activation-key"></a>정품 인증 키를 통해 장치 보호

권한 있는 Azure Stack Edge Pro R 또는 Azure Stack Edge 미니 R 장치만 Azure 구독에서 만든 Azure Stack Edge 서비스에 가입할 수 있습니다. 장치에 권한을 부여 하려면 정품 인증 키를 사용 하 여 Azure Stack Edge 서비스에서 장치를 활성화 해야 합니다.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

자세한 내용은 [활성화 키 가져오기](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)를 참조 하세요.

### <a name="protect-the-device-via-password"></a>암호를 통해 장치 보호

암호는 권한 있는 사용자만 데이터에 액세스할 수 있도록 합니다. Edge Pro R 장치가 잠긴 상태에서 부팅 Azure Stack 합니다.

다음과 같습니다.

- 브라우저를 통해 장치의 로컬 웹 UI에 연결 하 고 장치에 로그인 하는 데 암호를 제공 합니다.
- HTTP를 통해 장치 PowerShell 인터페이스에 원격으로 연결 합니다. 원격 관리는 기본적으로 설정 되어 있습니다. 또한 원격 관리는 사용자가 수행할 수 있는 작업을 제한 하기 위해 충분 한 관리 (JEA)를 사용 하도록 구성 됩니다. 그런 다음 장치 암호를 제공 하 여 장치에 로그인 할 수 있습니다. 자세한 내용은 [장치에 원격으로 연결](azure-stack-edge-gpu-connect-powershell-interface.md)을 참조 하세요.
- 장치의 로컬에 지 사용자는 초기 구성 및 문제 해결을 위해 장치에 제한적으로 액세스할 수 있습니다. 장치, 데이터 전송 및 저장소에서 실행 되는 계산 워크 로드는 모두 클라우드의 리소스에 대 한 Azure 공개 또는 정부 포털에서 액세스할 수 있습니다.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- 로컬 웹 UI를 사용 하 여 [암호를 변경](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#change-device-password)합니다. 암호를 변경 하는 경우 로그인 하는 데 문제가 없는지 모든 원격 액세스 사용자에 게 알려야 합니다.

### <a name="establish-trust-with-the-device-via-certificates"></a>인증서를 통해 장치에 대 한 신뢰 설정

Azure Stack Edge 견고한 장치를 사용 하면 고유한 인증서를 가져와 모든 공용 끝점에 사용할 수 있습니다. 자세한 내용은 [인증서 업로드](azure-stack-edge-gpu-manage-certificates.md#upload-certificates)로 이동 하세요. 장치에 설치할 수 있는 모든 인증서 목록을 보려면 [장치에서 인증서 관리](azure-stack-edge-gpu-manage-certificates.md)로 이동 합니다.

- 장치에서 계산을 구성 하면 IoT 장치와 IoT Edge 장치가 생성 됩니다. 이러한 디바이스에는 대칭 액세스 키가 자동으로 할당됩니다. 보안 모범 사례에 따라 이러한 키는 IoT Hub 서비스를 통해 정기적으로 순환됩니다.

## <a name="protect-your-data"></a>데이터 보호

이 섹션에서는 전송 중인 데이터와 저장 된 데이터를 보호 하는 보안 기능에 대해 설명 합니다.

### <a name="protect-data-at-rest"></a>미사용 데이터 보호

장치에 있는 미사용 데이터의 모든 데이터는 이중 암호화 되며, 데이터에 대 한 액세스를 제어 하 고, 장치를 비활성화 하면 데이터는 데이터 디스크에서 안전 하 게 지워집니다.

#### <a name="double-encryption-of-data"></a>데이터의 이중 암호화

디스크의 데이터는 두 가지 암호화 계층으로 보호 됩니다.

- 첫 번째 암호화 계층은 데이터 볼륨에 대 한 BitLocker XTS-AES 256 비트 암호화입니다.
- 두 번째 계층은 기본 제공 암호화가 있는 하드 디스크입니다.
- OS 볼륨에는 단일 암호화 계층으로 BitLocker가 있습니다.

> [!NOTE]
> OS 디스크에는 단일 계층의 BitLocker XTS-256 소프트웨어 암호화가 있습니다.

장치가 활성화 되 면 장치가 부팅 되지 않는 경우 장치에서 데이터를 복구 하는 데 도움이 되는 복구 키가 포함 된 키 파일을 저장 하 라는 메시지가 표시 됩니다. 파일에는 두 개의 키가 있습니다.

- 한 키는 OS 볼륨의 장치 구성을 복구 합니다.
<!-- - Second key is to unlock the BitLocker on the data disks. -->
- 두 번째 키는 데이터 디스크에서 하드웨어 암호화의 잠금을 해제 합니다.

> [!IMPORTANT]
> 장치 자체 외부의 안전한 위치에 키 파일을 저장 합니다. 장치가 부팅 되지 않고 키가 없는 경우 데이터 손실이 발생할 수 있습니다.

- 특정 복구 시나리오에서 저장 한 키 파일을 입력 하 라는 메시지가 표시 됩니다. 
<!--- If a node isn't booting up, you will need to perform a node replacement. You will have the option to swap the data disks from the failed node to the new node. For a 4-node device, you won't need a key file. For a 1-node device, you will be prompted to provide a key file.-->

#### <a name="restricted-access-to-data"></a>데이터에 대 한 제한 된 액세스

공유 및 저장소 계정에 저장 된 데이터에 대 한 액세스는 제한 됩니다.

- 공유 데이터에 액세스 하는 SMB 클라이언트에는 공유와 연결 된 사용자 자격 증명이 필요 합니다. 이러한 자격 증명은 공유를 만들 때 정의 됩니다.
- 공유에 액세스 하는 NFS 클라이언트는 공유를 만들 때 해당 IP 주소를 명시적으로 추가 해야 합니다.
- 장치에서 만들어진 Edge 저장소 계정은 로컬 이며 데이터 디스크에 대 한 암호화로 보호 됩니다. 이러한 Edge 저장소 계정이 매핑되는 Azure storage 계정은에 지 저장소 계정과 연결 된 구독 및 2 512 비트 저장소 액세스 키에 의해 보호 됩니다. 이러한 키는 Azure Storage 계정과 연결 된 것과 다릅니다. 자세한 내용은 [저장소 계정에서 데이터 보호](#protect-data-in-storage-accounts)를 참조 하세요.
- BitLocker XTS-AES 256 비트 암호화를 사용 하 여 로컬 데이터를 보호 합니다.

#### <a name="secure-data-erasure"></a>보안 데이터 지우기

장치에서 하드 리셋을 수행 하면 장치에서 보안 초기화가 수행 됩니다. 보안 초기화는 NIST SP 800-88r1 제거를 사용 하 여 디스크에서 데이터 지우기를 수행 합니다.

### <a name="protect-data-in-flight"></a>비행 중인 데이터 보호

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### <a name="protect-data-in-storage-accounts"></a>저장소 계정에서 데이터 보호

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]

- 저장소 계정 키를 회전 한 다음 정기적으로 [동기화](azure-stack-edge-j-series-manage-storage-accounts.md) 하 여 권한 없는 사용자 로부터 저장소 계정을 보호 합니다.

## <a name="manage-personal-information"></a>개인 정보 관리

Azure Stack Edge 서비스는 다음과 같은 시나리오에서 개인 정보를 수집 합니다.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

공유에 액세스 하거나 삭제할 수 있는 사용자 목록을 보려면 [Azure Stack Edge에서 공유 관리](azure-stack-edge-j-series-manage-shares.md)의 단계를 따르세요.

자세한 내용은 [보안 센터](https://www.microsoft.com/trustcenter)에서 Microsoft 개인 정보 취급 방침을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure Stack Edge Pro R 장치 배포](azure-stack-edge-gpu-deploy-prep.md)
