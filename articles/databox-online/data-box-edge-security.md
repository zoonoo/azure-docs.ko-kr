---
title: 데이터 상자에 지 보안 | Microsoft Docs
description: 데이터 상자에 지 장치, 서비스 및 온-프레미스 및 클라우드에서 데이터를 보호 하는 보안 및 개인 정보 보호 기능을 설명 합니다.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 197c2c021dd9f674d196fb1169155bc6015f2e79
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578613"
---
# <a name="data-box-edge-security-and-data-protection"></a>데이터 상자에 지 보안 및 데이터 보호

보안은 주요 관심사는 새로운 기술을 채택 하는 경우 특히 기술이 기밀 또는 소유 데이터를 사용 하 여 사용 되는 경우입니다. Microsoft Azure 데이터 가장자리가 상자의 솔루션은 권한 있는 엔터티만 수 보기, 수정 또는 데이터 삭제를 확인 하는 데 도움이 됩니다.

이 문서는 각 솔루션 구성 요소에 저장 된 데이터를 보호 하는 데 도움이 되는 데이터 가장자리가 상자의 보안 기능을 설명 합니다.

Azure 데이터 가장자리가 상자의 솔루션은 서로 상호 작용 하는 네 가지 주요 구성 요소:

- **데이터 가장자리가 상자의 데이터 상자 게이트웨이 서비스가 Azure에서 호스트 /** – 장치 순서를 만들고 장치를 구성 하 고 완료 될 때까지 다음 순서를 추적 하는 데 사용할 수 있는 관리 리소스입니다.
- **데이터 상자에 지 장치** – 전송 장치를 Azure로 온-프레미스 데이터를 가져올 수 있습니다에 게 제공 합니다.
- **장치에 연결 된 클라이언트/호스트** – 데이터 상자 Edge 장치에 연결 하 고 보호 해야 하는 데이터를 포함 하는 인프라 내의 클라이언트입니다.
- **클라우드 저장소** – 데이터가 저장되는 Azure 클라우드의 위치입니다. 이 위치는 일반적으로 사용자가 만든 데이터 가장자리가 상자의 리소스에 연결 된 저장소 계정.


## <a name="data-box-edgedata-box-gateway-service-protection"></a>데이터 상자에 지/데이터 상자 게이트웨이 서비스 보호

데이터 상자 Edge/데이터 상자 게이트웨이 서비스가 Microsoft Azure에서 호스팅되는 관리 서비스입니다. 서비스 구성 및 장치 관리에 사용 됩니다.

- 데이터 상자 Edge/데이터 상자 게이트웨이 서비스에 대 한 액세스는 EA (기업 계약) 또는 클라우드 솔루션 공급자 (CSP) 구독을 조직에 필요 합니다. 자세한 내용은 [Azure 구독에 대 한 등록](https://azure.microsoft.com/resources/videos/sign-up-for-microsoft-azure/)!
- 관리 서비스는 Azure에서 호스팅되므로 Azure 보안 기능으로 보호 됩니다. Microsoft Azure에서 제공하는 보안 기능에 대한 자세한 내용은 [Microsoft Azure 보안 센터](https://azure.microsoft.com/support/trust-center/security/)로 이동합니다.
- SDK 관리 작업에 대 한 암호화 키가 제공 하 여 Data Box Edge에 대 한 / 데이터 상자 게이트웨이 리소스 **장치 속성**합니다. 리소스 Graph API에 대 한 권한이 있는 경우에 암호화 키를 볼 수 있습니다.

## <a name="data-box-edge-device-protection"></a>데이터 상자에 지 장치 보호

데이터 상자 가장자리 로컬로 처리 한 다음 Azure에 전송 하 여 데이터를 변환 하는 데 도움이 되는 온-프레미스 장치는 합니다. 장치:

- 데이터 상자 Edge/데이터 상자 게이트웨이 서비스에 액세스 하는 정품 인증 키를 해야 합니다.
- 항상 장치 암호로 보호 합니다.
- 잠긴 장치가입니다. 장치 BMC 및 BIOS가 BIOS에 대 한 제한 된 사용자 액세스를 사용 하 여 암호로 보호 합니다.
- 보안 부팅이 있었습니다.
- Windows Defender Device Guard를 실행합니다. Device Guard를 사용 하면 코드 무결성 정책에서 정의 하는 신뢰할 수 있는 응용 프로그램만 실행할 수 있습니다.
- 장치를 잠그는 데 사용할 수 있는 표지 내에서 키를 있습니다. 에서는 장치를 구성한 후에 덮개를 열면 하는 것이 좋습니다. 키를 찾아서 장치 앞에 있는 데이터 디스크에 대 한 모든 무단된 액세스를 방지 하기 위해 커버 잠금이 합니다.

### <a name="protect-the-device-via-activation-key"></a>정품 인증 키를 통해 장치를 보호 합니다.

인증 된 데이터 상자 Edge 장치를 Azure 구독에서 만든 데이터 상자 Edge/데이터 상자 게이트웨이 서비스에 가입할 수 있습니다. 장치를 인증 데이터 상자 Edge/데이터 상자 게이트웨이 서비스를 사용 하 여 장치를 활성화 하는 정품 인증 키를 사용 해야 합니다. 자세한 내용은 [정품 인증 키 가져오기](data-box-edge-deploy-prep.md#get-the-activation-key)합니다.

사용 하는 정품 인증 키:

- Azure Active Directory (AAD) 기반된 인증 키가입니다.
- 3 일이 지나면 만료 됩니다.
- 장치 활성화 된 후 사용 되지 않습니다.
 
장치를 활성화 한 후에 토큰을 사용 하 여 Microsoft Azure와 통신할 수 있습니다.

### <a name="protect-the-device-via-password"></a>암호를 통해 장치를 보호 합니다.

암호 데이터 권한 있는 사용자만 액세스할 수 있는지 확인 합니다. 데이터 상자 게이트웨이 및 데이터 가장자리가 상자의 장치 부팅 잠긴 상태에서입니다.

다음을 수행할 수 있습니다.

- 로컬 웹 UI는 브라우저를 통해 장치에 연결한 후 장치에 로그인 하는 데 암호를 제공 합니다.
- HTTP를 통해 장치 PowerShell 인터페이스에 원격으로 연결 합니다. 원격 관리 기본적으로 켜져 있습니다. 그런 다음 장치에 로그인 할 장치 암호를 제공할 수 있습니다. 자세한 내용은 [데이터 상자 Edge 장치에 원격으로 연결](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)합니다.

다음 모범 사례를 고려하세요.

- 가장자리가 상자의 데이터 서비스는 기존 암호를 검색할 수 없습니다: Azure portal을 통해 재설정만 가능 합니다. 잊어버린 경우 암호를 재설정할 필요가 없도록 모든 암호를 안전한 장소에 저장하는 것이 좋습니다. 암호를 재설정 하면 수 다시 설정 하기 전에 모든 사용자에 게 알려야 합니다.
- 로컬 웹 UI를 사용 하 여 [암호를 변경](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)합니다. 암호를 변경 하는 경우에 로그인 오류 발생 하지 않도록 모든 원격 액세스 사용자에 게 해야 합니다.
- HTTP를 통해 장치의 Windows PowerShell 인터페이스를 원격으로 액세스할 수 있습니다. 보안 모범 사례를 따라 신뢰할 수 있는 네트워크에만 HTTP를 사용 해야 합니다.
- 장치 암호 강력 하 고 잘 보호 되는지 확인 합니다. 수행 합니다 [암호에 대 한 유용한 정보](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management)합니다.

## <a name="protect-the-data"></a>데이터 보호

이 섹션에서는 전송 중인 데이터와 저장 된 데이터를 보호 하는 데이터 가장자리가 상자의 보안 기능을 설명 합니다.

### <a name="protect-data-at-rest"></a>휴지 상태의 데이터 보호

데이터-에-나머지.

- -미사용 데이터에 대 한 데이터 가장자리가 상자의 로컬 데이터를 보호 하려면 BitLocker XTS AES-256 암호화를 사용 합니다.
- 공유에 있는 데이터에 대해 공유에 대 한 액세스 제한 됩니다.

    - 공유 데이터에 액세스 하는 SMB 클라이언트 공유와 연결 된 사용자 자격 증명을 해야 합니다. 이러한 자격 증명 공유 생성 시 정의 됩니다.
    - 클라이언트용 NFS 공유에 액세스 하는 클라이언트의 IP 주소를 공유를 만들 때에 추가 해야 합니다.


### <a name="protect-data-in-flight"></a>이동 중인 데이터 보호

데이터-에-항공권:

- 장치와 Azure 간에 이동 하는 데이터에 대 한 표준 TLS 1.2 사용 됩니다. TLS 1.1 및 이전 버전에 없는 대체 방법이 있습니다. TLS 1.2를 지원 하지 않는 경우 에이전트 통신이 차단 됩니다. TLS 1.2 포털 및 SDK 관리 작업에도 필요 합니다.
- 클라이언트 장치의 로컬 웹 UI 통해 브라우저에서에 액세스 하는 경우 표준 TLS 1.2는 기본 보안 프로토콜로 사용 됩니다.

    - TLS 1.2를 사용 하도록 브라우저를 구성 하는 것이 좋습니다.
    - 브라우저는 TLS 1.2를 지원 하지 않으면, TLS 1.1 또는 TLS 1.0을 사용할 수 있습니다.
- 데이터 서버 로부터 복사할 때 데이터를 보호 하려면 암호화를 사용 하 여 SMB 3.0을 사용 하는 것이 좋습니다.

### <a name="protect-data-via-storage-accounts"></a>저장소 계정을 통해 데이터 보호

장치를 대상으로 Azure에서 데이터에 사용 되는 저장소 계정과 연결 됩니다. 저장소 계정에 대 한 액세스는 구독 및 두 개의 512 비트 저장소에 의해 제어 됩니다 해당 저장소 계정과 연결 된 키에 액세스 합니다.

키 중 하나는 데이터 상자 Edge 장치를 저장소 계정에 액세스할 때 인증에 사용 됩니다. 주기적으로 키를 회전하도록 다른 키를 예약 상태로 둡니다.

보안상의 이유로 많은 데이터 센터 키 회전이 필요 합니다. 키 회전에 대해 이 모범 사례를 따르는 것이 좋습니다.

- 저장소 계정 키는 저장소 계정의 루트 암호와 비슷합니다. 계정 키를 신중 하 게 보호 합니다. 없는 다른 사용자에 게 암호를 배포, 하드 코드, 또는 다른 사람이 액세스할 수 있는 일반 텍스트로 아무 곳 이나 저장
- [계정 키를 다시 생성](../storage/common/storage-account-manage.md#regenerate-access-keys) 것으로 판단 되는 경우 Azure portal을 사용 하 여 손상 되었을 수 있습니다.
- 회전 차례로 [저장소 계정 키를 동기화](data-box-gateway-manage-shares.md#sync-storage-keys) 정기적으로 하는 데 저장소 계정이 무단으로 액세스 하지 않도록 합니다.
- Azure 관리자는 정기적으로 Azure Portal의 [스토리지] 섹션을 사용해 직접 Storage 계정에 액세스하여 기본 또는 보조 키를 변경하거나 다시 생성해야 합니다.


## <a name="manage-personal-information"></a>개인 정보 관리

Data Box에 지 / 데이터 상자 게이트웨이 서비스는 같은 키 경우 개인 정보를 수집 합니다.

- **주문 세부 정보** – 주문이 만들어지면 사용자의 배송 주소, 이메일, 연락처 정보가 Azure Portal에 저장됩니다. 저장되는 정보는 다음과 같습니다.
  - 담당자 이름
  - 전화 번호
  - Email
  - 주소
  - City
  - 우편 번호
  - 시스템 상태
  - 국가/시/도/지역
  - 배송 추적 번호

    주문 세부 정보는 암호화 되 고 서비스에 저장 합니다. 서비스는 리소스 또는 순서를 명시적으로 삭제 될 때까지 정보를 유지 합니다. 또한 리소스 및 해당 순서의 삭제는 장치는 장치를 Microsoft로 반환 될 때까지 제공 하는 시간에서 차단 됩니다.

- **배송 주소** – 주문이 완료 되 후 Data Box 서비스 UPS 같은 타사 운송 업체를 배송 주소를 제공 합니다.

- **사용자가 공유** -장치에서 사용자는 공유에 상주 하는 데이터를 액세스할 수 있습니다. 공유 데이터에 액세스할 수 있는 사용자의 목록을 표시하고 볼 수 있습니다. 이 목록은 공유를 삭제할 때 삭제 됩니다. 단계에 따라 공유를 삭제 하거나 액세스할 수 있는 사용자의 목록을 보려면 [데이터 상자 가장자리에서 공유 관리](data-box-gateway-manage-shares.md)합니다.

자세한 내용은 [보안 센터](https://www.microsoft.com/trustcenter)에서 Microsoft 개인 정보 취급 방침을 검토합니다.

## <a name="next-steps"></a>다음 단계

[데이터 상자 Edge 장치 배포](data-box-edge-deploy-prep.md)합니다.

