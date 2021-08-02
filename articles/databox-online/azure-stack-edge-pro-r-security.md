---
title: Azure Stack Edge Pro R 보안 | Microsoft Docs
description: 온-프레미스 및 클라우드에서 Azure Stack Edge Pro R 및 Azure Stack Edge Mini R 디바이스, 서비스 및 데이터를 보호하는 보안 및 개인 정보 보호 기능에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/03/2021
ms.author: alkohli
ms.openlocfilehash: a68394b5db25565b44db0347a685d6cd67540170
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111985709"
---
# <a name="security-and-data-protection-for-azure-stack-edge-pro-r-and-azure-stack-edge-mini-r"></a>Azure Stack Edge Pro R 및 Azure Stack Edge Mini R에 대한 보안 및 데이터 보호

[!INCLUDE [applies-to-pro-r-and-mini-r--skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

보안은 새로운 기술을 채택할 때, 특히 해당 기술이 기밀 또는 재산적 가치를 가지는 데이터에 사용되는 경우 중요한 관심사입니다. Azure Stack Edge Pro R 및 Azure Stack Edge Mini R은 권한이 있는 엔터티만 데이터를 확인, 수정 또는 삭제할 수 있게 해줍니다.

이 문서에서는 각 솔루션 구성 요소와 여기에 저장된 데이터를 보호하는 데 도움이 되는 Azure Stack Edge Pro R 및 Azure Stack Edge Mini R 보안 기능에 대해 설명합니다.

이 솔루션은 서로 상호 작용하는 4개의 기본 구성 요소로 구성됩니다.

- **Azure 공용 또는 Azure Government 클라우드에 호스트되는 Azure Stack Edge 서비스**. 디바이스 주문을 작성하고 디바이스를 구성한 다음, 완료까지 주문을 추적하는 데 사용되는 관리 리소스입니다.
- **Azure Stack Edge Rugged 디바이스**. 온-프레미스 데이터를 Azure 공용 또는 Azure Government 클라우드로 가져올 수 있도록 사용자에게 제공되는 러기드 물리적 디바이스입니다. 이 디바이스는 Azure Stack Edge Pro R 또는 Azure Stack Edge Mini R일 수 있습니다.
- **디바이스에 연결된 클라이언트/호스트**. 인프라에서 디바이스에 연결되고 보호해야 하는 데이터를 포함하는 클라이언트입니다.
- **클라우드 스토리지**. Azure 클라우드 플랫폼에서 데이터가 저장되는 위치입니다. 이 위치는 일반적으로 사용자가 만드는 Azure Stack Edge 리소스에 연결된 스토리지 계정입니다.

## <a name="service-protection"></a>서비스 보호

Azure Stack Edge 서비스는 Azure에서 호스트된 관리 서비스입니다. 이 서비스는 디바이스를 구성하고 관리하는 데 사용됩니다.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## <a name="device-protection"></a>디바이스 보호

러기드 디바이스는 데이터를 로컬로 처리한 후 Azure로 전송하여 데이터를 변환하는 데 도움을 주는 온-프레미스 디바이스입니다. 사용자 디바이스는 다음 특징이 있습니다.

- Azure Stack Edge 서비스에 액세스하기 위해 활성화 키가 있어야 합니다.
- 디바이스 암호로 항상 보호됩니다.
- 잠긴 디바이스입니다. 디바이스 BMC(베이스보드 관리 컨트롤러) 및 BIOS는 암호로 보호됩니다. BMC는 제한된 사용자 액세스를 통해 보호됩니다.
- Microsoft에서 제공되는 신뢰할 수 있는 소프트웨어만 사용하여 디바이스가 부트되도록 보장하는 보안 부트가 사용되도록 설정되어 있습니다.
- WDAC(Windows Defender 애플리케이션 제어)를 실행합니다. WDAC를 사용하면 코드 무결성 정책에 정의한 신뢰할 수 있는 애플리케이션만 실행할 수 있습니다.
- 하드웨어 기반, 소프트웨어 관련 기능을 수행하는 TPM(신뢰할 수 있는 플랫폼 모듈)을 포함합니다. 특히 TPM은 디바이스에 유지되어야 하는 비밀 및 데이터를 관리하고 보호합니다.
- 필요한 포트만 디바이스에 열리고 다른 포트는 모두 차단됩니다. 자세한 내용은 [디바이스의 포트 요구 사항](azure-stack-edge-pro-r-system-requirements.md) 목록을 참조하세요.
- 소프트웨어뿐만 아니라 디바이스 하드웨어에 대한 모든 액세스가 기록됩니다. 
    - 디바이스 소프트웨어에 대해 디바이스에서 인바운드 및 아웃바운드 트래픽에 대해 기본 방화벽 로그가 수집됩니다. 이러한 로그는 지원 패키지에 번들로 제공됩니다.
    - 디바이스 하드웨어에 대해 디바이스 섀시 열기 및 닫기와 같은 모든 디바이스 섀시 이벤트가 디바이스에 기록됩니다.

    하드웨어 및 소프트웨어 침입 이벤트를 포함하는 특정 로그에 대한 자세한 내용 및 로그를 가져오는 방법은 [고급 보안 로그 수집](azure-stack-edge-gpu-troubleshoot.md#gather-advanced-security-logs)을 참조하세요.


### <a name="protect-the-device-via-activation-key"></a>활성화 키를 통해 디바이스 보호

권한이 부여된 Azure Stack Edge Pro R 또는 Azure Stack Edge Mini R 디바이스만 Azure 구독에 만든 Azure Stack Edge 서비스에 가입하도록 허용됩니다. 디바이스에 권한을 부여하려면 활성화 키를 사용하여 Azure Stack Edge 서비스에서 디바이스를 활성화해야 합니다.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

자세한 내용은 [활성화 키 가져오기](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)를 참조하세요.

### <a name="protect-the-device-via-password"></a>암호를 통해 디바이스 보호

암호는 권한 있는 사용자만 데이터에 액세스할 수 있도록 보장합니다. Azure Stack Edge Pro R 디바이스는 잠금 상태로 부트됩니다.

다음을 할 수 있습니다.

- 브라우저를 통해 디바이스의 로컬 웹 UI에 연결한 다음, 암호를 입력하여 디바이스에 로그인합니다.
- HTTP를 통해 디바이스 PowerShell 인터페이스에 원격으로 연결합니다. 원격 관리는 기본적으로 켜져 있습니다. 원격 관리는 또한 JEA(Just Enough Administration)를 사용하여 사용자가 수행할 수 있는 작업을 제한하도록 구성됩니다. 그런 다음 디바이스 암호를 입력하여 디바이스에 로그인할 수 있습니다. 자세한 내용은 [디바이스에 원격으로 연결](azure-stack-edge-gpu-connect-powershell-interface.md)을 참조하세요.
- 디바이스의 로컬 Edge 사용자는 초기 구성 및 문제 해결을 위해 디바이스에 대해 제한된 액세스 권한을 갖습니다. 디바이스에서 실행되는 컴퓨팅 워크로드, 데이터 전송 및 스토리지는 Azure 공용 또는 정부 포털에서 클라우드 리소스에 대해 액세스될 수 있습니다.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- 로컬 웹 UI를 사용하여 [암호를 변경](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#change-device-password)합니다. 암호를 변경하는 경우 로그인 문제가 발생하지 않도록 모든 원격 액세스 사용자에게 알려야 합니다.

### <a name="establish-trust-with-the-device-via-certificates"></a>인증서를 통해 디바이스에 신뢰 설정

Azure Stack Edge Rugged 디바이스를 사용하면 사용자 고유의 인증서를 가져오고 모든 공용 엔드포인트에 사용하도록 이를 설치할 수 있습니다. 자세한 내용은 [인증서 업로드](azure-stack-edge-gpu-manage-certificates.md#upload-certificates)를 참조하세요. 디바이스에 설치할 수 있는 모든 인증서 목록은 [디바이스에서 인증서 관리](azure-stack-edge-gpu-manage-certificates.md)를 참조하세요.

- 디바이스에서 컴퓨팅을 구성하면 IoT 디바이스 및 IoT Edge 디바이스가 생성됩니다. 이러한 디바이스에는 대칭 액세스 키가 자동으로 할당됩니다. 보안 모범 사례에 따라 이러한 키는 IoT Hub 서비스를 통해 정기적으로 순환됩니다.

## <a name="protect-your-data"></a>데이터 보호

이 섹션에서는 전송 중인 데이터 및 저장된 데이터를 보호하는 보안 기능에 대해 설명합니다.

### <a name="protect-data-at-rest"></a>미사용 데이터 보호

디바이스에서 모든 미사용 데이터가 이중 암호화되고, 데이터에 대한 액세스가 제어되고, 디바이스가 비활성화되면 데이터가 데이터 디스크에서 안전하게 삭제됩니다.

#### <a name="double-encryption-of-data"></a>데이터의 이중 암호화

디스크의 데이터가 두 가지 암호화 레이어로 보호됩니다.

- 첫 번째 암호화 레이어는 데이터 볼륨의 BitLocker XTS-AES 256비트 암호화입니다.
- 두 번째 레이어는 기본 제공되는 암호화가 포함된 하드 디스크입니다.
- OS 볼륨에는 단일 암호화 레이어로 BitLocker가 포함됩니다.

> [!NOTE]
> OS 디스크에는 단일 레이어 BitLocker XTS-AES-256 소프트웨어 암호화가 포함됩니다.

디바이스를 활성화하기 전에 디바이스에서 미사용 암호화를 구성해야 합니다. 이 설정은 필수이며 성공적으로 구성될 때까지 디바이스를 활성화할 수 없습니다. 

공장에서 디바이스를 이미지로 만들면 볼륨 수준 BitLocker 암호화가 사용하도록 설정됩니다. 디바이스를 받은 후에는 저장 데이터 암호화를 구성해야 합니다. 스토리지 풀 및 볼륨이 다시 만들어지고 저장 데이터 암호화를 사용하도록 BitLocker 키를 제공할 수 있으므로 미사용 데이터에 대한 또 다른 암호화 계층을 만들 수 있습니다. 

미사용 암호화 키는 사용자가 제공하는 32자 길이 Base-64 인코딩 키이며 이 키는 실제 암호화 키를 보호하는 데 사용됩니다. Microsoft는 데이터를 보호하는 이 미사용 암호화 키에 액세스할 수 없습니다. 키는 디바이스가 활성화된 후 **클라우드 세부 정보** 페이지의 키 파일에 저장됩니다.

디바이스가 활성화되면 디바이스가 부트되지 않을 때 디바이스에서 데이터를 복구하는 데 도움이 되는 복구 키가 포함된 키 파일을 저장하라는 메시지가 표시됩니다. 특정 복구 시나리오에서는 저장한 키 파일을 입력하라는 메시지가 표시됩니다. 키 파일에는 다음과 같은 복구 키가 있습니다.

- 첫 번째 암호화 계층의 잠금을 해제하는 키
- 데이터 디스크에서 하드웨어 암호화의 잠금을 해제하는 키
- OS 볼륨에서 디바이스 구성을 복구하는 데 도움이 되는 키
- Azure 서비스를 통과하는 데이터를 보호하는 키

> [!IMPORTANT]
> 디바이스 자체 외부의 안전한 위치에 키 파일을 저장합니다. 디바이스가 부트되지 않고 키가 없으면 데이터가 손실될 수 있습니다.



#### <a name="restricted-access-to-data"></a>데이터에 대해 제한되는 액세스

공유 및 스토리지 계정에 저장된 데이터에 대한 액세스가 제한됩니다.

- 공유 데이터에 액세스하는 SMB 클라이언트에는 해당 공유와 연관된 사용자 자격 증명이 필요합니다. 이러한 자격 증명은 공유가 생성될 때 정의됩니다.
- 공유에 액세스하는 NFS 클라이언트는 공유가 생성될 때 해당 IP 주소가 명시적으로 추가되어야 합니다.
- 디바이스에 생성된 Edge 스토리지 계정은 로컬이며, 데이터 디스크의 암호화로 보호됩니다. 이러한 Edge 스토리지 계정이 매핑되는 Azure 스토리지 계정은 Edge 스토리지 계정과 연관된 2개의 512비트 스토리지 액세스 키 및 구독으로 보호됩니다. 이러한 키는 Azure Storage 계정과 연관된 키와 다릅니다. 자세한 내용은 [스토리지 계정에서 데이터 보호](#protect-data-in-storage-accounts)를 참조하세요.
- BitLocker XTS-AES 256비트 암호화는 로컬 데이터를 보호하는 데 사용됩니다.

#### <a name="secure-data-erasure"></a>보안 데이터 지우기

디바이스에서 하드 리셋을 수행하면 디바이스에 대해 보안 초기화가 수행됩니다. 보안 초기화는 NIST SP 800-88r1 제거를 사용하여 디스크에서 데이터 지우기를 수행합니다.

### <a name="protect-data-in-flight"></a>전송 중인 데이터 보호

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### <a name="protect-data-in-storage-accounts"></a>스토리지 계정의 데이터 보호

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]
- [스토리지 계정 키](azure-stack-edge-gpu-manage-storage-accounts.md)를 정기적으로 순환하고 동기화하면 권한 없는 사용자로부터 스토리지 계정을 보호하는 데 도움이 됩니다.

## <a name="manage-personal-information"></a>개인 정보 관리

Azure Stack Edge 서비스는 다음 시나리오에서 개인 정보를 수집합니다.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

공유에 액세스하거나 이를 삭제할 수 있는 사용자 목록을 보려면 [Azure Stack Edge에서 공유 관리](azure-stack-edge-gpu-manage-shares.md)의 단계를 따르세요.

자세한 내용은 [보안 센터](https://www.microsoft.com/trustcenter)에서 Microsoft 개인정보처리방침을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure Stack Edge Pro R 디바이스 배포](azure-stack-edge-gpu-deploy-prep.md)
