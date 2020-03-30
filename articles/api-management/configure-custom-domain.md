---
title: Azure API 관리 인스턴스에 대한 사용자 지정 도메인 이름 구성
titleSuffix: Azure API Management
description: 이 항목에서는 Azure API Management 인스턴스에 대해 사용자 지정 도메인 이름을 구성하는 방법에 대해 설명합니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 4587909ad6fca6cdf21d54d11d89f797bbb29833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335844"
---
# <a name="configure-a-custom-domain-name"></a>사용자 지정 도메인 이름 구성

Azure API 관리 서비스 인스턴스를 만들 때 Azure는 `azure-api.net` 이를 하위 `apim-service-name.azure-api.net`도메인(예: )의 하위 도메인으로 할당합니다. 그러나 **contoso.com**와 같은 사용자 지정 도메인 이름을 사용하여 API 관리 끝점을 노출할 수 있습니다. 이 자습서에서는 API 관리 인스턴스에서 노출되는 끝점에 기존 사용자 지정 DNS 이름을 매핑하는 방법을 보여 주었습니다.

> [!IMPORTANT]
> API Management는 기본 도메인 이름 또는 구성된 사용자 지정 도메인 이름과 일치하는 [호스트 헤더](https://tools.ietf.org/html/rfc2616#section-14.23) 값이 있는 요청만 허용합니다.

> [!WARNING]
> 인증서 고정을 사용하여 응용 프로그램의 보안을 향상하려는 고객은 기본 인증서가 아닌 관리하는 사용자 지정 도메인 이름과 인증서를 사용해야 합니다. 대신 기본 인증서를 고정하는 고객은 제어하지 않는 인증서의 속성에 대한 하드 종속성을 취하게 되며 권장되는 방법은 아닙니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에 설명한 단계를 수행하려면 다음 항목이 있어야 합니다.

-   활성화된 Azure 구독.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API 관리 인스턴스입니다. 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.
-   사용자 또는 조직이 소유한 사용자 지정 도메인 이름입니다. 이 항목에서는 사용자 지정 도메인 이름을 조달하는 방법에 대한 지침을 제공하지 않습니다.
-   사용자 지정 도메인 이름을 API 관리 인스턴스의 기본 도메인 이름에 매핑하는 DNS 서버에서 호스팅되는 CNAME 레코드입니다. 이 항목에서는 CNAME 레코드를 호스트하는 방법에 대한 지침을 제공하지 않습니다.
-   공개 및 프라이빗 키(.PFX)가 있는 유효한 인증서가 있어야 합니다. SAN(주체 또는 주체 대체 이름)은 도메인 이름과 일치해야 합니다(이를 통해 API Management 인스턴스가 TLS를 통해 URL을 안전하게 노출할 수 있음).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Azure Portal을 사용하여 사용자 지정 도메인 이름 설정

1. [Azure 포털에서](https://portal.azure.com/)API 관리 인스턴스로 이동합니다.
1. **사용자 지정 도메인을 선택합니다.**

    사용자 지정 도메인 이름을 할당할 수 있는 끝점이 많이 있습니다. 현재는 다음 엔드포인트를 사용할 수 있습니다.

    - **Gateway** 게이트웨이(기본값은: `<apim-service-name>.azure-api.net`)
    - **포털**(기본값: `<apim-service-name>.portal.azure-api.net`),
    - **관리**(기본값: `<apim-service-name>.management.azure-api.net`),
    - **SCM** SCM(기본값은 `<apim-service-name>.scm.azure-api.net`다음과 다)
    - **NewPortal** NewPortal(기본값은: `<apim-service-name>.developer.azure-api.net`)입니다.

    > [!NOTE]
    > **게이트웨이** 끝점만 소비 계층의 구성에 사용할 수 있습니다.
    > 이들 중 일부 또는 모든 엔드포인트를 업데이트할 수 있습니다. 일반적으로 고객은 **게이트웨이(이** URL은 API 관리를 통해 노출된 API를 호출하는 데 사용됨)와 **포털(개발자** 포털 URL)을 업데이트합니다.
    > **관리** 및 **SCM** 끝점은 API 관리 인스턴스 소유자만 내부적으로 사용되므로 사용자 지정 도메인 이름이 덜 자주 할당됩니다.
    > **프리미엄** 계층은 **게이트웨이** 끝점에 대해 여러 호스트 이름 설정을 지원합니다.

1. 업데이트하려는 엔드포인트를 선택합니다.
1. 오른쪽 창에서 **사용자 지정**을 클릭합니다.

    - **사용자 지정 도메인 이름**에서 사용하려는 이름을 지정합니다. `api.contoso.com`)을 입력합니다.
    - **인증서에서**키 자격 증명 모음에서 인증서를 선택합니다. 유효한 을 업로드할 수도 있습니다. PFX 파일 및 **해당 암호를**제공 , 인증서가 암호로 보호되는 경우.

    > [!NOTE]
    > 와일드카드 도메인 이름은 예를 들어 `*.contoso.com` 소비 계층을 제외한 모든 계층에서 지원됩니다.

    > [!TIP]
    > 인증서를 관리하고 자동 회전하도록 설정하려면 Azure Key Vault를 사용하는 것이 좋습니다.
    > Azure Key Vault를 사용하여 사용자 지정 도메인 TLS/SSL 인증서를 관리하는 경우 인증서가 _비밀이_아닌 키 [볼트에 _인증서로_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate)삽입되어 있는지 확인합니다.
    >
    > TLS/SSL 인증서를 가져오려면 API 관리 목록에 있어야 하며 인증서가 포함된 Azure 키 자격 증명 모음에 대한 비밀 권한을 가져와야 합니다. Azure 포털을 사용하는 경우 필요한 모든 구성 단계가 자동으로 완료됩니다. 명령줄 도구 또는 관리 API를 사용하는 경우 이러한 권한을 수동으로 부여해야 합니다. 이 작업은 다음 두 단계로 수행됩니다. 먼저 API 관리 인스턴스에서 관리되는 ID 페이지를 사용하여 관리되는 ID가 활성화되어 있는지 확인하고 해당 페이지에 표시된 주 ID를 기록합니다. 둘째, 권한 목록을 제공하고 인증서가 포함된 Azure Key Vault에서 이 보안 주체 ID에 대한 비밀 권한을 가져옵니다.
    >
    > 인증서가 자동 회전하도록 설정된 경우 API 관리는 서비스에 대한 가동 중지 시간 없이 자동으로 최신 버전을 선택합니다(API 관리 계층에 SLA가 있는 경우 즉, 개발자 계층을 제외한 모든 계층에서).

1. 적용을 클릭합니다.

    > [!NOTE]
    > 배포 크기에 따라, 인증서를 할당하는 과정에 15분 이상 소요될 수 있습니다. 개발자 SKU에는 가동 중지 시간이 있고 기본 및 상위 SKU에는 가동 중지 시간이 없습니다.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS 구성

사용자 지정 도메인 이름에 대한 DNS를 구성할 때 다음 두 가지 옵션이 있습니다.

-   구성된 사용자 지정 도메인 이름의 끝점을 가리키는 CNAME 레코드를 구성합니다.
-   API 관리 게이트웨이 IP 주소를 가리키는 A 레코드를 구성합니다.

> [!NOTE]
> API Managment 인스턴스 IP 주소는 정적이지만 몇 가지 시나리오에서 변경될 수 있습니다. 따라서 사용자 지정 도메인을 구성할 때 CNAME을 사용하는 것이 좋습니다. DNS 구성 방법을 선택할 때이 고려. IP 문서 [문서](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) 및 API [관리 FAQ에서](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)자세히 읽어보십시오.

## <a name="next-steps"></a>다음 단계

[서비스 업그레이드 및 크기 조정](upgrade-and-scale.md)
