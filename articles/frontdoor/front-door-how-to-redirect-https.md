---
title: Azure Portal를 사용 하 여 HTTP에서 HTTPS로 리디렉션하는 프런트 도어 만들기
description: Azure Portal를 사용 하 여 HTTP에서 HTTPS로 리디렉션된 트래픽으로 프런트 도어를 만드는 방법에 대해 알아봅니다.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 5/21/2019
ms.author: duau
ms.openlocfilehash: fe2159f0eeb9d01081e6a25e7a88ceff4f1e361c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399693"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Azure Portal를 사용 하 여 HTTP에서 HTTPS로 리디렉션하는 프런트 도어 만들기

Azure Portal를 사용 하 여 TLS 종료를 위한 인증서를 사용 하 여 [Front 도어](front-door-overview.md) 를 만들 수 있습니다. 라우팅 규칙은 HTTP 트래픽을 HTTPS로 리디렉션하는 데 사용 됩니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 기존 웹 앱 리소스를 사용 하 여 Front 도어 만들기
> * TLS/SSL 인증서를 사용 하 여 사용자 지정 도메인 추가 
> * 사용자 지정 도메인에서 HTTPS 리디렉션 설정

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>기존 웹 앱 리소스를 사용 하 여 Front 도어 만들기

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
2. Azure Portal의 왼쪽 위 모서리에 있는 **리소스 만들기**를 클릭합니다.
3. 검색 창을 사용 하 여 **전방 도어** 를 검색 하 고 리소스 종류를 찾았으면 **만들기**를 클릭 합니다.
4. 구독을 선택한 다음 기존 리소스 그룹을 사용 하거나 새 리소스 그룹을 만듭니다. UI에서 요청 된 위치는 리소스 그룹에만 해당 됩니다. 프런트 도어 구성은 모든 [Azure 전면 도어의 POP 위치](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)에 배포 됩니다.

    ![새 Front 문에 대 한 기본 사항 구성](./media/front-door-url-redirect/front-door-create-basics.png)

5. **다음** 을 클릭 하 여 구성 탭을 입력 합니다. 프런트 도어 구성은 기본 프런트 엔드 호스트를 추가 하 고 백 엔드 풀에 백 엔드를 추가한 다음, 프런트 엔드 호스트의 라우팅 동작을 매핑하는 라우팅 규칙을 만드는 세 단계로 이루어집니다. 

     ![전면 도어 구성 디자이너](./media/front-door-url-redirect/front-door-designer.png)

6. 프런트 엔드 호스트 **+** 에서 ' ' 아이콘 _Frontend hosts_ 을 클릭 하 여 프런트 도어 ()의 기본 프런트 엔드 호스트에 대해 전역적으로 고유한 이름을 입력 `\<**name**\>.azurefd.net` 합니다. **추가** 를 클릭 하 여 다음 단계로 이동 합니다.

     ![프런트 엔드 호스트 추가](./media/front-door-url-redirect/front-door-create-fehost.png)

7. 백 엔드 풀 **+** 에서 ' ' 아이콘 _Backend pools_ 을 클릭 하 여 백 엔드 풀을 만듭니다. 백 엔드 풀의 이름을 입력 한 다음 '**백 엔드 추가**'를 클릭 합니다.
8. 백 엔드 호스트 유형을 _App service_로 선택 합니다. 웹 앱이 호스트 되는 구독을 선택한 다음 **백 엔드 호스트 이름**드롭다운에서 특정 웹 앱을 선택 합니다.
9. **추가** 를 클릭 하 여 백 엔드를 저장 하 고 **추가** 를 다시 클릭 하 여 백 엔드 풀 구성을 저장 합니다.   ![백 엔드 풀에 백 엔드 추가](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. **+** _라우팅 규칙_ 에서 ' ' 아이콘을 클릭 하 여 경로를 만듭니다. 경로 이름 (예를 들어, ' HttpToHttpsRedirect ')을 입력 한 다음 _허용 된 프로토콜_ 필드를 **' HTTP l o n '** 으로 설정 합니다. 적절 한 _프런트 엔드 호스트가_ 선택 되어 있는지 확인 합니다.  
11. _경로 세부 정보_ 섹션에서 _경로 유형을_ **리디렉션**으로 설정 하 고, 리디렉션 _유형이_ **Found (302)** 로 설정 되 고, _리디렉션 프로토콜이_ **HTTPS로만**설정 되어 있는지 확인 합니다. 
12. 추가를 클릭 하 여 HTTP에 대 한 라우팅 규칙을 HTTPS 리디렉션에 저장 합니다.
     ![HTTPS 리디렉션 경로에 HTTP 추가](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. HTTPS 트래픽을 처리 하는 또 다른 라우팅 규칙을 추가 합니다. **+** _라우팅 규칙_ 에 대 한 ' ' 기호를 클릭 하 고 경로 이름 (예를 들어, ' DefaultForwardingRoute ')을 지정한 다음 _허용 된 프로토콜_ 필드를 **' HTTPS 전용 '** 으로 설정 합니다. 적절 한 _프런트 엔드 호스트가_ 선택 되어 있는지 확인 합니다.
14. 경로 정보 섹션에서 _경로 유형_ 을 **전달**로 설정 하 고, 올바른 백 엔드 풀이 선택 되어 있고 _전달 프로토콜이_ **HTTPS로만**설정 되어 있는지 확인 합니다. 
15. 추가를 클릭 하 여 요청 전달에 대 한 라우팅 규칙을 저장 합니다.
     ![HTTPS 트래픽에 대 한 전달 경로 추가](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. **검토 + 만들기** , **만들기**를 차례로 클릭 하 여 Front 도어 프로필을 만듭니다. 만든 후 리소스로 이동 합니다.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>사용자 지정 도메인을 Front 문에 추가 하 고 HTTPS를 사용 하도록 설정
다음 단계에서는 기존 Front 도어 리소스에 사용자 지정 도메인을 추가한 다음 HTTP에서 HTTPS로 리디렉션할 수 있도록 설정 하는 방법을 보여 줍니다. 

### <a name="add-a-custom-domain"></a>사용자 지정 도메인 추가

이 예에서는 하위 도메인에 대 한 CNAME 레코드를 추가 `www` 합니다 (예: `www.contosonews.com` ).

#### <a name="create-the-cname-record"></a>CNAME 레코드 만들기

CNAME 레코드를 추가 하 여 프런트 도어의 기본 프런트 엔드 호스트 ( `<name>.azurefd.net` , 여기서 `<name>` 은 전방 도어 프로필의 이름)에 하위 도메인을 매핑합니다.

예를 들어 도메인의 경우 `www.contoso.com` 이름을에 매핑하는 CNAME 레코드를 추가 `www` `<name>.azurefd.net` 합니다.

CNAME을 추가하면 DNS 레코드 페이지가 다음 예제와 비슷합니다.

![CNAME 사용자 지정 도메인에서 전방 도어](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Front 문에 사용자 지정 도메인 등록

1. 프런트 도어 디자이너 탭의 프런트 엔드 호스트 섹션에서 ' + ' 아이콘을 클릭 하 여 새 사용자 지정 도메인을 추가 합니다. 
2. 사용자 지정 호스트 이름 필드에 정규화 된 사용자 지정 DNS 이름을 입력 합니다 (예:) `www.contosonews.com` . 
3. 도메인에서 프런트 도어로의 CNAME 매핑을 확인 한 후 **추가** 를 클릭 하 여 사용자 지정 도메인을 추가 합니다.
4. **저장** 을 클릭 하 여 변경 내용을 제출 합니다.

![사용자 지정 도메인 메뉴](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>사용자 지정 도메인에서 HTTPS를 사용 하도록 설정

1. 추가 된 사용자 지정 도메인을 클릭 하 고 **사용자 지정 도메인 HTTPS**섹션에서 상태를 **사용**으로 변경 합니다.
2. 전면 도어에서 유지 관리, 관리 및 autorotated 하는 무료 인증서의 경우 _전면 도어_ 로 설정 된 **인증서 관리 유형을** 그대로 둘 수 있습니다. Azure Key Vault와 함께 저장 된 사용자 지정 TLS/SSL 인증서를 사용 하도록 선택할 수도 있습니다. 이 자습서에서는 전면 도어 관리 인증서를 사용 한다고 가정 합니다.
![사용자 지정 도메인에 대해 HTTPS 사용](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. **업데이트** 를 클릭 하 여 선택 내용을 저장 한 다음 **저장**을 클릭 합니다.
4. 몇 분 후에 **새로 고침** 을 클릭 한 다음 사용자 지정 도메인을 다시 클릭 하 여 인증서 프로 비전의 진행 상황을 확인 합니다. 

> [!WARNING]
> 사용자 지정 도메인에 대해 HTTPS를 사용 하도록 설정 하는 데는 몇 분 정도 걸릴 수 있으며 CNAME이 프런트 도어 호스트에 직접 매핑되지 않는 경우 도메인 소유권 유효성 검사에 따라서도 달라 집니다 `<name>.azurefd.net` . [사용자 지정 도메인에 대해 HTTPS를 사용 하도록 설정 하는 방법](./front-door-custom-domain-https.md)에 대해 자세히 알아보세요.

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>사용자 지정 도메인에 대 한 라우팅 규칙 구성

1. 앞에서 만든 리디렉션 라우팅 규칙을 클릭 합니다.
2. 프런트 엔드 호스트에 대 한 드롭다운을 클릭 하 고 사용자 지정 도메인을 선택 하 여이 경로를 도메인에도 적용 합니다.
3. **업데이트**를 클릭합니다.
4. 다른 라우팅 규칙에 대해서도 동일한 작업을 수행 합니다. 즉, 전달 경로에서 사용자 지정 도메인을 추가 합니다.
5. **저장** 을 클릭 하 여 변경 내용을 제출 합니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
- [전면 도어에서 URL 리디렉션](front-door-url-redirect.md)에 대해 자세히 알아보세요.
