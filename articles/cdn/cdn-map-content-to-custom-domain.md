---
title: '자습서: 엔드포인트에 사용자 지정 도메인 추가'
titleSuffix: Azure Content Delivery Network
description: 이 자습서를 사용하여 도메인 이름이 URL에 표시되도록 Azure Content Delivery Network 엔드포인트에 사용자 지정 도메인을 추가합니다.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 11/06/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 03ed47ee97f52aca708118f202fad583753549bf
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331232"
---
# <a name="tutorial-add-a-custom-domain-to-your-endpoint"></a>자습서: 엔드포인트에 사용자 지정 도메인 추가

이 자습서에서는 Azure CDN(Content Delivery Network) 엔드포인트에 사용자 지정 도메인을 추가하는 방법을 보여줍니다. 

CDN 프로필의 엔드포인트 이름은 azureedge.net의 하위 도메인입니다. 기본적으로 콘텐츠를 전송할 때 CDN 프로필 도메인이 URL에 포함됩니다.

예를 들어 **https://contoso.azureedge.net/photo.png** 와 같습니다.

Azure CDN은 CDN 엔드포인트와 사용자 지정 도메인을 연결하는 옵션을 제공합니다. 이 옵션은 URL에서 기본 도메인 대신 사용자 지정 도메인을 사용하여 콘텐츠를 전송합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> - CNAME DNS 레코드 만들기.
> - CDN 엔드포인트와 사용자 지정 도메인 연결.
> - 사용자 지정 도메인 확인.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

* 이 자습서의 단계를 완료하려면 먼저 CDN 프로필과 하나 이상의 CDN 엔드포인트를 만들어야 합니다. 
    * 자세한 내용은 [빠른 시작: Azure CDN 프로필 및 엔드포인트 만들기](cdn-create-new-endpoint.md)를 참조하세요.

* 사용자 지정 도메인이 없으면 도메인 공급자를 통해 구매합니다. 
    * 자세한 내용은 [사용자 지정 도메인 이름 구매](../app-service/manage-custom-dns-buy-domain.md)를 참조하세요.

* Azure를 사용하여 [DNS 도메인](../dns/dns-overview.md)을 호스트하는 경우 사용자 지정 도메인을 Azure DNS에 위임합니다. 
    * 자세한 내용은 [Azure DNS에 도메인 위임](../dns/dns-delegate-domain-azure-dns.md)을 참조하세요.

* 도메인 공급자를 사용하여 DNS 도메인을 처리하는 경우 [CNAME DNS 레코드 만들기](#create-a-cname-dns-record)를 계속 진행합니다.


## <a name="create-a-cname-dns-record"></a>CNAME DNS 레코드 만들기

Azure CDN 엔드포인트에 사용자 지정 도메인을 사용하려면 먼저 Azure DNS 또는 해당하는 DNS 공급자를 통해 CNAME(Canonical Name) 레코드를 만들어서 CDN 엔드포인트를 가리켜야 합니다. 

CNAME 레코드는 원본 도메인을 대상 도메인 이름에 매핑하는 DNS 레코드입니다. 

Azure CDN의 경우 원본 도메인 이름은 사용자 지정 도메인 이름이고, 대상 도메인 이름은 CDN 엔드포인트 호스트 이름입니다. 

Azure CDN은 CNAME 레코드를 확인한 후 원본 사용자 지정 도메인으로 주소가 지정된 트래픽을 대상 CDN 엔드포인트 호스트 이름으로 라우팅합니다.

사용자 지정 도메인 및 해당 하위 도메인은 한 번에 하나의 단일 엔드포인트와 연결할 수 있습니다. 

여러 Azure 서비스에 동일한 사용자 지정 도메인의 다른 하위 도메인을 사용하려면 여러 CNAME 레코드를 사용합니다.

여러 하위 도메인을 포함한 사용자 지정 도메인을 동일한 CDN 엔드포인트에 매핑할 수 있습니다.

> [!NOTE]
> 이 자습서에서는 CNAME 레코드 종류를 사용합니다. A 또는 AAAA 레코드 종류를 사용하는 경우 CNAME을 선택한 레코드 종류로 바꾸면서 아래의 동일한 단계를 수행합니다.

---
# <a name="azure-dns"></a>[**Azure DNS**](#tab/azure-dns)

Azure DNS는 동일한 구독에 포함된 Azure 리소스에 별칭 레코드를 사용합니다.

Azure CDN 엔드포인트에 대한 별칭 레코드를 추가하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 메뉴에서 **모든 리소스** 를 선택한 다음, 사용자 지정 도메인에 대한 Azure DNS 영역을 선택합니다.

3. 사용자 지정 도메인에 대한 DNS 영역에서 **+ 레코드 세트** 를 선택합니다.

4. **레코드 세트 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Name  | CDN 엔드포인트에 사용할 별칭을 입력합니다. 예를 들면 **www** 로 설정해야 합니다. |
    | 유형  | **CNAME** 을 선택합니다. |
    | 별칭 레코드 집합 | **Yes** 를 선택합니다. |
    | 별칭 형식 | **Azure 리소스** 를 선택합니다. |
    | 구독을 선택합니다. | 구독을 선택합니다. |
    | Azure 리소스 | Azure CDN 엔드포인트를 선택합니다. |

5. 레코드의 **TTL** 을 해당 값으로 변경합니다.

6. **확인** 을 선택합니다.

# <a name="dns-provider"></a>[**DNS 공급자**](#tab/dns-provider)

## <a name="map-the-temporary-cdnverify-subdomain"></a>임시 cdnverify 하위 도메인 매핑

프로덕션 중인 기존 도메인을 매핑할 경우 특별한 고려 사항이 있습니다. 

Azure Portal에서 사용자 지정 도메인을 등록할 때 도메인에서 짧은 가동 중지 시간이 발생할 수 있습니다.

웹 트래픽이 중단되지 않도록 하려면 Azure **cdnverify** 하위 도메인이 있는 CDN 엔드포인트 호스트 이름에 사용자 지정 도메인을 매핑합니다. 이 프로세스는 임시 CNAME 매핑을 만듭니다. 

이 메서드를 사용하여 DNS 매핑이 발생하는 동안 사용자가 중단 없이 도메인에 액세스할 수 있습니다. 

프로덕션 가동 중지 시간을 고려할 필요가 없는 경우에는 사용자 지정 도메인을 CDN 엔드포인트에 직접 매핑해도 됩니다. [영구 사용자 지정 도메인 매핑](#map-the-permanent-custom-domain)을 계속 진행합니다.

cdnverify 하위 도메인에서 CNAME 레코드를 만들려면:

1. 사용자 지정 도메인에 대한 DNS 공급자의 웹 사이트에 로그인합니다.

2. 사용자 지정 도메인에 대한 CNAME 레코드 항목을 만들고 다음 표와 같이 필드에 입력합니다(필드 이름 다를 수 있음).

    | 원본                    | Type  | 대상                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify. www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - 원본: cdnverify 하위 도메인을 포함한 사용자 지정 도메인 이름을 **cdnverify.\<custom-domain-name>** 형식으로 입력합니다.
        - 예: **cdnverify. www.contoso.com**

    - 유형: **CNAME** 을 입력하거나 선택합니다.

    - 대상: cdnverify 하위 도메인을 포함한 CDN 엔드포인트 호스트 이름을 **cdnverify.\<endpoint-name>.azureedge.net** 형식으로 입력합니다. 
        - 예: **cdnverify.contoso.azureedge.net**

3. 변경 내용을 저장합니다.

## <a name="map-the-permanent-custom-domain"></a>영구 사용자 지정 도메인 매핑

이 섹션에서는 영구 사용자 지정 도메인을 CDN 엔드포인트에 매핑합니다. 

사용자 지정 도메인에 대한 CNAME 레코드를 만들려면:

1. 사용자 지정 도메인에 대한 도메인 공급자의 웹 사이트에 로그인합니다.

2. 사용자 지정 도메인에 대한 CNAME 레코드 항목을 만들고 다음 표와 같이 필드에 입력합니다(필드 이름 다를 수 있음).

    | 원본          | Type  | 대상           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - 원본: 사용자 지정 도메인 이름을 입력합니다.
        - 예: **www.contoso.com**

    - 유형: **CNAME** 을 입력하거나 선택합니다.

    - 대상: CDN 엔드포인트 호스트 이름을 **\<endpoint-name>.azureedge.net** 형식으로 입력합니다. 
        - 예: **contoso.azureedge.net**

3. 변경 내용을 저장합니다.

4. 임시 cdnverify 하위 도메인 CNAME 레코드를 이전에 만든 경우 삭제합니다. 

5. 프로덕션에서 이 사용자 지정 도메인을 처음으로 사용하는 경우 [CDN 엔드포인트와 사용자 지정 도메인 연결](#associate-the-custom-domain-with-your-cdn-endpoint) 및 [사용자 지정 도메인 확인](#verify-the-custom-domain)의 단계에 따릅니다.

---
## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>CDN 엔드포인트와 사용자 지정 도메인 연결

사용자 지정 도메인을 등록한 후에 CDN 엔드포인트에 추가할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 사용자 지정 도메인에 매핑하려는 엔드포인트를 포함하는 CDN 프로필로 이동합니다.
    
2. **CDN 프로필** 페이지에서 사용자 지정 도메인과 연결할 CDN 엔드포인트를 선택합니다.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-endpoint-selection.png" alt-text="CDN 엔드포인트 선택" border="true":::
    
3. **+ 사용자 지정 도메인** 을 선택합니다. 

   :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png" alt-text="사용자 지정 도메인 추가 단추" border="true":::

4. **사용자 지정 도메인 추가** 에서 **엔드포인트 호스트 이름** 은 미리 채워져 있으며 CDN 엔드포인트 URL **\<endpoint-hostname>** .azureedge.net에서 파생됩니다. 이는 변경할 수 없습니다.

5. **사용자 지정 호스트 이름** 의 경우 CNAME 레코드의 원본 도메인으로 사용하려면 하위 도메인을 포함하여 사용자 지정 도메인을 입력합니다. 
    1. 예: **www.contoso.com** 또는 **cdn.contoso.com** **cdnverify 하위 도메인 이름을 사용하지 마세요**.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png" alt-text="사용자 지정 도메인 추가" border="true":::

6. **추가** 를 선택합니다.

   Azure에서 입력한 사용자 지정 도메인 이름에 대한 CNAME 레코드가 있는지 확인합니다. CNAME이 올바르면 사용자 지정 도메인의 유효성이 검사됩니다. 

   새 사용자 지정 도메인 설정이 모든 CDN 에지 노드에 전파되려면 다소 시간이 걸릴 수 있습니다. 
    - **Microsoft의 Azure CDN 표준** 프로필의 경우 일반적으로 10분 이내에 전파가 완료됩니다. 
    - **Akamai의 Azure CDN Standard** 프로필의 경우, 일반적으로 1분 이내에 전파가 완료됩니다. 
    - **Verizon의 Azure CDN 표준** 및 **Verizon의 Azure CDN 프리미엄** 프로필의 경우 일반적으로 10분 이내에 전파가 완료됩니다.   


## <a name="verify-the-custom-domain"></a>사용자 지정 도메인 확인

사용자 지정 도메인 등록을 완료한 후에는 사용자 지정 도메인이 CDN 엔드포인트를 참조하는지 확인합니다.
 
1. 엔드포인트에 캐시된 공용 콘텐츠가 있는지 확인합니다. 예를 들어 CDN 엔드포인트가 스토리지 계정과 연결되어 있는 경우 Azure CDN은 공용 컨테이너에 콘텐츠를 캐시합니다. 사용자 지정 도메인을 테스트하려면 컨테이너가 공용 액세스를 허용하도록 설정되었으며 파일을 하나 이상 포함하고 있는지 확인합니다.

2. 브라우저에서 사용자 지정 도메인을 사용하는 파일의 주소로 이동합니다. 예를 들어, 사용자 지정 도메인이 `www.contoso.com`인 경우 캐시된 파일의 URL은 URL `http://www.contoso.com/my-public-container/my-file.jpg`와 유사하게 됩니다. 결과가 **\<endpoint-hostname>** .azureedge.net에서 CDN 엔드포인트에 직접 액세스할 때와 동일한지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 사용자 지정 도메인과 엔드포인트를 연결하지 않으려면 다음 단계를 수행하여 사용자 지정 도메인을 제거합니다.
 
1. CDN 프로필에서 제거하려는 사용자 지정 도메인을 포함하는 엔드포인트를 선택합니다.

2. **엔드포인트** 페이지의 사용자 지정 도메인에서 제거하려는 사용자 지정 도메인을 마우스 오른쪽 단추로 클릭한 다음, 팝업 메뉴에서 **삭제** 를 선택합니다. **Yes** 를 선택합니다.

   사용자 지정 도메인은 엔드포인트에서 분리됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> - CNAME DNS 레코드 만들기.
> - CDN 엔드포인트와 사용자 지정 도메인 연결.
> - 사용자 지정 도메인 확인.

Azure CDN 사용자 지정 도메인에서 HTTPS를 구성하는 방법을 알아보려면 다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [자습서: Azure CDN 사용자 지정 도메인에서 HTTPS 구성](cdn-custom-ssl.md)