---
title: Azure Application Gateway로 URL 및 쿼리 문자열 다시 쓰기 - Azure Portal
description: Azure Portal을 사용하여 URL 및 쿼리 문자열을 다시 쓰도록 Azure Application Gateway를 구성하는 방법을 알아봅니다.
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: how-to
ms.date: 4/05/2021
ms.author: azhussai
ms.openlocfilehash: c0a99d2c2885de59ccc903b9f505b8049d922b43
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108205200"
---
# <a name="rewrite-url-with-azure-application-gateway---azure-portal"></a>Azure Application Gateway로 URL 다시 쓰기 - Azure Portal

이 문서에서는 Azure Portal을 사용하여 URL을 다시 쓰도록 [Application Gateway v2 SKU](application-gateway-autoscaling-zone-redundant.md) 인스턴스를 구성하는 방법을 설명합니다.

>[!NOTE]
> URL 다시 쓰기 기능은 Application Gateway Standard_v2 및 WAF_v2 SKU에만 사용할 수 있습니다. WAF를 사용하는 게이트웨이에 URL 다시 쓰기 기능이 구성된 경우, 다시 쓴 요청 헤더 및 URL에 대해 WAF 평가가 수행됩니다. [자세한 정보를 알아보세요](rewrite-http-headers-url.md#using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku).

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 Application Gateway v2 SKU 인스턴스가 있어야 합니다. URL 다시 쓰기는 v1 SKU에서 지원되지 않습니다. v2 SKU가 없는 경우 시작하기 전에 [Application Gateway v2 SKU](tutorial-autoscale-ps.md) 인스턴스를 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com/) 에 로그인합니다.

## <a name="configure-url-rewrite"></a>URL 다시 쓰기 구성

아래 예제에서는 요청 URL에 */article* 이 포함될 때마다 URL 경로 및 URL 쿼리 문자열이 다시 작성됩니다.

`contoso.com/article/123/fabrikam` -> `contoso.com/article.aspx?id=123&title=fabrikam`

1. **모든 리소스** 를 선택한 후 애플리케이션 게이트웨이를 선택합니다.

2. 왼쪽 창에서 **다시 쓰기** 를 선택합니다.

3. **다시 쓰기 집합** 을 선택합니다.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-1.png" alt-text="다시 쓰기 집합 추가":::

4. 다시 쓰기 집합의 이름을 제공하고 라우팅 규칙과 연결합니다.

    a. **이름** 상자에 다시 쓰기 집합의 이름을 입력합니다.
    
    b. **연결된 라우팅 규칙** 목록에 나열된 규칙을 하나 이상 선택합니다. 이는 라우팅 규칙을 통해 다시 쓰기 구성을 원본 수신기에 연결하는 데 사용됩니다. 다른 다시 쓰기 집합과 연결되지 않은 라우팅 규칙만 선택할 수 있습니다. 다른 다시 쓰기 집합과 이미 연결된 규칙은 회색으로 표시됩니다.
    
    c. **다음** 을 선택합니다.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-2.png" alt-text="규칙에 연결":::

5. 다시 쓰기 규칙을 만듭니다.

    a. **다시 쓰기 규칙 추가** 를 선택합니다.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-3.png" alt-text="다시 쓰기 규칙 추가를 강조 표시하는 스크린샷.":::
    
    b. **다시 쓰기 규칙 이름** 상자에 다시 쓰기 규칙의 이름을 입력합니다. **규칙 시퀀스** 상자에 숫자를 입력합니다.

6. 이 예제에서는 경로에 */article* 이 포함된 경우에만 URL 경로 및 URL 쿼리 문자열을 다시 씁니다. 이렇게 하려면 URL 경로에 */article* 이 포함되었는지 여부를 평가하는 조건을 추가합니다.

    a. **조건 추가** 를 선택한 다음, **If** 명령이 포함된 상자를 선택하여 펼칩니다.
    
    b. 이 예제에서는 URL 경로에서 */article* 패턴을 확인하고 **확인할 변수 형식** 목록에서 **서버 변수** 를 선택하려고 합니다.
    
    다. **서버 변수** 목록에서 uri_path를 선택합니다.
    
    d. **대/소문자 구분** 에서 **아니요** 를 선택합니다.
    
    e. **연산자** 목록에서 **equal (=)** 을 선택합니다.
    
    f. 정규식 패턴을 입력합니다. 이 예제에서는 `.*article/(.*)/(.*)` 패턴을 사용합니다.
    
      ( )는 나중에 URL 경로를 다시 쓰기 위한 식을 작성할 때 사용할 부분 문자열을 캡처하는 데 사용됩니다. 자세한 내용은 [여기](rewrite-http-headers-url.md#capturing)를 참조하세요.

    g. **확인** 을 선택합니다.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-4.png" alt-text="Condition":::

 

7. URL 및 URL 경로를 다시 쓰는 작업 추가

   a. **다시 쓰기 유형** 목록에서 **URL** 을 선택합니다.

   b. **작업 형식** 목록에서 **설정** 을 선택합니다.

   다. **구성 요소** 에서 **URL 경로 및 URL 쿼리 문자열 모두** 를 선택합니다.

   d. **URL 경로 값** 에 경로의 새 값을 입력합니다. 이 예제에서는 **/article.aspx** 를 사용합니다. 

   e. **URL 쿼리 문자열 값** 에 URL 쿼리 문자열의 새 값을 입력합니다. 이 예제에서는 **id={var_uri_path_1}&title={var_uri_path_2}** 를 사용합니다.
    
    `{var_uri_path_1}` 및 `{var_uri_path_1}`은 이 `.*article/(.*)/(.*)` 식의 조건을 평가하는 동안 캡처된 부분 문자열을 가져오는 데 사용됩니다.
    
   f. **확인** 을 선택합니다.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-5.png" alt-text="동작":::

8. **만들기** 를 클릭하여 다시 쓰기 집합을 만듭니다.

9. 새 다시 쓰기 집합이 다시 쓰기 집합 목록에 표시되는지 확인합니다.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-6.png" alt-text="다시 쓰기 규칙 추가":::

## <a name="verify-url-rewrite-through-access-logs"></a>액세스 로그를 통해 URL 다시 쓰기 확인

액세스 로그에서 아래 필드를 관찰하여 URL 다시 쓰기가 예상대로 발생했는지 확인합니다.

* **originalRequestUriWithArgs**: 이 필드에는 원래 요청 URL이 포함됩니다.
* **requestUri**: 이 필드에는 Application Gateway에 다시 쓰기 작업 후 URL이 포함됩니다

액세스 로그의 모든 필드에 대한 자세한 내용은 [여기](application-gateway-diagnostics.md#for-application-gateway-and-waf-v2-sku)를 참조하세요.

##  <a name="next-steps"></a>다음 단계

몇 가지 일반적인 사용 사례에서 다시 쓰기를 설정하는 방법에 관한 자세한 내용은 [일반적인 다시 쓰기 시나리오](./rewrite-http-headers-url.md)를 참조하세요.