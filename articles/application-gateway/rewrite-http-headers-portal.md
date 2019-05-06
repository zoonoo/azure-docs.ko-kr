---
title: Azure Application Gateway-Azure portal 사용 하 여 HTTP 요청 및 응답 헤더를 다시 작성 | Microsoft Docs
description: 요청 및 게이트웨이 통해 전달 하는 응답의 HTTP 헤더를 다시 작성 하는 Azure Application Gateway를 구성 하려면 Azure portal을 사용 하는 방법 알아보기
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/10/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: e144214a58f9fe383cf4edd878554792d9d6a6f9
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947158"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Azure Application Gateway-Azure portal 사용 하 여 HTTP 요청 및 응답 헤더를 다시 작성

이 문서에서는 구성 하려면 Azure portal을 사용 하는 방법에 설명 합니다는 [Application Gateway v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) 요청 및 응답의 HTTP 헤더를 다시 작성 하는 인스턴스.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료 하려면 Application Gateway v2 SKU 인스턴스 해야 합니다. 헤더를 다시 작성은 v1 SKU에서 지원 되지 않습니다. V2 SKU가 없는 경우 만듭니다는 [Application Gateway v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) 시작 하기 전에 인스턴스.

## <a name="create-required-objects"></a>필요한 개체 만들기

HTTP 헤더 다시 쓰기를 구성 하려면 다음이 단계를 완료 해야 합니다.

1. HTTP 헤더 다시 쓰기에 필요한 개체를 만듭니다.

   - **작업을 다시 작성**: 요청 및 요청 헤더 필드를 다시 작성 하려는 헤더에 대 한 새 값을 지정 하는 데 사용 합니다. 연결할 수 있습니다 또는 다시 작성 작업을 사용 하 여 조건을 다시 작성 더 합니다.

   - **조건을 다시 작성**: 선택적 구성입니다. 다시 쓰기 조건이 HTTP (S) 요청 및 응답의 콘텐츠를 평가 합니다. 다시 작성 작업을 다시 작성 조건과 일치 하는 HTTP (S) 요청 또는 응답 하는 경우 발생 합니다.

     작업을 사용 하 여 하나 이상의 조건에 연결 하는 경우 모든 조건이 충족 되는 경우에 동작이 발생 합니다. 즉, 논리적 AND 연산을 작업이 나타납니다.

   - **재작성 규칙**: 여러 다시 쓰기 작업을 포함 / 조건 조합을 다시 작성 합니다.

   - **규칙 순서**: 재작성 규칙 실행 되는 순서를 확인할 수 있습니다. 이 구성을 다시 쓰기 집합의 다시 쓰기 규칙이 여러 개 있는 경우에 유용 합니다. 낮은 규칙 순서 값이 있는 다시 쓰기 규칙을 먼저 실행 됩니다. 두 재작성 규칙에 동일한 규칙 순서 값을 할당 하는 경우 실행 순서가 명확 하지 않습니다.

   - **집합을 다시 작성**: 요청 라우팅 규칙을 사용 하 여 관련 된 여러 다시 쓰기 규칙을 포함 합니다.

2. 라우팅 규칙에 설정 다시 쓰기를 연결 합니다. 다시 쓰기 구성 라우팅 규칙을 통해 원본 수신기에 연결 됩니다. 기본 라우팅 규칙을 사용 하면 헤더 재작성 구성이 원본 수신기와 연결 된 이며 전체 머리글을 다시 작성 합니다. 경로 기반 라우팅 규칙을 사용 하는 경우 헤더 재작성 구성은 URL 경로 맵에 정의 됩니다. 이 경우 사이트의 특정 경로 영역에만 적용 됩니다.

여러 HTTP 헤더 재작성 집합을 만들고 여러 수신기를 설정 하는 각 다시 쓰기를 적용할 수 있습니다. 그러나 특정 수신기로 다시 작성 하나에 적용할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com/) 에 로그인합니다.

## <a name="configure-header-rewrite"></a>헤더 다시 쓰기를 구성 합니다.

이 예제에서는 백 엔드 응용 프로그램에서 보낸 HTTP 응답의 location 헤더를 다시 작성 하 여 리디렉션 URL을 수정 합니다.

1. 선택 **모든 리소스**, 한 다음 application gateway를 선택 합니다.

2. 선택 **다시 작성** 왼쪽된 창에서.

3. 선택 **집합을 다시 작성**:

   ![다시 쓰기 집합 추가](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. 다시 작성 집합에 대 한 이름을 입력 하 고 라우팅 규칙을 사용 하 여 연결 합니다.

   - 설정 다시 쓰기에 대 한 이름을 입력 합니다 **이름을** 상자입니다.
   - 에 나열 된 규칙 중 하나 이상을 선택 합니다 **라우팅 규칙을 연결 된** 목록입니다. 다른 재작성 집합과 관련 된 규칙만 선택할 수 있습니다. 이미 다른 재작성 집합과 연결 하는 규칙을 흐리게 표시 됩니다.
   - **다음**을 선택합니다.
   
     ![이름 및 연결 추가](media/rewrite-http-headers-portal/name-and-association.png)

5. 다시 쓰기 규칙을 만듭니다.

   - 선택 **재작성 규칙 추가**합니다.

     ![다시 쓰기 규칙 추가](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - 재작성 규칙에 대 한 이름을 입력 합니다 **재작성 규칙 이름** 상자입니다. 에 값을 입력 합니다 **규칙 순서** 상자입니다.

     ![다시 쓰기 규칙 이름 추가](media/rewrite-http-headers-portal/rule-name.png)

6. 이 예제에서는 azurewebsites.net에 대 한 참조를 포함 하는 경우에 위치 헤더를 다시 작성 됩니다. 이 위해 응답의 location 헤더 azurewebsites.net 포함 되는지 여부를 평가 하는 조건을 추가 합니다.

   - 선택 **조건 추가** 를 선택한 다음 포함 하는 상자를 **경우** 확장 지침.

     ![조건 추가](media/rewrite-http-headers-portal/add-condition.png)

   - 에 **검사할 변수의 형식을** 목록에서 **HTTP 헤더**합니다.

   - 에 **헤더 형식** 목록에서 **응답**합니다.

   - 이 예제에서 공용 헤더는 location 헤더를 평가 하는 것 이므로 선택 **공용 헤더** 아래에서 **헤더 이름을**입니다.

   - 에 **공용 헤더** 목록에서 **위치**합니다.

   - 아래 **대/소문자 구분**를 선택 **No**합니다.

   - 에 **연산자** 목록에서 **등호 (=)** 합니다.

   - 정규식 패턴을 입력 합니다. 이 예에서는 사용 하 여 패턴 `(https?):\/\/.*azurewebsites\.net(.*)$`합니다.

   - **확인**을 선택합니다.

     ![If 구성 상태](media/rewrite-http-headers-portal/condition.png)

7. 위치 헤더를 다시 작성 작업을 추가 합니다.

   - 에 **동작 유형** 목록에서 **설정**합니다.

   - 에 **헤더 형식** 목록에서 **응답**합니다.

   - 아래 **헤더 이름을**를 선택 **공용 헤더**합니다.

   - 에 **공용 헤더** 목록에서 **위치**합니다.

   - 헤더 값을 입력 합니다. 이 예에서는 사용 하 여 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 헤더 값으로. 이 값으로 대체 됩니다 *azurewebsites.net* 사용 하 여 *contoso.com* location 헤더에 있습니다.

   - **확인**을 선택합니다.

     ![작업 추가](media/rewrite-http-headers-portal/action.png)

8. 선택 **만들기** 집합을 다시 쓰기를 만들려면:

   ![만들기 선택](media/rewrite-http-headers-portal/create.png)

9. 다시 쓰기 집합 보기가 열립니다. 다시 쓰기 집합의 목록에서 만든 재작성 세트가 올바른지 확인 합니다.

   ![집합 뷰를 다시 작성](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>다음 단계

몇 가지 일반적인 사용 사례를 설정 하는 방법에 대 한 자세한 내용은 참조 하세요 [공용 헤더 시나리오를 다시 작성](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)합니다.