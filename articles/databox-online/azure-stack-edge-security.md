---
title: Azure Stack Edge 보안 | Microsoft Docs
description: 온-프레미스 및 클라우드에서 Azure Stack Edge 장치, 서비스 및 데이터를 보호 하는 보안 및 개인 정보 보호 기능에 대해 설명 합니다.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 3d567ec4d760be24fdbb79ff85bd6db0eb4a66c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82570071"
---
# <a name="azure-stack-edge-security-and-data-protection"></a>Azure Stack Edge 보안 및 데이터 보호

새 기술을 채택 하는 경우, 특히 기술이 기밀 또는 소유 데이터와 함께 사용 되는 경우 보안은 중요 한 문제입니다. Azure Stack Edge를 사용 하면 권한 있는 엔터티만 데이터를 확인, 수정 또는 삭제할 수 있습니다.

이 문서에서는 각 솔루션 구성 요소와 이러한 구성 요소에 저장 된 데이터를 보호 하는 데 도움이 되는 Azure Stack Edge 보안 기능을 설명 합니다.

Azure Stack Edge는 서로 상호 작용 하는 네 가지 주요 구성 요소로 구성 됩니다.

- **Azure에서 호스트 되는 Azure Stack Edge 서비스**입니다. 장치 순서를 만들고 장치를 구성한 다음 완료에 대 한 순서를 추적 하는 데 사용 하는 관리 리소스입니다.
- **Edge 장치를 Azure Stack**합니다. 온-프레미스 데이터를 Azure로 가져올 수 있도록 사용자에 게 제공 되는 전송 장치입니다.
- **장치에 연결 된 클라이언트/호스트**. 인프라에서 Azure Stack Edge 장치에 연결 하 고 보호 해야 하는 데이터를 포함 하는 클라이언트입니다.
- **클라우드 저장소**. Azure cloud platform에서 데이터가 저장 되는 위치입니다. 이 위치는 일반적으로 사용자가 만드는 Azure Stack Edge 리소스에 연결 된 저장소 계정입니다.

## <a name="azure-stack-edge-service-protection"></a>Azure Stack Edge 서비스 보호

Azure Stack Edge 서비스는 Azure에서 호스트 되는 관리 서비스입니다. 서비스는 장치를 구성 하 고 관리 하는 데 사용 됩니다.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-device-protection"></a>Azure Stack Edge 장치 보호

Azure Stack Edge 장치는 데이터를 로컬로 처리 한 다음 Azure에 전송 하 여 데이터를 변환 하는 데 도움이 되는 온-프레미스 장치입니다. 장치:

- Azure Stack Edge 서비스에 액세스 하려면 정품 인증 키가 필요 합니다.
- 는 장치 암호로 항상 보호 됩니다.
- 는 잠긴 장치입니다. 장치 BMC 및 BIOS는 암호로 보호 됩니다. BIOS는 제한 된 사용자 액세스를 통해 보호 됩니다.
- 보안 부팅이 사용 하도록 설정 되어 있습니다.
- Windows Defender Device Guard를 실행 합니다. Device Guard를 사용 하면 코드 무결성 정책에서 정의한 신뢰할 수 있는 응용 프로그램만 실행할 수 있습니다.

### <a name="protect-the-device-via-activation-key"></a>정품 인증 키를 통해 장치 보호

권한 있는 Azure Stack Edge 장치만 Azure 구독에서 만든 Azure Stack Edge 서비스에 가입할 수 있습니다. 장치에 권한을 부여 하려면 정품 인증 키를 사용 하 여 Azure Stack Edge 서비스에서 장치를 활성화 해야 합니다.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

자세한 내용은 [활성화 키 가져오기](azure-stack-edge-deploy-prep.md#get-the-activation-key)를 참조 하세요.

### <a name="protect-the-device-via-password"></a>암호를 통해 장치 보호

암호는 권한 있는 사용자만 데이터에 액세스할 수 있도록 합니다. Azure Stack Edge 장치가 잠긴 상태에서 부팅 됩니다.

다음을 할 수 있습니다.

- 브라우저를 통해 장치의 로컬 웹 UI에 연결 하 고 장치에 로그인 하는 데 암호를 제공 합니다.
- HTTP를 통해 장치 PowerShell 인터페이스에 원격으로 연결 합니다. 원격 관리는 기본적으로 설정 되어 있습니다. 그런 다음 장치 암호를 제공 하 여 장치에 로그인 할 수 있습니다. 자세한 내용은 [Azure Stack Edge 장치에 원격으로 연결](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)을 참조 하세요.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 로컬 웹 UI를 사용 하 여 [암호를 변경](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access)합니다. 암호를 변경 하는 경우 로그인 하는 데 문제가 없는지 모든 원격 액세스 사용자에 게 알려야 합니다.

## <a name="protect-your-data"></a>데이터 보호

이 섹션에서는 전송 중인 데이터와 저장 된 데이터를 보호 하는 Azure Stack Edge 보안 기능에 대해 설명 합니다.

### <a name="protect-data-at-rest"></a>휴지 상태의 데이터 보호

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- BitLocker XTS-AES 256 비트 암호화를 사용 하 여 로컬 데이터를 보호 합니다.


### <a name="protect-data-in-flight"></a>비행 중인 데이터 보호

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>스토리지 계정을 통해 데이터 보호

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 저장소 계정 키를 회전 한 다음 정기적으로 [동기화](azure-stack-edge-manage-shares.md#sync-storage-keys) 하 여 권한 없는 사용자 로부터 저장소 계정을 보호 합니다.

## <a name="manage-personal-information"></a>개인 정보 관리

Azure Stack Edge 서비스는 다음과 같은 시나리오에서 개인 정보를 수집 합니다.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

공유에 액세스 하거나 삭제할 수 있는 사용자 목록을 보려면 [Azure Stack Edge에서 공유 관리](azure-stack-edge-manage-shares.md)의 단계를 따르세요.

자세한 내용은 [보안 센터](https://www.microsoft.com/trustcenter)에서 Microsoft 개인 정보 취급 방침을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure Stack Edge 장치 배포](azure-stack-edge-deploy-prep.md)
