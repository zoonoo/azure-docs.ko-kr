---
title: Azure Front 도어 Standard/Premium SKU 구성에서 사용자 지정 도메인에 대해 HTTPS 구성
description: 이 문서에서는 사용자 지정 도메인을 Azure Front 도어 Standard/Premium SKU에 등록 하는 방법에 대해 알아봅니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: c2edf11939996156c2b589b0b7876ae1b01466e5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740820"
---
# <a name="configure-https-on-a-front-door-standardpremium-sku-preview-custom-domain-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Front 도어 Standard/Premium SKU (미리 보기) 사용자 지정 도메인에서 HTTPS 구성

> [!NOTE]
> 이 설명서는 Azure Front 도어 Standard/Premium (미리 보기)에 대 한 것입니다. Azure Front 문에 대 한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

Azure Front 도어 Standard/Premium은 사용자 지정 도메인을 추가할 때 기본적으로 응용 프로그램에 대 한 보안 TLS 배달을 가능 하 게 합니다. 사용자 지정 도메인에서 HTTPS 프로토콜을 사용 하 여 인터넷을 통해 전송 될 때 중요 한 데이터가 TLS/SSL 암호화를 통해 안전 하 게 배달 되도록 합니다. 웹 브라우저가 HTTPS를 통해 웹 사이트에 연결되면 웹 사이트 보안 인증서의 유효성을 검사하고 합법적인 인증 기관에서 발급되었는지를 확인합니다. 이 프로세스는 보안을 제공하며 공격으로부터 웹 애플리케이션을 보호합니다.

Azure Front 도어 Standard/Premium은 Azure 관리 되는 인증서 및 고객 관리 인증서를 모두 지원 합니다. Azure 전면 도어는 기본적으로 Azure 관리 인증서를 사용 하 여 모든 사용자 지정 도메인에 대해 HTTPS를 자동으로 사용 하도록 설정 합니다. Azure 관리 되는 인증서를 가져오는 데 필요한 추가 단계는 없습니다. 인증서는 도메인 유효성 검사 프로세스 중에 생성 됩니다. Azure 전면 도어 표준/프리미엄을 Key Vault와 통합 하 여 고유한 인증서를 사용할 수도 있습니다.

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [**Microsoft Azure Preview에 대한 추가 사용 약관**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 사용자 지정 도메인에 대해 HTTPS를 구성 하려면 먼저 Azure Front 도어 표준/프리미엄 프로필을 만들어야 합니다. 자세한 내용은 [빠른 시작: Azure Front 도어 표준/프리미엄 프로필 만들기](create-front-door-portal.md)를 참조 하세요.

* 사용자 지정 도메인이 아직 없는 경우 도메인 공급자를 사용 하 여 먼저 구매 해야 합니다. 예를 들어 [사용자 지정 도메인 이름 구매](../../app-service/manage-custom-dns-buy-domain.md)를 참조하세요.

* Azure를 사용 하 여 [dns 도메인](../../dns/dns-overview.md)을 호스트 하는 경우 도메인 공급자의 dns (domain name system)를 Azure DNS에 위임 해야 합니다. 자세한 내용은 [Azure DNS에 도메인 위임](../../dns/dns-delegate-domain-azure-dns.md)을 참조하세요. 그렇지 않고 도메인 공급자를 사용 하 여 DNS 도메인을 처리 하는 경우 DNS TXT 레코드를 입력 하 여 도메인을 수동으로 확인 해야 합니다.

## <a name="azure-managed-certificates"></a>Azure 관리 되는 인증서

1. Azure 전면 도어 표준/프리미엄 프로필에 대 한 설정 아래에서 **도메인** 을 선택 하 고 **+ 추가** 를 선택 하 여 새 도메인을 추가 합니다.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-new-custom-domain.png" alt-text="도메인 구성 방문 페이지의 스크린샷":::

1. **도메인 추가** 페이지에서 *dns 관리* 의 경우 **Azure 관리 되는 dns** 옵션을 선택 합니다. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-domain-azure-managed.png" alt-text="Azure 관리 되는 DNS가 선택 된 도메인 추가 페이지의 스크린샷":::

1. [사용자 지정 도메인](how-to-add-custom-domain.md)사용의 단계에 따라 사용자 지정 도메인의 유효성을 검사 하 고 끝점에 연결 합니다.

1. 사용자 지정 도메인이 끝점에 성공적으로 연결 되 면 Azure 관리 인증서가 전면 도어에 배포 됩니다. 이 프로세스를 완료 하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="using-your-own-certificate"></a>사용자 고유의 인증서 사용

사용자 고유의 TLS 인증서를 사용 하도록 선택할 수도 있습니다. 이 인증서를 Azure Front 도어 Standard/Premium과 함께 사용 하려면 먼저 Azure Key Vault으로 가져와야 합니다. Azure Key Vault에서 [인증서 가져오기](../../key-vault/certificates/tutorial-import-certificate.md) 를 참조 하세요. 

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Azure 키 자격 증명 모음 계정 및 인증서 준비
 
1. 사용자 지정 HTTPS를 사용 하도록 설정 하려는 Azure Front 도어 Standard/Premium과 동일한 구독에서 실행 중인 Azure Key Vault 계정이 있어야 합니다. 아직 Azure Key Vault 계정이 없는 경우 새로 하나 만듭니다.

    > [!WARNING]
    > Azure Front Door는 현재 Front Door 구성과 동일한 구독의 Key Vault 계정만 지원합니다. Azure Front 도어 Standard/Premium과 다른 구독에서 Key Vault를 선택 하면 오류가 발생 합니다.

1. 인증서가 이미 있는 경우 Azure Key Vault 계정에 직접 업로드할 수 있습니다. 그렇지 않으면 Azure Key Vault와 통합 된 파트너 인증 기관 중 하나에서 Azure Key Vault를 통해 직접 새 인증서를 만듭니다. 인증서를 **비밀** 대신 **인증서** 개체로 업로드합니다.

    > [!NOTE]
    > 자체 TLS/SSL 인증서의 경우, Front Door는 EC 암호화 알고리즘을 사용하는 인증서를 지원하지 않습니다.

#### <a name="register-azure-front-door"></a>Azure Front Door 등록

PowerShell을 통해 Azure Active Directory에서 Azure Front Door의 서비스 사용자를 앱으로 등록합니다.

> [!NOTE]
> 이 작업을 수행하려면 글로벌 관리자 권한이 필요하며, 테넌트 당 **한 번** 만 수행하면 됩니다.

1. 필요한 경우 로컬 컴퓨터의 PowerShell에 [Azure PowerShell](/powershell/azure/install-az-ps)을 설치합니다.

1. PowerShell에서 다음 명령을 실행합니다.

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>키 자격 증명 모음에 Azure Front Door 액세스 권한 부여
 
Azure Key Vault 계정의 인증서에 액세스하기 위한 Azure Front Door 사용 권한을 부여합니다.

1. 키 자격 증명 모음 계정의 설정에서 **액세스 정책** 을 선택 합니다. 그런 다음 새로 **추가** 를 선택 하 여 새 정책을 만듭니다.

1. **보안 주체 선택** 에서 **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** 를 검색 하 고 * * AzureFrontDoor-Cdn * *을 선택 합니다. **선택** 을 클릭합니다.

1. **비밀 사용 권한** 에서 **가져오기** 를 선택하여 Front Door가 인증서를 검색하도록 허용합니다.

1. **인증서 사용 권한** 에서 **가져오기** 를 선택하여 Front Door가 인증서를 검색하도록 허용합니다.

1. **확인** 을 선택합니다. 

#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>배포할 Azure Front Door 인증서 선택
 
1. 포털의 Azure Front 도어 Standard/Premium으로 돌아갑니다.

1. *설정* 아래의 **비밀** 으로 이동 하 여 **인증서 추가** 를 선택 합니다.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate.png" alt-text="Azure 전면 도어 비밀 방문 페이지의 스크린샷":::

1. **인증서 추가** 페이지에서 Azure Front 도어 Standard/Premium에 추가 하려는 인증서의 확인란을 선택 합니다. 버전 선택을 "최신"으로 유지 하 고 **추가** 를 선택 합니다. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate-page.png" alt-text="인증서 추가 페이지의 스크린샷":::

1. 인증서가 성공적으로 프로 비전 되 면 새 사용자 지정 도메인을 추가할 때 사용할 수 있습니다.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/successful-certificate-provisioned.png" alt-text="인증서의 스크린샷이 암호에 성공적으로 추가 되었습니다.":::

1. *설정* 아래에서 **도메인** 으로 이동 하 고 **+ 추가** 를 선택 하 여 새 사용자 지정 도메인을 추가 합니다. **도메인 추가** 페이지에서 "직접 인증서 가져오기 (byoc) *"를 선택 합니다.* *비밀* 의 경우 드롭다운에서 사용 하려는 인증서를 선택 합니다. 

    > [!NOTE]
    > 선택한 인증서에는 추가할 사용자 지정 도메인과 동일한 CN (일반 이름)이 있어야 합니다.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-custom-domain-https.png" alt-text="HTTPS를 사용 하 여 사용자 지정 도메인 페이지 추가의 스크린샷":::

1. 화면의 지시에 따라 인증서의 유효성을 검사 합니다. 그런 다음 [사용자 지정 도메인 가이드 만들기](how-to-add-custom-domain.md) 에 설명 된 대로 새로 만든 사용자 지정 도메인을 끝점에 연결 합니다.

#### <a name="change-from-azure-managed-to-bring-your-own-certificate-byoc"></a>Azure에서 관리를 변경 하 여 사용자 고유의 인증서 (BYOC) 가져오기

1. 인증서 상태를 선택 하 여 **인증서 세부 정보** 페이지를 열면 기존 Azure 관리 인증서를 사용자 관리 인증서로 변경할 수 있습니다.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/domain-certificate.png" alt-text="도메인 방문 페이지의 인증서 상태 스크린샷" lightbox="../media/how-to-configure-https-custom-domain/domain-certificate-expanded.png":::

1. **인증서 세부 정보** 페이지에서 "Azure 관리"를 "사용자 고유의 인증서 (Byoc) 가져오기" 옵션으로 변경할 수 있습니다. 그런 다음 이전 단계와 동일한 단계를 수행 하 여 인증서를 선택 합니다. **업데이트** 를 선택 하 여 연결 된 인증서를 도메인으로 변경 합니다.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/certificate-details-page.png" alt-text="인증서 세부 정보 페이지의 스크린샷":::

## <a name="next-steps"></a>다음 단계

[Azure 전면 도어 표준/프리미엄을 사용한 캐싱에](concept-caching.md)대해 알아보세요.
