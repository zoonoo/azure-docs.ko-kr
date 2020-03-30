---
title: 포털에서 HTTP 요청 및 응답 헤더 다시 작성 - Azure 응용 프로그램 게이트웨이
description: Azure 포털을 사용하여 Azure 응용 프로그램 게이트웨이를 구성하여 게이트웨이를 통과하는 요청 및 응답에서 HTTP 헤더를 다시 작성하는 방법을 알아봅니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: b90736b3ed1c1f69488fde4a386cf215d751c362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74012852"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Azure 응용 프로그램 게이트웨이 - Azure 포털을 사용 하 고 HTTP 요청 및 응답 헤더 다시 작성

이 문서에서는 Azure 포털을 사용하여 [응용 프로그램 게이트웨이 v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) 인스턴스를 구성하여 요청 및 응답에서 HTTP 헤더를 다시 작성하는 방법을 설명합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 응용 프로그램 게이트웨이 v2 SKU 인스턴스가 있어야 합니다. v1 SKU에서는 헤더 를 다시 작성할 수 없습니다. v2 SKU가 없는 경우 시작하기 전에 [응용 프로그램 게이트웨이 v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) 인스턴스를 만듭니다.

## <a name="create-required-objects"></a>필수 개체 만들기

HTTP 헤더 재작성을 구성하려면 다음 단계를 완료해야 합니다.

1. HTTP 헤더 다시 작성하는 데 필요한 개체를 만듭니다.

   - **작업 다시 쓰기**: 다시 작성하려는 요청 및 요청 헤더 필드와 헤더에 대한 새 값을 지정하는 데 사용됩니다. 하나 이상의 재쓰기 조건을 다시 쓰기 작업과 연결할 수 있습니다.

   - **다시 쓰기 조건**: 선택적 구성입니다. 다시 작성 조건은 HTTP(S) 요청 및 응답의 내용을 평가합니다. HTTP(S) 요청 또는 응답이 재작성 조건과 일치하는 경우 다시 쓰기 작업이 수행됩니다.

     둘 이상의 조건을 작업과 연결하는 경우 모든 조건이 충족될 때만 작업이 수행됩니다. 즉, 작업은 논리적 AND 작업입니다.

   - **다시 쓰기 규칙**: 여러 재쓰기 작업 / 다시 쓰기 조건 조합을 포함합니다.

   - **규칙 시퀀스**: 다시 쓰기 규칙이 실행되는 순서를 결정하는 데 도움이 됩니다. 이 구성은 다시 쓰기 집합에 여러 번 다시 쓰기 규칙이 있는 경우에 유용합니다. 규칙 시퀀스 값이 낮은 다시 쓰기 규칙이 먼저 실행됩니다. 동일한 규칙 시퀀스 값을 두 개의 다시 쓰기 규칙에 할당하는 경우 실행 순서는 결정적이지 않습니다.

   - **다시 쓰기 집합**: 요청 라우팅 규칙과 연결될 여러 재작성 규칙을 포함합니다.

2. 다시 쓰기 집합을 라우팅 규칙에 연결합니다. 재작성 구성은 라우팅 규칙을 통해 소스 수신기에 연결됩니다. 기본 라우팅 규칙을 사용하는 경우 헤더 재작성 구성은 원본 수신기와 연결되며 전역 헤더 다시 작성입니다. 경로 기반 라우팅 규칙을 사용하는 경우 헤더 재작성 구성이 URL 경로 맵에 정의됩니다. 이 경우 사이트의 특정 경로 영역에만 적용됩니다.

여러 HTTP 헤더 다시 쓰기 집합을 만들고 각 재쓰기 집합을 여러 수신기에 적용할 수 있습니다. 그러나 특정 수신기에 하나의 재쓰기 집합만 적용할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com/) 에 로그인합니다.

## <a name="configure-header-rewrite"></a>헤더 재작성 구성

이 예제에서는 백 엔드 응용 프로그램에서 보낸 HTTP 응답에서 위치 헤더를 다시 작성 하여 리디렉션 URL을 수정합니다.

1. **모든 리소스를**선택한 다음 응용 프로그램 게이트웨이를 선택합니다.

2. 왼쪽 창에서 **다시 쓰기를** 선택합니다.

3. **다시 쓰기 세트**선택 :

   ![다시 쓰기 세트 추가](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. 다시 쓰기 집합의 이름을 제공하고 라우팅 규칙과 연결합니다.

   - **이름** 상자에 다시 쓰기 집합의 이름을 입력합니다.
   - **연결된 라우팅 규칙** 목록에 나열된 규칙 중 하나 이상을 선택합니다. 다른 재작성 세트와 연결되지 않은 규칙만 선택할 수 있습니다. 이미 다른 재쓰기 세트와 연결된 규칙은 흐리게 표시됩니다.
   - **다음**을 선택합니다.
   
     ![이름 및 연결 추가](media/rewrite-http-headers-portal/name-and-association.png)

5. 다시 쓰기 규칙 만들기:

   - **다시 쓰기 규칙 추가를 선택합니다.**

     ![다시 쓰기 규칙 추가](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - 다시 쓰기 **규칙 이름** 상자에 다시 쓰기 규칙에 대한 이름을 입력합니다. **규칙 시퀀스** 상자에 숫자를 입력합니다.

     ![다시 쓰기 규칙 이름 추가](media/rewrite-http-headers-portal/rule-name.png)

6. 이 예제에서는 azurewebsites.net 대한 참조가 포함된 경우에만 위치 헤더를 다시 작성합니다. 이렇게 하려면 응답을 위한 위치 헤더에 azurewebsites.net 포함 되는지 여부를 평가 하는 조건을 추가 합니다.

   - **조건 추가를** 선택한 다음 **If** 지침이 포함된 상자를 선택하여 확장합니다.

     ![조건 추가](media/rewrite-http-headers-portal/add-condition.png)

   - 목록을 **검사할 변수 유형에서** **HTTP 헤더를**선택합니다.

   - 헤더 **유형** 목록에서 **응답을**선택합니다.

   - 이 예제에서는 공통 헤더인 위치 헤더를 평가하기 때문에 **헤더 이름**아래에서 **공통 헤더를** 선택합니다.

   - 공통 **헤더** 목록에서 **위치**를 선택합니다.

   - **대/소문자 구분**에서 **아니요를**선택합니다.

   - **연산자** 목록에서 **같음(=)을**선택합니다.

   - 정규식 패턴을 입력합니다. 이 예제에서는 패턴을 `(https?):\/\/.*azurewebsites\.net(.*)$`사용합니다.

   - **확인**을 선택합니다.

     ![If 조건 구성](media/rewrite-http-headers-portal/condition.png)

7. 위치 헤더를 다시 작성하는 작업을 추가합니다.

   - 작업 **유형** 목록에서 **을 선택합니다.**

   - 헤더 **유형** 목록에서 **응답을**선택합니다.

   - **헤더 이름**아래에서 **공통 헤더를**선택합니다.

   - 공통 **헤더** 목록에서 **위치**를 선택합니다.

   - 헤더 값을 입력합니다. 이 예제에서는 헤더 값으로 사용합니다. `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 이 값은 *azurewebsites.net* 위치 헤더의 *contoso.com* 대체합니다.

   - **확인**을 선택합니다.

     ![작업 추가](media/rewrite-http-headers-portal/action.png)

8. 다시 쓰기 세트를 만들려면 **만들기를** 선택합니다.

   ![만들기 선택](media/rewrite-http-headers-portal/create.png)

9. 다시 쓰기 집합 보기가 열립니다. 만든 재작성 세트가 다시 쓰기 집합 목록에 있는지 확인합니다.

   ![세트 뷰 다시 작성](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>다음 단계

몇 가지 일반적인 사용 사례를 설정하는 방법에 대한 자세한 내용은 [일반적인 헤더 다시 쓰기 시나리오를](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)참조하십시오.