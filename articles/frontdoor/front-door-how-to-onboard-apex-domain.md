---
title: 기존 Front Door에 루트 또는 apex 도메인 온보딩 - Azure Portal
description: Azure Portal을 사용하여 기존 Front Door에 루트 또는 apex 도메인을 온보딩하는 방법을 알아봅니다.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 11/13/2020
ms.author: duau
ms.openlocfilehash: 55eefe7a7490df050aa7ebc2bb41fbadcc8d8279
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94646341"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Front Door에서 루트 또는 Apex 도메인 온보드
Azure Front Door는 CNAME 레코드를 사용하여 사용자 지정 도메인의 온보딩에 대한 도메인 소유권의 유효성을 검사합니다. Front Door는 Front Door 프로필과 연결된 프런트 엔드 IP 주소를 노출하지 않습니다. 따라서 Azure Front Door에 온보딩하려는 경우, apex 도메인을 IP 주소에 매핑할 수 없습니다.

DNS 프로토콜은 영역 루트에서 CNAME 레코드가 할당되는 것을 방지합니다. 예를 들어, 도메인이 `contoso.com`인 경우 `somelabel.contoso.com`에 대한 CNAME 레코드를 생성할 수 있지만 `contoso.com` 자체에 대해서는 CNAME를 생성할 수 없습니다. 이 제한은 Azure Front Door 뒤에 부하가 분산된 응용 프로그램이 있는 응용 프로그램 소유자에게 문제가 됩니다. Front Door 프로필을 사용하려면 CNAME 레코드를 만들어야 하므로 영역 루트에서 Front Door 프로필을 가리킬 수 없습니다.

이 문제는 Azure DNS 별칭 레코드를 사용하여 해결할 수 있습니다. CNAME 레코드와 달리 별칭 레코드는 영역 루트에 생성됩니다. 이제 응용 프로그램 소유자는 영역 루트 레코드에서 공용 엔드포인트가 있는 Front Door 프로필을 가리키도록 할 수 있습니다. 응용 프로그램 소유자는 DNS 영역 내 다른 모든 도메인에 사용되는 Front Door 프로필을 가리킵니다. 예를 들어 `contoso.com` 및 `www.contoso.com`은 동일한 Front Door 프로필을 가리킬 수 있습니다. 

루트 또는 루트 도메인을 Front Door 프로필에 매핑하려면 CNAME 평면화 또는 DNS 추적이 필요합니다. IP 주소에 도달할 때까지 DNS 공급자가 CNAME 항목을 재귀적으로 확인하는 메커니즘입니다. 이 기능은 Front Door 엔드포인트에 대한 Azure DNS에서 지원됩니다. 

> [!NOTE]
> CNAME 평면화 또는 DNS 추적을 지원하는 다른 DNS 공급자도 있습니다. 그러나 Azure Front Door는 고객의 도메인 호스팅을 위해 Azure DNS를 사용하는 것을 권장합니다.

Azure Portal를 사용하여 Front Door에서 루트 도메인을 등록하고 TLS 종료를 위한 인증서와 연결하여 HTTPS를 사용하도록 설정할 수 있습니다. 루트 도메인은 루트 또는 naked 도메인으로도 불립니다.

## <a name="create-an-alias-record-for-zone-apex"></a>영역 루트에 대한 별칭 레코드 생성하기

1. 등록할 도메인에 대한 **Azure DNS** 구성을 엽니다.

1. 영역 apex에 대한 레코드를 만들거나 편집합니다.

1. 레코드 **종류** 를 *A* 레코드로 선택한 다음, **별칭 레코드 집합** 에 대해 *예* 를 선택합니다. **별칭 유형** 은 *Azure 리소스* 로 설정되어야 합니다.

1. Front Door 프로필이 호스트되는 Azure 구독을 선택합니다. 그런 다음 **Azure 리소스** 드롭다운에서 Front Door 리소스를 선택합니다.

1. **확인** 을 선택하여 변경 내용을 제출합니다.

    :::image type="content" source="./media/front-door-apex-domain/front-door-apex-alias-record.png" alt-text="영역 루트에 대한 별칭 레코드":::

1. 위의 단계에서는 Front Door 리소스를 가리키는 영역 apex 레코드를 생성하고, Front Door 프로필에 도메인을 등록하는 데 사용되는 CNAME 레코드 매핑 ' afdverify '(예:`afdverify.contosonews.com`)를 생성합니다.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Front Door에 사용자 지정 도메인 온보딩

1. Front Door 디자이너 탭의 Frontend 호스트 섹션에서 ' + ' 아이콘을 선택하여 새 사용자 지정 도메인을 추가합니다.

1. 사용자 지정 호스트 이름 필드에 루트 또는 apex 도메인 이름을 입력합니다(예: `contosonews.com`).

1. 도메인에서 Front Door로의 CNAME 매핑을 확인 한 후 **추가** 를 선택하여 사용자 지정 도메인을 추가합니다.

1. **저장** 을 선택하여 변경 내용을 제출합니다.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-domain.png" alt-text="사용자 지정 도메인 메뉴":::

## <a name="enable-https-on-your-custom-domain"></a>사용자 지정 도메인에 HTTPS를 사용하도록 설정

1. 추가된 사용자 지정 도메인을 선택하고 **사용자 지정 도메인 HTTPS** 섹션에서 상태를 **사용** 으로 변경합니다.

1. **인증서 관리 유형** 을 *'자체 인증서 사용'* 으로 선택합니다.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-custom-domain.png" alt-text="사용자 지정 도메인 HTTPS 설정":::    

   > [!WARNING]
   > Apex 또는 루트 도메인에 대해 현재 Front Door 관리 인증서 관리 유형이 지원되지 않습니다. Apex 또는 루트 도메인에서 HTTPS를 사용하도록 설정하는 데 사용할 수 있는 유일한 옵션은 Azure Key Vault에서 호스트되는 사용자 지정 TLS/SSL 인증서를 사용하는 것입니다.

1. 다음 단계로 진행하기 전에 UI에 명시된 대로 키 자격 증명 모음에 액세스할 수 있도록 Front Door에 올바른 권한을 설정했는지 확인합니다.

1. 현재 구독에서 **Key Vault 계정** 을 선택한 다음 적절한 **암호** 및 **암호 버전** 을 선택하여 올바른 인증서에 매핑합니다.

1. **업데이트** 를 선택하여 선택 항목을 저장하고 **저장** 을 선택합니다.

1. 몇 분 후에 **새로 고침** 을 선택하고 사용자 지정 도메인을 다시 선택하여 인증서 프로비전의 진행 상태를 확인합니다. 

> [!WARNING]
> Apex 도메인에 대한 적절한 라우팅 규칙을 만들거나 기존 라우팅 규칙에 도메인을 추가했는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
