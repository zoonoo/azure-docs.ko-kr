---
title: 자체 호스팅 Azure API 관리 게이트웨이에 대한 사용자 지정 도메인 이름 구성 | 마이크로 소프트 문서
description: 이 항목에서는 자체 호스팅 Azure API Management 게이트웨이에 대한 사용자 지정 도메인 이름을 구성하는 단계를 설명합니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 1f2184c7c62887a98a76877528b167d173c3d75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335941"
---
# <a name="configure-a-custom-domain-name"></a>사용자 지정 도메인 이름 구성

자체 호스팅 [Azure API Management 게이트웨이를](self-hosted-gateway-overview.md) 프로비전할 때 호스트 이름이 할당되지 않으며 IP 주소에서 참조해야 합니다. 이 문서에서는 기존 사용자 지정 DNS 이름(hostname이라고도 함)을 자체 호스팅 게이트웨이에 매핑하는 방법을 보여 주며 있습니다.

> [!NOTE]
> 자체 호스팅 게이트웨이 기능이 미리 보기 상태입니다. 미리 보기 중에 자체 호스팅 게이트웨이는 추가 비용 없이 개발자 및 프리미엄 계층에서만 사용할 수 있습니다. 개발자 계층은 단일 자체 호스팅 게이트웨이 배포로 제한됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에 설명한 단계를 수행하려면 다음 항목이 있어야 합니다.

-   활성화된 Azure 구독.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API 관리 인스턴스입니다. 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.
- 자체 호스팅 게이트웨이입니다. 자세한 내용은 [자체 호스팅 게이트웨이프로비전 방법을](api-management-howto-provision-self-hosted-gateway.md) 참조하세요.
-   사용자 또는 조직이 소유한 사용자 지정 도메인 이름입니다. 이 항목에서는 사용자 지정 도메인 이름을 조달하는 방법에 대한 지침을 제공하지 않습니다.
-   사용자 지정 도메인 이름을 자체 호스팅 게이트웨이의 IP 주소에 매핑하는 DNS 서버에서 호스팅되는 DNS 레코드입니다. 이 항목에서는 DNS 레코드를 호스트하는 방법에 대한 지침을 제공하지 않습니다.
-   공개 및 프라이빗 키(.PFX)가 있는 유효한 인증서가 있어야 합니다. SAN(주체 또는 주체 대체 이름)은 도메인 이름과 일치해야 합니다(이를 통해 API Management 인스턴스가 TLS를 통해 URL을 안전하게 노출할 수 있음).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>API 관리 서비스에 사용자 지정 도메인 인증서 추가

1. **보안**아래에서 **인증서를** 선택합니다.
2. **+ 추가**를 선택합니다.
3. **인증서의** 리소스 이름을 Id 필드에 입력합니다.
4. 인증서가 포함된 파일을 선택합니다(. PFX)를 선택하여 **인증서** 필드 또는 폴더 아이콘을 선택합니다.
5. 인증서의 암호를 **암호** 필드에 입력합니다.
6. API 관리 서비스에 인증서를 추가하려면 **만들기를** 선택합니다.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Azure 포털을 사용하여 자체 호스팅 게이트웨이에 대한 사용자 지정 도메인 이름 설정

1. **설정**에서 **게이트웨이를 선택합니다.**
2. 도메인 이름을 구성할 자체 호스팅 게이트웨이를 선택합니다.
3. **설정**에서 **호스트 이름을** 선택합니다.
4. **+ 추가** 선택
5. 호스트 이름에 대한 리소스 이름을 **Name** 필드에 입력합니다.
6. **호스트 이름** 필드에 도메인 이름을 입력합니다.
7. 인증서 드롭다운에서 **인증서를** 선택합니다.
8. 이 **게이트웨이를** 통해 노출된 API가 클라이언트 인증서 인증을 사용하는 경우 클라이언트 인증서 확인란 을 협상합니다.
    > [!WARNING]
    > 이 설정은 게이트웨이에 대해 구성된 모든 도메인 이름으로 공유됩니다.
9. **추가를** 선택하여 선택한 자체 호스팅 게이트웨이에 사용자 지정 도메인 이름을 할당합니다.

## <a name="next-steps"></a>다음 단계

[서비스 업그레이드 및 크기 조정](upgrade-and-scale.md)
