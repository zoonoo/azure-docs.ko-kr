---
title: Azure 포털을 사용하여 HTTPS 리디렉션에 HTTP를 사용하여 정문 만들기
description: Azure 포털을 사용하여 HTTP에서 HTTPS로 리디렉션된 트래픽으로 정문 작성 방법을 알아봅니다.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: b7385ef27cd17705f2c86b6f57d4780511b6935c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246860"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Azure 포털을 사용하여 HTTPS 리디렉션에 HTTP를 사용하여 정문 만들기

Azure 포털을 사용하여 SSL 종료에 대한 인증서가 있는 [정문](front-door-overview.md) 만들기를 할 수 있습니다. 라우팅 규칙은 HTTP 트래픽을 HTTPS로 리디렉션하는 데 사용됩니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 기존 웹 앱 리소스로 정문 만들기
> * SSL 인증서가 있는 사용자 지정 도메인 추가 
> * 사용자 지정 도메인에서 HTTPS 리디렉션 설정

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>기존 웹 앱 리소스로 정문 만들기

1. 에서 [https://portal.azure.com](https://portal.azure.com)Azure 포털에 로그인합니다.
2. Azure Portal의 왼쪽 위 모서리에 있는 **리소스 만들기**를 클릭합니다.
3. 검색 막대를 사용하여 **정문** 찾기를 클릭하고 리소스 유형을 찾으면 **만들기를 클릭합니다.**
4. 구독을 선택한 다음 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. UI에서 묻는 위치는 리소스 그룹에만 적합합니다. 정문 구성은 [Azure 정문](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)POP 위치 모두에 배포됩니다.

    ![새 정문용 기본 구성](./media/front-door-url-redirect/front-door-create-basics.png)

5. **다음을** 클릭하여 구성 탭을 입력합니다. Front Door의 구성은 기본 프런트 엔드 호스트를 추가하고 백 엔드 풀에 백엔드를 추가한 다음 라우팅 규칙을 만들어 프런트 엔드 호스트의 라우팅 동작을 매핑하는 세 단계로 수행됩니다. 

     ![프론트 도어 구성 설계자](./media/front-door-url-redirect/front-door-designer.png)

6. **+** _프런트 엔드 호스트의_ '아이콘'을 클릭하여 프런트 엔드 호스트를 만들고 프런트 도어 ()에`\<**name**\>.azurefd.net`대한 기본 프런트 엔드 호스트에 대한 전역 고유 이름을 입력합니다. **추가를** 클릭하여 다음 단계로 진행합니다.

     ![프런트 엔드 호스트 추가](./media/front-door-url-redirect/front-door-create-fehost.png)

7. 백 엔드**+** 풀에서 '' _아이콘을_ 클릭하여 백 엔드 풀을 만듭니다. 백 엔드 풀의 이름을 제공한 다음 **'백 엔드 추가'를**클릭합니다.
8. 백 엔드 호스트 유형을 _앱 서비스로_선택합니다. 웹 앱이 호스팅되는 구독을 선택한 다음 **Backend 호스트 이름에**대한 드롭다운에서 특정 웹 앱을 선택합니다.
9. 백 엔드를 저장하려면 **추가를** 클릭하고 다시 **추가를** 클릭하여 백 엔드 풀 구성을 저장합니다.   ![백 엔드 풀에 백 엔드 추가](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. 라우팅**+** _규칙에서_ '' 아이콘을 클릭하여 경로를 만듭니다. 경로의 이름을 'HttpToHttpsRedirect'라고 지정한 다음 _허용된 프로토콜_ 필드를 **'HTTP 전용'으로**설정합니다. 적절한 _프런트 엔드 호스트가_ 선택되었는지 확인합니다.  
11. 경로 _세부 정보_ 섹션에서 _경로 유형을_ **리디렉션으로**설정하고 _리디렉션 유형이_ **Found(302)로** 설정되어 있는지 확인하고 _리디렉션 프로토콜이_ **HTTPS로만**설정되어 있는지 확인합니다. 
12. 추가를 클릭하여 HTTP의 라우팅 규칙을 HTTPS 리디렉션에 저장합니다.
     ![HTTPS 리디렉션 경로에 HTTP 추가](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. HTTPS 트래픽을 처리하기 위한 다른 라우팅 규칙을 추가합니다. **+** _'라우팅 규칙에_ 서명'을 클릭하고 경로의 이름을 지정하고 'DefaultForwardingRoute'라고 말한 다음 허용된 프로토콜 필드를 **'HTTPS 만'으로** _설정합니다._ 적절한 _프런트 엔드 호스트가_ 선택되었는지 확인합니다.
14. 경로 세부 정보 섹션에서 _경로 유형을_ **앞으로**설정하고 오른쪽 백 엔드 풀을 선택하고 _전달 프로토콜이_ **HTTPS로만**설정되어 있는지 확인합니다. 
15. 추가를 클릭하여 요청 전달을 위한 라우팅 규칙을 저장합니다.
     ![HTTPS 트래픽에 대한 전달 경로 추가](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. **검토 + 만들기를** 클릭한 다음 **만들기**를 클릭하여 정문 프로필을 만듭니다. 생성된 리소스로 이동합니다.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>정문에 사용자 지정 도메인을 추가하고 HTTPS를 사용하도록 설정합니다.
다음 단계에서는 기존 Front Door 리소스에 사용자 지정 도메인을 추가한 다음 HTTP에서 HTTPS 리디렉션을 사용하도록 설정하는 방법을 보여 주실 수 있습니다. 

### <a name="add-a-custom-domain"></a>사용자 지정 도메인 추가

이 예제에서는 하위 도메인(예: `www` `www.contosonews.com`)에 대한 CNAME 레코드를 추가합니다.

#### <a name="create-the-cname-record"></a>CNAME 레코드 만들기

CNAME 레코드를 추가하여 정문의 기본 프런트 엔드 호스트에`<name>.azurefd.net`하위 `<name>` 도메인을 매핑합니다(정문 프로필의 이름은 위치).

도메인의 `www.contoso.com` 경우 예를 들어 에 이름을 `www` 매핑하는 CNAME `<name>.azurefd.net`레코드를 추가합니다.

CNAME을 추가하면 DNS 레코드 페이지가 다음 예제와 비슷합니다.

![정문으로 가는 CNAME 사용자 지정 도메인](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>정문에서 사용자 지정 도메인 온보딩

1. 정문 디자이너 탭에서 프런트 엔드 호스트 섹션에서 '+' 아이콘을 클릭하여 새 맞춤 도메인을 추가합니다. 
2. 사용자 지정 호스트 이름 필드에 정규화된 사용자 지정 `www.contosonews.com`DNS 이름을 입력합니다. 
3. 도메인에서 정문으로의 CNAME 매핑의 유효성이 검사되면 **추가를** 클릭하여 사용자 지정 도메인을 추가합니다.
4. 변경 내용을 제출하려면 **저장을** 클릭합니다.

![사용자 지정 도메인 메뉴](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>사용자 지정 도메인에서 HTTPS 사용

1. 추가된 사용자 지정 도메인을 클릭하고 섹션 에서 **사용자 지정 도메인 HTTPS에서**상태를 **사용 으로**변경합니다.
2. 인증서 관리 **유형은** 정문에서 유지 관리, 관리 및 자동 회전하는 무료 인증서에 대해 _관리되는 프런트 도어로_ 설정된 상태로 둘 수 있습니다. Azure Key Vault에 저장된 사용자 지정 SSL 인증서를 사용하도록 선택할 수도 있습니다. 이 자습서는 Front Door 관리 인증서를 사용 한다고 가정 합니다.
![사용자 지정 도메인에 대한 HTTPS 사용](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. **업데이트를** 클릭하여 선택 영역을 저장한 다음 **저장을**클릭합니다.
4. 몇 분 후에 **새로 고침을** 클릭한 다음 사용자 지정 도메인을 다시 클릭하여 인증서 프로비저닝의 진행 상황을 확인합니다. 

> [!WARNING]
> 사용자 지정 도메인에 대한 HTTPS를 사용하도록 설정하는 데 몇 분 정도 걸릴 수 있으며 CNAME이 Front Door 호스트에 `<name>.azurefd.net`직접 매핑되지 않은 경우 도메인 소유권 유효성 검사에 따라 달라집니다. [사용자 지정 도메인에 대해 HTTPS를 사용하도록 설정하는 방법에 대해](./front-door-custom-domain-https.md)자세히 알아봅니다.

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>사용자 지정 도메인에 대한 라우팅 규칙 구성

1. 이전에 만든 리디렉션 라우팅 규칙을 클릭합니다.
2. Frontend 호스트에 대한 드롭다운을 클릭하고 사용자 지정 도메인을 선택하여 도메인에도 이 경로를 적용합니다.
3. **업데이트**를 클릭합니다.
4. 다른 라우팅 규칙에 대해서도 동일한 작업을 수행하여 전달 경로가 사용자 지정 도메인을 추가합니다.
5. 변경 내용을 제출하려면 **저장을** 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
- URL 리디렉션에 대해 자세히 알아보려면 [정문 에서.](front-door-url-redirect.md)
