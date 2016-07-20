<properties
   pageTitle="마켓플레이스용 데이터 서비스 만들기 가이드 | Microsoft Azure"
   description="Azure 마켓플레이스에서 구매하기 위한 데이터 서비스를 만들고 인증하고 배포하는 방법에 대한 자세한 지침입니다."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="06/28/2016"
      ms.author="hascipio; avikova" />

# Azure 마켓플레이스에 대한 데이터 서비스 게시 가이드
1단계 [계정 만들기 및 등록](marketplace-publishing-accounts-creation-registration.md)을 완료한 후 Azure 마켓플레이스에서 제공하는 데이터 서비스 제품의 [일반 비기술 필수 조건](marketplace-publishing-pre-requisites.md) 및 [기술 필수 조건](marketplace-publishing-data-service-creation-prerequisites.md)을 안내해 드렸습니다. 이제 Azure 마켓플레이스의 [게시 포털][link-pubportal]에서 데이터 서비스 제품을 만드는 단계를 안내해 드리겠습니다.

## 1\. 게시 포털에 로그인합니다.

[https://publish.windowsazure.com](https://publish.windowsazure.com.)으로 이동합니다.

**처음으로 게시 포털에 로그인할 때는 개발자 센터에서 회사의 판매자 프로필이 등록된 계정을 동일하게 사용하세요.** 나중에 게시 포털에서 회사의 직원을 공동 관리자로 추가할 수 있습니다.

게시 포털에 처음 로그인하는 경우 **데이터 서비스 게시** 타일을 클릭합니다.

## 2\. 왼쪽의 탐색 메뉴에서 **데이터 서비스**를 선택합니다.

  ![그리기](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## 3\. 새 데이터 서비스를 만듭니다.

새 데이터 서비스 제품의 제목을 입력하고 오른쪽의 “+”를 클릭합니다.

  ![그리기](media/marketplace-publishing-data-service-creation/step-3.png)

## 4\. 탐색 메뉴에서 새로 만든 데이터 서비스 아래에 있는 하위 메뉴를 검토합니다.

**연습** 탭을 클릭하고 Azure 마켓플레이스에 데이터 서비스를 적절히 게시하는 데 필요한 모든 단계를 검토합니다.

> [AZURE.TIP] 언제든지 "연습" 페이지의 링크를 클릭하거나 왼쪽에서 데이터 서비스 제품 하위 메뉴의 탭을 사용할 수 있습니다.

## 5\. 새 플랜을 만듭니다.

### 제품, 플랜, 트랜잭션.

각 제품에 플랜이 여러 개 있어도 되는데, 적어도 플랜이 하나(1) 이상 있어야 합니다. 최종 사용자는 제품을 구독할 때 제품의 플랜 중 하나를 구독합니다. 각 플랜은 최종 사용자가 서비스를 이용할 수 있는 방식을 정의합니다.

현재 Azure 마켓플레이스는 데이터 서비스에 대한 월별 구독 트랜잭션 기반 모델만 지원합니다. 즉, 최종 사용자는 구독하는 특정 플랜의 가격에 따라 월정액 요금을 지불하게 되며 매월 플랜에 정의된 트랜잭션 수만큼 사용할 수 있습니다.

일반적으로 각 트랜잭션은 서비스로 전송되는 쿼리를 기준으로 데이터 서비스에서 반환하는 레코드 수로 정의됩니다. 기본값은 100입니다. 각 쿼리로 반환되는 트랜잭션 수는 레코드 수를 100으로 나눈 값을 가장 가까운 정수로 반올림한 숫자입니다.

각 쿼리에서 사용한 트랜잭션 수를 모니터링(측정)하는 것은 Azure 마켓플레이스 서비스 계층의 책임입니다.

> [AZURE.IMPORTANT] 한 달이 지나기 전에 트랜잭션 제한에 도달한 최종 사용자는 월간 구독 주기가 끝날 때까지 서비스를 사용할 수 없습니다.

> 플랜 또는 플랜 중 하나에 무제한 트랜잭션을 포함할 수 있습니다(필수는 아님).

### 플랜을 만듭니다.
1. “새 플랜 추가” 옆에 있는 **“+”**를 클릭합니다.

2. 이 플랜의 사용량을 **무제한** 또는 **제한** 중에 선택합니다. 제한을 선택할 경우 한 달 동안 플랜에 허용되는 트랜잭션 수를 입력합니다.

    ![그리기](media/marketplace-publishing-data-service-creation/step-5.1.png)

    또한 게시 포털에서 "플랜 식별자"를 제안할 것입니다. 플랜 식별자는 UI에서 최종 사용자에 플랜 이름을 알려주고 마켓플레이스 서비스가 플랜을 식별하는 데 사용됩니다. 원하는 경우 "플랜 식별자"를 변경할 수 있습니다.

    > [AZURE.NOTE] "플랜 식별자"는 각 제품의 범위 내에서 고유해야 합니다. 게시 포털에서 여러 가지 식별자가 사용되기 때문에 프로덕션에 처음으로 게시한 후 플랜 식별자가 잠기게 되고 더 이상 이 식별자를 변경할 수 없습니다.

3. 클릭하여 선택한 내용을 수락합니다.

4. 새로 만든 플랜에 대한 몇 가지 추가 질문을 받게 됩니다.

    ![그리기](media/marketplace-publishing-data-service-creation/step-5.2.png)


|질문|중요도|
|----|----|
|**이 계획은 무료이며 전 세계에서 사용할 수 있습니까?**|완전 무료 플랜을 만들 수 있습니다. 이 제품의 유일한 플랜인 경우 마켓플레이스에 "무료 제품"을 게시한다는 뜻입니다. 플랜 중 하나만 무료인 경우 월별 트랜잭션 수가 상대적으로 적은 서비스에 대해 자세히 알아보도록 최종 사용자에게 제안하는 옵션이 제공됩니다. "예"라고 대답하면 더 이상 질문이 나오지 않습니다.|

> [AZURE.NOTE] 최종 사용자는 언제든지 유료 플랜으로 업그레이드할 수 있습니다.

|질문|중요도|
|----|----|
|**무료 평가판이 있나요?**|“평가판 없음”을 선택할 수도 있고 “1개월” 동안 플랜을 사용할 수 있는 옵션을 제공할 수도 있습니다. 게시자는 최종 사용자가 1개월 동안 플랜을 무료로 사용하면서 제품의 장점을 이해할 수 있는 기회를 제공하기 위해 이 옵션을 선호합니다.|

> [AZURE.IMPORTANT] 최종 사용자는 신용 카드, 기업 계약 등의 결제 수단을 설정해야만 무료 평가판을 구입할 수 있습니다.

> 고객이 구독 취소 절차를 시작하지 않는 한, 1개월 무료 평가판 기간이 끝나면 Azure 마켓플레이스에서는 구독 날짜를 기준으로 고객에게 요금을 부과합니다. 최종 사용자에게 특별한 알림이 제공되지 않습니다.

|질문|중요도|
|----|----|
|**이 플랜을 구매하려면 프로모션 코드가 필요합니까?**| 게시자는 특정 고객에게 "Promocode"라는 특수 코드를 제공하여 서비스 플랜에 대한 액세스를 제한할 수 있습니다. 이 Promocode가 있는 최종 사용자만 플랜을 구독할 수 있습니다. "아니요"를 선택할 경우 제품이 제공되는 지역의 모든 사람이(자세한 내용은 [마켓플레이스 마케팅 콘텐츠 가이드](marketplace-publishing-push-to-staging.md) 참조) 이 플랜을 구독할 수 있다고 동의하는 것입니다. 더 이상 질문이 나오지 않습니다.|
|**유효한 프로모션 코드가 없는 사용자에게도 이 플랜을 숨길까요?**|이전 질문에 "예"라고 대답했으면 마켓플레이스 UI에서 이 플랜을 완전히 제거하는 옵션이 게시자에게 제공됩니다. 다시 말해서 고객이 제품 세부 정보 페이지에서 이 플랜을 볼 수 없습니다. 플랜 구매를 위한 promocode를 받은 최종 사용자는 이 promocode를 사용하여 플랜을 구독할 수 있습니다.|

## 6\. 마켓플레이스 마케팅 콘텐츠를 만듭니다.
**마케팅, 가격 책정, 지원 및 범주** 탭의 필수 정보를 입력하는 방법은 Azure 마켓플레이스에 게시된 모든 아티팩트에 공통적으로 적용되는 [마켓플레이스 마케팅 콘텐츠 가이드](marketplace-publishing-push-to-staging.md)를 참조하세요.

## 7\. 제품을 서비스(SQL Azure 기반 또는 웹 서비스 기반)에 연결합니다.

**데이터 서비스** 하위 메뉴를 클릭합니다.

페이지의 위쪽 절반에서 제품의 **네임스페이스**를 입력하라고 할 것입니다.

  ![그리기](media/marketplace-publishing-data-service-creation/step-7.png)

아래 질문은 게시자가 새로 만든 제품을 Azure 마켓플레이스에 노출하는 방법을 정의합니다. 자세한 내용은 [데이터 서비스 기술 필수 조건 가이드](marketplace-publishing-data-service-creation-prerequisites.md)를 참조하세요.

  ![그리기](media/marketplace-publishing-data-service-creation/step-7.2.png)

**데이터베이스 기반 서비스 게시**

**데이터베이스**를 클릭합니다. 다음 페이지가 나타납니다.

  ![그리기](media/marketplace-publishing-data-service-creation/step-7.3.png)

SQL Azure DB를 기반으로 데이터 집합에 대한 CSDL 매핑을 만들려면:

  ![그리기](media/marketplace-publishing-data-service-creation/step-7.4.png)

그 후 각 테이블에 대해

  ![그리기](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![그리기](media/marketplace-publishing-data-service-creation/step-7.6.png)

웹 서비스가 다음과 같으면

  ![그리기](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [AZURE.IMPORTANT] [CSDL을 통해 기존 서비스를 OData에 매핑](marketplace-publishing-data-service-creation-odata-mapping.md)을 읽고 CSDL 웹 서비스를 만드는 방법에 대한 지침과 예를 살펴보세요.

## 다음 단계
데이터 서비스 제품을 만들었으니, 이제 [마켓플레이스 마케팅 콘텐츠 가이드](marketplace-publishing-push-to-staging.md)의 지침을 완료한 후 [스테이징에서 데이터 서비스 테스트](marketplace-publishing-data-service-test-in-staging.md)를 진행하세요.

## 참고 항목
- [시작: Azure 마켓플레이스에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)
- 전체 OData 매핑 프로세스와 목적을 이해하려는 경우 문서 [데이터 서비스 OData 매핑](marketplace-publishing-data-service-creation-odata-mapping.md)을 읽고 정의, 구조 및 지침을 검토하세요.
- 특정 노드 및 해당 매개 변수를 학습하고 이해하려면 문서 [데이터 서비스 OData 매핑 노드](marketplace-publishing-data-service-creation-odata-mapping-nodes.md)에서 정의 및 설명, 예제, 사용 사례 컨텍스트를 살펴보세요.
- 예제를 검토하고 싶으면 [데이터 서비스 OData 매핑 예제](marketplace-publishing-data-service-creation-odata-mapping-examples.md) 문서를 통해 샘플 코드를 살펴보고 코드 구문 및 컨텍스트를 이해하세요.


[link-pubportal]: https://publish.windowsazure.com

<!---HONumber=AcomDC_0706_2016-->