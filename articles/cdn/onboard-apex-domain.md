---
title: 루트 또는 apex 도메인을 기존 Azure CDN 끝점에 등록-Azure Portal
description: Azure Portal를 사용 하 여 루트 또는 apex 도메인을 기존 Azure CDN 끝점에 등록 하는 방법에 대해 알아봅니다.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 8ab4f698c7149d8d57f790e221ccbe35ec090fe6
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370354"
---
# <a name="onboard-a-root-or-apex-domain-to-an-existing-azure-cdn-endpoint"></a>루트 또는 apex 도메인을 기존 Azure CDN 끝점에 등록

Azure CDN는 CNAME 레코드를 사용 하 여 사용자 지정 도메인 등록을 위한 도메인 소유권의 유효성을 검사 합니다. CDN은 CDN 프로필과 연결 된 프런트 엔드 IP 주소를 노출 하지 않습니다. Apex 도메인을 Azure CDN에 등록 하려는 경우 IP 주소에 매핑할 수 없습니다.

DNS 프로토콜은 영역 루트에서 CNAME 레코드가 할당되는 것을 방지합니다. 예를 들어 도메인이 인 경우 `contoso.com` 에 대 한 cname 레코드를 만들 수 `somelabel.contoso.com` 있지만 자체에 대해서는 cname을 만들 수 없습니다 `contoso.com` . 이 제한은 Azure CDN 뒤에 부하가 분산 된 응용 프로그램이 있는 응용 프로그램 소유자에 게 문제를 제공 합니다. CDN 프로필을 사용 하려면 CNAME 레코드를 만들어야 하므로 apex 영역에서 CDN 프로필을 가리킬 수 없습니다.

이 문제는 Azure DNS의 별칭 레코드를 사용 하 여 해결할 수 있습니다. CNAME 레코드와 달리 별칭 레코드는 apex 영역에 생성 됩니다. 응용 프로그램 소유자는이를 사용 하 여 영역 apex 레코드를 공용 끝점이 있는 CDN 프로필로 가리킬 수 있습니다. 응용 프로그램 소유자는 DNS 영역 내의 다른 도메인에 사용 되는 것과 동일한 CDN 프로필을 가리킵니다. 예를 들어 `contoso.com` 및는 `www.contoso.com` 동일한 CDN 프로필을 가리킬 수 있습니다. 

Apex 또는 루트 도메인을 CDN 프로필에 매핑하려면 CNAME 평면화 또는 DNS를 추적 해야 합니다. IP 주소에 도달할 때까지 DNS 공급자가 CNAME 항목을 재귀적으로 확인 하는 메커니즘입니다. 이 기능은 CDN 끝점에 대 한 Azure DNS에서 지원 됩니다. 

> [!NOTE]
> CNAME 평면화 또는 DNS 추적을 지 원하는 다른 DNS 공급자도 있습니다. 그러나 해당 도메인을 호스트 하기 위해 고객에 게 Azure DNS를 사용 하는 것이 좋습니다 Azure CDN.

Azure Portal를 사용 하 여 CDN에서 apex 도메인을 등록 하 고 TLS 종료를 위한 인증서와 연결 하 여 HTTPS를 사용 하도록 설정할 수 있습니다. Apex 도메인은 root 또는 naked 도메인도 불립니다.

## <a name="create-an-alias-record-for-zone-apex"></a>영역 apex에 대 한 별칭 레코드 만들기

1. 등록 도메인에 대 한 **Azure DNS** 구성을 엽니다.

2. **+ 레코드 집합** 을 선택합니다.

3. **레코드 집합 추가** 에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ------|
    | Name | **@** 을 입력 합니다. |
    | 유형 | **을** 선택 합니다. |
    | 별칭 레코드 집합 | **예** 를 선택합니다. |
    | 별칭 형식 | **Azure 리소스** 를 선택 합니다. |
    | 구독을 선택합니다. | 구독을 선택합니다. |
    | Azure 리소스 | CDN 끝점을 선택 합니다. |

4. **TTL** 값을 입력 합니다.

5. **확인** 을 선택 하 여 변경 내용을 제출 합니다.

    :::image type="content" source="./media/onboard-apex-domain/cdn-apex-alias-record.png" alt-text="영역 apex에 대 한 별칭 레코드":::

6. 위의 단계에서는 CDN 리소스를 가리키는 영역 apex 레코드를 만듭니다. CNAME 레코드 매핑 **cdnverify** 는 CDN 프로필에 도메인을 등록 하는 데 사용 됩니다.
    1. 예: **cdnverify.contoso.com**.
    

## <a name="onboard-the-custom-domain-on-your-cdn"></a>CDN에서 사용자 지정 도메인 등록

사용자 지정 도메인을 등록한 후에 CDN 엔드포인트에 추가할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 사용자 지정 도메인에 매핑하려는 엔드포인트를 포함하는 CDN 프로필로 이동합니다.
    
2. **CDN 프로필** 페이지에서 사용자 지정 도메인과 연결할 CDN 엔드포인트를 선택합니다.

    :::image type="content" source="media/onboard-apex-domain/cdn-endpoint-selection.png" alt-text="CDN 끝점 선택" border="true":::
    
3. **+ 사용자 지정 도메인** 을 선택 합니다. 

   :::image type="content" source="media/onboard-apex-domain/cdn-custom-domain-button.png" alt-text="사용자 지정 도메인 추가 단추" border="true":::

4. **사용자 지정 도메인 추가** 에서 **끝점 호스트** 이름이 미리 채워지고 CDN 끝점 URL: **\<endpoint-hostname>** . azureedge.net에서 파생 됩니다. 이는 변경할 수 없습니다.

5. **사용자 지정 호스트 이름** 에 대해 CNAME 레코드의 원본 도메인으로 사용할 사용자 지정 root 또는 apex 도메인을 입력 합니다. 
    1. 예를 들면 **contoso.com** 입니다. **Cdnverify 하위 도메인 이름을 사용 하지 마세요**.

    :::image type="content" source="media/onboard-apex-domain/cdn-add-custom-domain.png" alt-text="사용자 지정 도메인 추가" border="true":::

6. **추가** 를 선택합니다.

   Azure에서 입력한 사용자 지정 도메인 이름에 대한 CNAME 레코드가 있는지 확인합니다. CNAME이 올바르면 사용자 지정 도메인의 유효성이 검사됩니다. 

   새 사용자 지정 도메인 설정이 모든 CDN 에지 노드에 전파되려면 다소 시간이 걸릴 수 있습니다. 
    - **Microsoft의 Azure CDN 표준** 프로필의 경우 일반적으로 10분 이내에 전파가 완료됩니다. 
    - **Akamai의 Azure CDN Standard** 프로필의 경우, 일반적으로 1분 이내에 전파가 완료됩니다. 
    - **Verizon의 Azure CDN 표준** 및 **Verizon의 Azure CDN 프리미엄** 프로필의 경우 일반적으로 10분 이내에 전파가 완료됩니다.   

## <a name="enable-https-on-your-custom-domain"></a>사용자 지정 도메인에서 HTTPS를 사용 하도록 설정

Azure CDN에 대 한 사용자 지정 도메인에서 HTTPS를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [자습서: Azure CDN 사용자 지정 도메인에서 Https 구성](cdn-custom-ssl.md) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [CDN 끝점을 만드는](cdn-create-new-endpoint.md)방법에 대해 알아봅니다.
