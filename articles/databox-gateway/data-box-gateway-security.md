---
title: Azure Data Box Gateway 보안 | Microsoft Docs
description: 온-프레미스 및 클라우드에서 Azure Data Box Gateway 가상 장치, 서비스 및 데이터를 보호하는 보안 및 개인 정보 보호 기능에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 13d3809611714992f24a66a96c22074e69fba9bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786659"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure Data Box Gateway 보안 및 데이터 보호

보안은 새로운 기술을 채택할 때, 특히 해당 기술이 기밀 또는 재산적 가치를 가지는 데이터에 사용되는 경우 중요한 관심사입니다. Azure Data Box Gateway는 권한이 있는 엔터티만 데이터를 보거나, 수정하거나, 삭제할 수 있도록 하는 데 유용합니다.

이 문서에서는 각 솔루션 구성 요소와 이러한 구성 요소에 저장된 데이터를 보호 하는 데 유용한 Azure Data Box Gateway 보안 기능을 설명합니다.

Data Box Gateway 솔루션은 서로 상호 작용하는 4개의 기본 구성 요소로 구성됩니다.

- **Azure에서 호스트되는 Data Box Gateway 서비스**. 장치 주문을 작성하고 장치를 구성한 다음, 완료까지 주문을 추적하는 데 사용되는 관리 리소스입니다.
- **Data Box Gateway 장치**. 사용자가 제공하는 시스템의 하이퍼바이저에서 프로비전하는 가상 장치입니다. 이 가상 장치는 온-프레미스 데이터를 Azure로 가져오는 데 사용됩니다.
- **장치에 연결된 클라이언트/호스트**. 인프라에서 Data Box Gateway 장치에 연결되고 보호해야 하는 데이터를 포함하는 클라이언트입니다.
- **클라우드 스토리지**. Azure 클라우드 플랫폼에서 데이터가 저장되는 위치입니다. 해당 위치는 일반적으로 사용자가 만든 Azure Data Box Gateway 리소스에 연결된 스토리지 계정입니다.

## <a name="data-box-gateway-service-protection"></a>Data Box Gateway 서비스 보호

Data Box Gateway 서비스는 Azure에서 호스트되는 관리 서비스입니다. 이 서비스는 장치를 구성하고 관리하는 데 사용됩니다.

[!INCLUDE [data-box-gateway-service-protection](../../includes/data-box-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Data Box Gateway 장치 보호

Data Box Gateway 장치는 사용자가 제공하는 온-프레미스 시스템의 하이퍼바이저에서 프로비전되는 가상 장치입니다. 이 장치는 Azure에 데이터를 전송하는 데 유용합니다. 사용자 장치는 다음 특징이 있습니다.

- Azure Stack Edge Pro/Data Box Gateway 서비스에 액세스하려면 활성화 키가 필요합니다.
- 장치 암호로 항상 보호됩니다.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>활성화 키를 통해 장치 보호

권한 있는 Data Box Gateway 장치만 Azure 구독에서 만든 Data Box Gateway 서비스에 참가할 수 있습니다. 장치에 권한을 부여하려면 활성화 키를 사용하여 Data Box Gateway 서비스에서 장치를 활성화해야 합니다.

[!INCLUDE [data-box-gateway-activation-key](../../includes/data-box-gateway-activation-key.md)]

자세한 내용은 [활성화 키 가져오기](data-box-gateway-deploy-prep.md#get-the-activation-key)를 참조하세요.

### <a name="protect-the-device-via-password"></a>암호를 통해 장치 보호

암호는 권한 있는 사용자만 데이터에 액세스할 수 있도록 보장합니다. Data Box Gateway 장치는 잠긴 상태에서 부팅됩니다.

다음을 할 수 있습니다.

- 브라우저를 통해 장치의 로컬 웹 UI에 연결한 다음, 암호를 입력하여 장치에 로그인합니다.
- HTTP를 통해 장치의 PowerShell 인터페이스에 원격으로 연결합니다. 원격 관리는 기본적으로 활성화되어 있습니다. 그런 다음 장치 암호를 입력하여 장치에 로그인할 수 있습니다. 자세한 내용은 [Data Box Gateway 장치에 원격으로 연결](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)을 참조하세요.

[!INCLUDE [data-box-gateway-password-best-practices](../../includes/data-box-gateway-password-best-practices.md)]
- 로컬 웹 UI를 사용하여 [암호를 변경](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)합니다. 암호를 변경하는 경우 로그인 문제가 발생하지 않도록 모든 원격 액세스 사용자에게 알려야 합니다.

## <a name="protect-your-data"></a>데이터 보호

이 섹션에서는 전송 중인, 그리고 저장된 데이터를 보호하는 Data Box Gateway 보안 기능에 대해 설명합니다.

### <a name="protect-data-at-rest"></a>미사용 데이터 보호

[!INCLUDE [data-box-gateway-data-rest](../../includes/data-box-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>플라이트 중인 데이터 보호

[!INCLUDE [data-box-gateway-data-flight](../../includes/data-box-gateway-data-flight.md)]

### <a name="protect-data-using-storage-accounts"></a>스토리지 계정을 사용한 데이터 보호

[!INCLUDE [data-box-gateway-data-storage-accounts](../../includes/data-box-gateway-protect-data-storage-accounts.md)]

- [스토리지 계정 키를 정기적으로 순환하고 동기화하면](data-box-gateway-manage-shares.md#sync-storage-keys) 권한 없는 사용자로부터 스토리지 계정을 보호할 수 있습니다.

### <a name="protect-the-device-data-using-bitlocker"></a>BitLocker를 사용하여 장치 데이터 보호

Data Box Gateway 가상 머신에서 가상 디스크를 보호하려면 BitLocker를 사용하도록 설정하는 것이 좋습니다. 기본적으로 BitLocker가 활성화되지 않았습니다. 자세한 내용은 다음을 참조하세요.

- [Hyper-V 관리자의 암호화 지원 설정](/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
- [가상 머신의 BitLocker 지원](https://kb.vmware.com/s/article/2036142)

## <a name="manage-personal-information"></a>개인 정보 관리

Data Box Gateway 서비스는 다음과 같은 시나리오에서 개인 정보를 수집합니다.

[!INCLUDE [data-box-gateway-manage-personal-data](../../includes/data-box-gateway-manage-personal-data.md)]

공유에 액세스하거나 공유를 삭제할 수 있는 사용자 목록을 보려면 [Data Box Gateway 공유 관리](data-box-gateway-manage-shares.md)의 단계를 수행합니다.

자세한 내용은 [보안 센터](https://www.microsoft.com/trustcenter)에서 Microsoft 개인 정보 취급 방침을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Data Box Gateway 장치 배포](data-box-gateway-deploy-prep.md)
