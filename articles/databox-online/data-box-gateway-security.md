---
title: Azure 데이터 상자 게이트웨이 보안 | Microsoft Docs
description: Azure 데이터 상자 게이트웨이 가상 장치, 서비스 및 온-프레미스 및 클라우드에서 데이터를 보호 하는 보안 및 개인 정보 보호 기능을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: b27b712128ddfbb07a7a7f68f616c20ec3fb53d3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685902"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure 데이터 상자 게이트웨이 보안 및 데이터 보호

보안은 주요 관심사는 새로운 기술을 채택 하는 경우 특히 기술이 기밀 또는 소유 데이터를 사용 하 여 사용 되는 경우입니다. Microsoft Azure 데이터 상자 게이트웨이 솔루션은 권한 있는 엔터티만 수 보기, 수정 또는 데이터 삭제를 확인 하는 데 도움이 됩니다.

이 문서에서는 각 솔루션 구성 요소에 저장 된 데이터를 보호 하는 Azure 데이터 상자 게이트웨이 보안 기능을 설명 합니다.

데이터 상자 게이트웨이 솔루션은 서로 상호 작용 하는 네 가지 주요 구성 요소:

- **Azure에서 호스팅된 데이터 상자 게이트웨이 서비스** – 장치 순서를 만들고 장치를 구성 하 고 완료 될 때까지 다음 순서를 추적 하는 데 사용할 수 있는 관리 리소스입니다.
- **데이터 상자 게이트웨이 장치** – 가상 장치를 제공한 시스템의 하이퍼바이저에 프로 비전 합니다. 이 가상 장치를 Azure로 온-프레미스 데이터를 가져오는 데 사용 됩니다.
- **장치에 연결 된 클라이언트/호스트** – 데이터 상자 게이트웨이 장치에 연결 하 고 보호 해야 하는 데이터를 포함 하는 인프라 내의 클라이언트입니다.
- **클라우드 저장소** – 데이터가 저장되는 Azure 클라우드의 위치입니다. 이 위치는 일반적으로 사용자가 만든 데이터 상자 게이트웨이 리소스에 연결 된 저장소 계정.


## <a name="data-box-gateway-service-protection"></a>데이터 상자 게이트웨이 서비스 보호

데이터 상자 게이트웨이 서비스가 Microsoft Azure에서 호스팅되는 관리 서비스입니다. 서비스 구성 및 장치 관리에 사용 됩니다.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>데이터 상자 게이트웨이 장치 보호

데이터 상자 게이트웨이 장치에 제공 하는 온-프레미스 시스템의 하이퍼바이저에 프로 비전 된 가상 장치가입니다. 장치는 Azure로 데이터를 전송 하는 데 도움이 됩니다. 장치:

- 데이터 상자 Edge/데이터 상자 게이트웨이 서비스에 액세스 하는 정품 인증 키를 해야 합니다.
- 항상 장치 암호로 보호 합니다.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>정품 인증 키를 통해 장치를 보호 합니다.

인증 된 데이터 상자 게이트웨이 장치를 Azure 구독에서 만든 데이터 상자 게이트웨이 서비스에 가입할 수 있습니다. 장치를 인증 데이터 상자 게이트웨이 서비스를 사용 하 여 장치를 활성화 하는 정품 인증 키를 사용 해야 합니다.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

자세한 내용은 [정품 인증 키 가져오기](data-box-gateway-deploy-prep.md#get-the-activation-key)합니다.

### <a name="protect-the-device-via-password"></a>암호를 통해 장치를 보호 합니다.

암호 데이터 권한 있는 사용자만 액세스할 수 있는지 확인 합니다. 데이터 상자 게이트웨이 장치 부팅 잠긴 상태에서입니다.

다음을 수행할 수 있습니다.

- 로컬 웹 UI는 브라우저를 통해 장치에 연결한 후 장치에 로그인 하는 데 암호를 제공 합니다.
- HTTP를 통해 장치 PowerShell 인터페이스에 원격으로 연결 합니다. 원격 관리 기본적으로 켜져 있습니다. 그런 다음 장치에 로그인 할 장치 암호를 제공할 수 있습니다. 자세한 내용은 [데이터 상자 게이트웨이 장치에 원격으로 연결](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)합니다.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 로컬 웹 UI를 사용 하 여 [암호를 변경](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)합니다. 암호를 변경 하는 경우에 로그인 오류 발생 하지 않도록 모든 원격 액세스 사용자에 게 해야 합니다.


## <a name="protect-the-data"></a>데이터 보호

이 섹션에서는 전송 중인 데이터와 저장 된 데이터를 보호 하는 데이터 상자 게이트웨이 보안 기능을 설명 합니다.

### <a name="protect-data-at-rest"></a>휴지 상태의 데이터 보호

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>이동 중인 데이터 보호

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>저장소 계정을 통해 데이터 보호

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 회전 차례로 [저장소 계정 키를 동기화](data-box-gateway-manage-shares.md#sync-storage-keys) 정기적으로 하는 데 저장소 계정이 무단으로 액세스 하지 않도록 합니다.

## <a name="manage-personal-information"></a>개인 정보 관리

데이터 상자 게이트웨이 서비스를 같은 키 경우 개인 정보를 수집합니다.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

단계에 따라 공유를 삭제 하거나 액세스할 수 있는 사용자의 목록을 보려면 [공유 데이터 상자 게이트웨이에서 관리](data-box-gateway-manage-shares.md)합니다.

자세한 내용은 [보안 센터](https://www.microsoft.com/trustcenter)에서 Microsoft 개인 정보 취급 방침을 검토합니다.

## <a name="next-steps"></a>다음 단계

[데이터 상자 게이트웨이 장치 배포](data-box-gateway-deploy-prep.md)합니다.
