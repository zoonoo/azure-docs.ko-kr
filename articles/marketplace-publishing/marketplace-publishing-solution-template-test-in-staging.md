<properties
   pageTitle="마켓플레이스에 대한 솔루션 템플릿 제품 테스트 | Microsoft Azure"
   description="Azure 마켓플레이스에 대한 솔루션 템플릿 제품을 테스트하는 방법을 이해합니다."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/08/2015"
   ms.author="hascipio; v-divte" />

# 준비 단계에서 솔루션 템플릿 제품 테스트
준비 단계에서는 제품을 프로덕션에 게시하기 전에 개인 "샌드박스"에 배포하여 기능을 테스트 및 확인할 수 있습니다. 제품이 해당 제품을 배포한 고객에게 보이는 것처럼 준비 단계에 표시됩니다. 제품이 **준비 단계에 푸시되려면 인증되어야 합니다**.

제품이 준비되면 [Azure Preview 포털](https://ms.portal.azure.com/)에서 제품을 보고 테스트할 수 있습니다.

제품을 준비 단계에 푸시하고 [Azure Preview 포털](https://ms.portal.azure.com/)에서 테스트를 수행하려면 아래 단계를 수행하세요.

1.	[게시 포털](https://publish.windowsazure.com)로 이동 -> **솔루션 템플릿** 탭 선택 -> 제품 -> **게시** -> **"Push to Staging"**(준비 단계로 푸시)을 클릭합니다.
2.	제품 미리 보기/테스트를 위해 사용할 Azure 구독 목록을 제공합니다.
3.	이전 단계에서 제품을 준비한 구독 ID를 동일하게 사용하여 Azure Preview 포털에 로그인합니다.
4.	아래 언급된 지점에 있는 Azure Preview 포털에서 테스트를 한 번 이상 수행합니다.
  -	마케팅 콘텐츠가 갤러리에 올바르게 표시됩니다.
  -	토폴로지의 종단간 배포
  -	성능 테스트 및 스트레스 테스트 수행
  -	토폴로지가 모범 사례에 부합하는지 확인합니다.

## 다음 단계
결과에 만족하면 최종 제품 게시 단계인 **4단계**, [마켓플레이스에 제품 배포](marketplace-publishing-push-to-production.md)로 진행하면 됩니다. 그렇지 않은 경우 제품을 변경하고 다시 인증 요청을 합니다.

> [AZURE.NOTE]마케팅 콘텐츠를 변경한 경우에는 인증이 필요하지 않습니다.

## 참고 항목
- [시작: Azure 마켓플레이스에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)

<!---HONumber=Oct15_HO3-->