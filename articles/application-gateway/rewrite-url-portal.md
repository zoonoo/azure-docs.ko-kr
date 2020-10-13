---
title: Azure 애플리케이션 게이트웨이로 URL 및 쿼리 문자열 재작성-Azure Portal
description: Azure Portal를 사용 하 여 URL 및 쿼리 문자열을 다시 작성 하도록 Azure 애플리케이션 게이트웨이를 구성 하는 방법을 알아봅니다.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 7/16/2020
ms.author: surmb
ms.openlocfilehash: 160d056447bd53ea01437acd372b5efeb15b4773
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083160"
---
# <a name="rewrite-url-with-azure-application-gateway---azure-portal-preview"></a>Azure 애플리케이션 게이트웨이로 URL 재작성-Azure Portal (미리 보기)

이 문서에서는 Azure Portal를 사용 하 여 [Application Gateway V2 SKU](application-gateway-autoscaling-zone-redundant.md) 인스턴스를 구성 하 여 URL을 다시 작성 하는 방법을 설명 합니다.

>[!NOTE]
> URL 다시 쓰기 기능은 미리 보기 상태 이며 Application Gateway의 Standard_v2 및 WAF_v2 SKU에 대해서만 사용할 수 있습니다. 프로덕션 환경에서는 사용 하지 않는 것이 좋습니다. 미리 보기에 대 한 자세한 내용은 [여기의 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료 하려면 Application Gateway v2 SKU 인스턴스가 있어야 합니다. URL 다시 쓰기는 v1 SKU에서 지원 되지 않습니다. V2 SKU가 없는 경우 시작 하기 전에 [Application Gateway V2 sku](tutorial-autoscale-ps.md) 인스턴스를 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com/) 에 로그인합니다.

## <a name="configure-url-rewrite"></a>URL 다시 쓰기 구성

아래 예제에서 요청 URL에 */아티클이*포함 될 때마다 url 경로 및 url 쿼리 문자열이 다시 작성 됩니다.

`contoso.com/article/123/fabrikam` -> `contoso.com/article.aspx?id=123&title=fabrikam`

1. **모든 리소스**를 선택 하 고 응용 프로그램 게이트웨이를 선택 합니다.

2. 왼쪽 창에서 다시 **쓰기** 를 선택 합니다.

3. **재작성 집합**선택:

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-1.png" alt-text="재작성 집합 추가":::

4. 재작성 집합의 이름을 제공 하 고 라우팅 규칙에 연결 합니다.

    a. **이름** 상자에 재작성 집합의 이름을 입력 합니다.
    
    b. **연결 된 라우팅 규칙** 목록에 나열 된 규칙을 하나 이상 선택 합니다. 이는 라우팅 규칙을 통해 재작성 구성을 원본 수신기에 연결 하는 데 사용 됩니다. 다른 재작성 집합과 연결 되지 않은 라우팅 규칙만 선택할 수 있습니다. 다른 재작성 집합과 이미 연결 된 규칙은 회색으로 표시 됩니다.
    
    c. **다음**을 선택합니다.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-2.png" alt-text="재작성 집합 추가":::

5. 재작성 규칙을 만듭니다.

    a. **재작성 규칙 추가**를 선택 합니다.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-3.png" alt-text="재작성 집합 추가":::
    
    b. 재작성 규칙 **이름** 상자에 재작성 규칙의 이름을 입력 합니다. **규칙 시퀀스** 상자에 숫자를 입력 합니다.

6. 이 예제에서는 경로에 */아티클이*포함 된 경우에만 url 경로 및 url 쿼리 문자열을 다시 작성 합니다. 이렇게 하려면 URL 경로에 */아티클이* 포함 되는지 여부를 평가 하는 조건을 추가 합니다.

    a. **조건 추가** 를 선택한 다음, **If** 명령이 포함 된 상자를 선택 하 여 확장 합니다.
    
    b. 이 예제에서는 URL 경로에서 pattern */article* 을 확인 하 고 **확인할 변수 형식** 목록에서 **서버 변수**를 선택 합니다.
    
    다. **서버 변수** 목록에서 uri_path을 선택 합니다.
    
    d. **대/소문자 구분**에서 **아니요**를 선택 합니다.
    
    e. **연산자** 목록에서 **같음 (=)** 을 선택 합니다.
    
    f. 정규식 패턴을 입력 합니다. 이 예제에서는 다음 패턴을 사용 합니다. `.*article/(.*)/(.*)`
    
      ()는 나중에 URL 경로를 다시 작성 하기 위한 식을 작성할 때 사용 하는 부분 문자열을 캡처하는 데 사용 됩니다. 자세한 내용은 [여기](rewrite-http-headers-url.md#capturing)를 참조하세요.

    g. **확인**을 선택합니다.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-4.png" alt-text="재작성 집합 추가":::

 

7. URL 및 URL 경로를 다시 작성 하는 작업 추가

   a. **재작성 유형** 목록에서 **URL**을 선택 합니다.

   b. **작업 유형** 목록에서 **설정**을 선택 합니다.

   다. **구성 요소**에서 **URL 경로 및 url 쿼리 문자열을 모두** 선택 합니다.

   d. **URL 경로 값**에 경로에 대 한 새 값을 입력 합니다. 이 예제에서는 **/article.aspx** 를 사용 합니다. 

   e. **Url 쿼리 문자열 값**에 url 쿼리 문자열의 새 값을 입력 합니다. 이 예에서는 **id = {var_uri_path_1} &title = {var_uri_path_2}** 을 사용 합니다.
    
    `{var_uri_path_1}` 및 `{var_uri_path_1}` 는이 식의 조건을 평가 하는 동안 캡처된 부분 문자열을 가져오는 데 사용 됩니다. `.*article/(.*)/(.*)`
    
   f. **확인**을 선택합니다.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-5.png" alt-text="재작성 집합 추가":::

8. **만들기** 를 클릭 하 여 재작성 집합을 만듭니다.

9. 새 재작성 집합이 재작성 집합 목록에 나타나는지 확인 합니다.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-6.png" alt-text="재작성 집합 추가":::

## <a name="verify-url-rewrite-through-access-logs"></a>액세스 로그를 통해 URL 재작성 확인

액세스 로그에서 아래 필드를 관찰 하 여 예상 대로 URL 재작성이 발생 했는지 확인 합니다.

* **Originalrequesturiwithargs**:이 필드에는 원래 요청 URL이 포함 됩니다.
* **requestUri**:이 필드에는의 재작성 작업 후 URL이 포함 Application Gateway

액세스 로그의 모든 필드에 대 한 자세한 내용은 [여기](application-gateway-diagnostics.md#for-application-gateway-and-waf-v2-sku)를 참조 하세요.

##  <a name="next-steps"></a>다음 단계

몇 가지 일반적인 사용 사례에 대 한 다시 쓰기를 설정 하는 방법에 대 한 자세한 내용은 [일반적인 재작성 시나리오](rewrite-http-headers.md)를 참조 하십시오.
