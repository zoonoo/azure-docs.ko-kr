---
title: 기존 정문 - Azure 포털에 루트 또는 정점 도메인온보온
description: Azure 포털을 사용하여 기존 정문에 루트 또는 정점 도메인을 온보답게 하는 방법을 알아봅니다.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 4b74338f22a82d76ef13126ee0862b841bd89a99
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878887"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>정문에 루트 또는 정점 도메인 온보로
Azure Front Door는 CNAME 레코드를 사용하여 사용자 지정 도메인온보딩에 대한 도메인 소유권의 유효성을 검사합니다. 또한 Front Door는 정문 프로필과 연결된 프런트 엔드 IP 주소를 노출하지 않으므로 정점 도메인을 Azure 정문에 온보온하는 경우 IP 주소에 매핑할 수 없습니다.

DNS 프로토콜은 영역 루트에서 CNAME 레코드가 할당되는 것을 방지합니다. 예를 들어 도메인이 `contoso.com`다음과 같은 경우 에 대한 CNAME `somelabel.contoso.com`레코드를 만들 수 있습니다. 하지만 자체적으로 CNAME을 `contoso.com` 만들 수는 없습니다. 이 제한은 Azure 정문 뒤에 로드 균형 응용 프로그램이 있는 응용 프로그램 소유자에게 문제가 발생합니다. 정문 프로파일을 사용하려면 CNAME 레코드를 작성해야 하므로 영역 정점에서 정문 프로파일을 가리킬 수 없습니다.

이 문제는 Azure DNS의 별칭 레코드를 사용하여 해결됩니다. CNAME 레코드와 달리 별칭 레코드는 영역 정점에 생성되며 응용 프로그램 소유자는 이를 사용하여 영역 정점 레코드를 공용 끝점이 있는 Front Door 프로파일로 가리킬 수 있습니다. 응용 프로그램 소유자는 DNS 영역 내의 다른 도메인에 사용되는 동일한 전면 문 프로필을 가리킵니다. 예를 `contoso.com` 들어, `www.contoso.com` 동일한 정문 프로파일을 가리킬 수 있습니다. 

정점 또는 루트 도메인을 Front Door 프로필에 매핑하려면 기본적으로 CNAME 병합 또는 DNS 추적이 필요하며, 이 메커니즘은 DNS 공급자가 IP 주소에 닿을 때까지 CNAME 항목을 재귀적으로 해결합니다. 이 기능은 전면 도어 끝점에 대한 Azure DNS에서 지원됩니다. 

> [!NOTE]
> CNAME 병합 또는 DNS 추적을 지원하는 다른 DNS 공급자도 있지만 Azure Front Door는 도메인을 호스팅하기 위해 고객에게 Azure DNS를 사용하는 것이 좋습니다.

Azure 포털을 사용하여 정문에서 정점 도메인을 온보데 사용하고 TLS 종료 인증서와 연결하여 HTTPS를 활성화할 수 있습니다. 에이펙스 도메인은 루트 또는 벌거 벗은 도메인이라고도 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 정문 프로필을 가리키는 별칭 레코드 만들기
> * 정문에 루트 도메인 추가
> * 루트 도메인에 HTTPS 설정

> [!NOTE]
> 이 자습서에서는 이미 정문 프로필을 만들어야 합니다. 빠른 시작과 같은 다른 자습서를 [참조: 정문 만들기](./quickstart-create-front-door.md) 또는 시작 HTTPS [리디렉션에 HTTP와 정문 만들기.](./front-door-how-to-redirect-https.md)

## <a name="create-an-alias-record-for-zone-apex"></a>영역 정점에 대한 별칭 레코드 만들기

1. 도메인이 온보딩할 **Azure DNS** 구성을 엽니다.
2. 영역 정점에 대한 레코드를 만들거나 편집합니다.
3. 레코드 **유형을** _A_ 레코드로 선택한 다음 **별칭 레코드 집합에**대해 _예를_ 선택합니다. **별칭 형식은** _Azure 리소스로_설정해야 합니다.
4. 정문 프로필이 호스팅되는 Azure 구독을 선택한 다음 Azure **리소스** 드롭다운에서 정문 리소스를 선택합니다.
5. **확인을** 클릭하여 변경 내용을 제출합니다.

    ![영역 정점에 대한 별칭 레코드](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. 위의 단계는 정문 리소스를 가리키는 영역 정점 레코드와 정문 프로필에서 도메인 온보딩에 사용되는 CNAME 레코드 매핑 'afdverify'(예 - `afdverify.contosonews.com`)를 `afdverify.<name>.azurefd.net` 만듭니다.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>정문에서 사용자 지정 도메인 온보딩

1. 정문 디자이너 탭에서 프런트 엔드 호스트 섹션에서 '+' 아이콘을 클릭하여 새 맞춤 도메인을 추가합니다.
2. 사용자 지정 호스트 이름 필드에 루트 또는 정점 `contosonews.com`도메인 이름을 입력합니다.
3. 도메인에서 정문으로의 CNAME 매핑의 유효성이 검사되면 **추가를** 클릭하여 사용자 지정 도메인을 추가합니다.
4. 변경 내용을 제출하려면 **저장을** 클릭합니다.

![사용자 지정 도메인 메뉴](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>사용자 지정 도메인에서 HTTPS 사용

1. 추가된 사용자 지정 도메인을 클릭하고 섹션 에서 **사용자 지정 도메인 HTTPS에서**상태를 **사용 으로**변경합니다.
2. 인증서 **관리 유형을** 선택하여 _'내 인증서 사용'을 선택합니다._

> [!WARNING]
> 정문 관리 인증서 관리 유형은 현재 정점 또는 루트 도메인에 대해 지원되지 않습니다. 정문 또는 정문 에 대한 루트 도메인에서 HTTPS를 사용하도록 설정하는 데 사용할 수 있는 유일한 옵션은 Azure Key Vault에서 호스팅되는 사용자 지정 TLS/SSL 인증서를 사용하는 것입니다.

3. 다음 단계로 진행하기 전에 UI에 표시된 대로 정문이 키 볼트에 액세스할 수 있는 올바른 권한을 설정해야 합니다.
4. 현재 구독에서 **Key Vault 계정을** 선택한 다음 적절한 **비밀** 및 **비밀 버전을** 선택하여 올바른 인증서에 매핑합니다.
5. **업데이트를** 클릭하여 선택 영역을 저장한 다음 **저장을**클릭합니다.
6. 몇 분 후에 **새로 고침을** 클릭한 다음 사용자 지정 도메인을 다시 클릭하여 인증서 프로비저닝의 진행 상황을 확인합니다. 

> [!WARNING]
> 정점 도메인에 적합한 라우팅 규칙을 만들었거나 기존 라우팅 규칙에 도메인을 추가해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.