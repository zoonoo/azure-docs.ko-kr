---
title: 트래픽 관리자로 DNS 이름 구성
description: 로드 균형 조정을 위해 트래픽 관리자와 통합되는 Azure 앱 서비스 앱에 대한 사용자 지정 도메인을 구성하는 방법에 대해 알아봅니다.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: f8322c12669e41fc7c9aa88e99f95cf1b26ea87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944094"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>트래픽 관리자 통합을 사용하여 Azure 앱 서비스에서 사용자 지정 도메인 이름 구성

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> Cloud Services의 경우 [Azure 클라우드 서비스에 대한 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name.md)을 참조하세요.

[Azure 트래픽 관리자를](/azure/traffic-manager/) 사용하여 Azure [앱 서비스에](overview.md)트래픽 을 로드하는 경우 ** \<트래픽 관리자-엔드포인트>.trafficmanager.net을**사용하여 앱 서비스 앱에 액세스할 수 있습니다. 사용자에게 더 인식 할 수있는 도메인\.이름을 제공하기 위해 앱 서비스 앱과 함께 www contoso.com 같은 사용자 지정 도메인 이름을 할당 할 수 있습니다.

이 문서에서는 [트래픽 관리자와](../traffic-manager/traffic-manager-overview.md)통합된 앱 서비스 앱으로 사용자 지정 도메인 이름을 구성하는 방법을 보여 주며 있습니다.

> [!NOTE]
> 트래픽 관리자 끝점을 사용하여 도메인 이름을 구성할 때 [CNAME](https://en.wikipedia.org/wiki/CNAME_record) 레코드만 지원됩니다. 레코드가 지원되지 않으므로 contoso.com 같은 루트 도메인 매핑도 지원되지 않습니다.
> 

## <a name="prepare-the-app"></a>앱 준비

사용자 지정 DNS 이름을 Azure 트래픽 관리자와 통합된 앱에 매핑하려면 웹 앱의 [앱 서비스 계획이](https://azure.microsoft.com/pricing/details/app-service/) **표준** 계층 이상이어야 합니다. 이 단계에서는 App Service 앱이 지원되는 가격 책정 계층에 속하는지 확인해야 합니다.

### <a name="check-the-pricing-tier"></a>가격 책정 계층 확인

Azure [포털에서](https://portal.azure.com) **앱 서비스를**검색하고 선택합니다.

**App Service** 페이지에서 Azure 앱의 이름을 선택합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/select-app.png)

앱 페이지의 왼쪽 탐색에서 **확장(앱 서비스 계획)을**선택합니다.

![강화 메뉴](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

앱의 현재 계층은 파란색 테두리로 강조 표시됩니다. 앱이 **표준** 계층 이상인지(프로덕션 또는 **격리** 된 **Production** 범주의 모든 계층)에 있는지 확인합니다. 그렇다면 **확장** 페이지를 닫고 [CNAME 매핑 만들기를](#create-the-cname-mapping)건너뜁니다.

![가격 책정 계층 확인](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>강화 - App Service 계획

앱을 확장해야 하는 경우 **프로덕션** 범주에서 가격 책정 계층중 어느 것을 선택하세요. 추가 옵션을 보려면 **추가 옵션 보기**를 클릭합니다.

**적용**을 클릭합니다.

## <a name="create-traffic-manager-endpoint"></a>트래픽 관리자 끝점 만들기

[끝점 추가 또는 삭제의](../traffic-manager/traffic-manager-endpoints.md)단계에 따라 앱 서비스 앱을 트래픽 관리자 프로필의 끝점으로 추가합니다.

앱 서비스 앱이 지원되는 가격 책정 계층에 있으면 끝점을 추가할 때 사용 가능한 앱 서비스 대상 목록에 표시됩니다. 앱이 목록에 없는 경우 [앱의 가격 책정 계층을 확인합니다.](#prepare-the-app)

## <a name="create-the-cname-mapping"></a>CNAME 매핑 만들기
> [!NOTE]
> [구입한 앱 서비스 도메인을](manage-custom-dns-buy-domain.md)구성하려면 이 섹션을 건너뛰고 사용자 지정 도메인 [사용으로](#enable-custom-domain)이동합니다.
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

각 도메인 공급자의 세부 사항은 다양하지만 사용자 지정 도메인 이름(예: **contoso.com)에서**앱과 통합된 트래픽 관리자 도메인 *from* 이름(contoso.trafficmanager.net)으로 매핑됩니다. *to* **contoso.trafficmanager.net**

> [!NOTE]
> 레코드를 이미 사용 중이고 앱을 우선적으로 바인딩해야 하는 경우 추가 CNAME 레코드를 만들 수 있습니다. 예를 들어 **www\.contoso.com** 앱에 선제적으로 바인딩하려면 **awverify.www에서** **contoso.trafficmanager.net**CNAME 레코드를 만듭니다. 그런 다음 "www" CNAME 레코드를 변경할 필요 없이 앱에 "www\.contoso.com"를 추가할 수 있습니다. 자세한 내용은 [활성 DNS 이름을 Azure 앱 서비스](manage-custom-dns-migrate-domain.md)로 마이그레이션을 참조하세요.

도메인 공급자에서 DNS 레코드를 추가하거나 수정했다면 변경 내용을 저장합니다.

## <a name="enable-custom-domain"></a>사용자 지정 도메인 사용
도메인 이름에 대한 레코드가 전파된 후 브라우저를 사용하여 사용자 지정 도메인 이름이 앱 서비스 앱으로 확인되는지 확인합니다.

> [!NOTE]
> CNAME이 DNS 시스템을 통해 전파되는 데 약간의 시간이 걸릴 수 있습니다. CNAME을 사용할 수 <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> 있는지 확인하는 등의 서비스를 사용할 수 있습니다.
> 
> 

1. 도메인 확인이 성공하면 [Azure Portal의](https://portal.azure.com) 앱 페이지로 돌아갑니다.
2. 왼쪽 탐색에서 사용자 지정 도메인**호스트 이름 추가를** **선택합니다.** > 
4. 이전에 매핑한 사용자 지정 도메인 이름을 입력하고 **유효성 검사를**선택합니다.
5. **호스트 이름 레코드 유형이** **CNAME(www\.example.com 또는 하위 도메인)으로**설정되어 있는지 확인합니다.

6. 앱 서비스 앱이 이제 트래픽 관리자 끝점과 통합되었으므로 **CNAME 구성에서**트래픽 관리자 도메인 이름이 표시됩니다. 을 선택하고 **사용자 지정 도메인 추가를**클릭합니다.

    ![앱에 DNS 이름 추가](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure App Service에서 SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)
