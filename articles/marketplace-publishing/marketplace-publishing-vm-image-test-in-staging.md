<properties
   pageTitle="마켓플레이스용 VM 제품 테스트 | Microsoft Azure"
   description="Azure 마켓플레이스용 VM 이미지를 테스트하는 방법을 이해합니다."
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
   ms.date="10/09/2015"
   ms.author="hascipio" />

# 스테이징에서 Azure 마켓플레이스용 VM 제품 테스트

스테이징이란 SKU를 마켓플레이스에 배포하기 전에 기능을 테스트하고 확인할 수 있는 사설 "샌드박스"에 배포하는 것을 의미합니다. 그러면 SKU는 배포한 고객에게 표시되는 것처럼 스테이징으로 표시됩니다. VM 이미지는 인증되어야만 스테이징으로 푸시될 수 있습니다.

## 1단계: 제품을 스테이징으로 푸시

1. **게시** 탭에서 **스테이징으로 푸시**를 클릭합니다.

  ![그리기](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. 게시 포털에서 오류에 대한 알림이 표시되는 경우 오류를 해결합니다.
3.	**스테이징된 제품에 액세스할 수 있는 사람** 대화 상자에서 [Azure Preview 포털](https://portal.azure.com)에서 제품을 미리 보는 데 사용할 Azure 구독 목록을 입력합니다.
4. 이전 단계에서 나열된 Azure 구독 중 하나를 사용하여 [Azure Preview 포털](http://portal.azure.com)에 로그인합니다.
5. 제품을 찾고 VM 이미지 지점의 유효성을 검사합니다.
  1. 마케팅 콘텐츠가 마켓플레이스에 올바르게 표시되는지 확인합니다.

      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

  2. VM 이미지의 종단 간 배포

> [AZURE.IMPORTANT]제품을 프로덕션으로 푸시할 준비가 되었다고 게시 포털을 통해 Microsoft에 알릴 때까지[**게시** > **"프로덕션으로 푸시 승인 요청"**] 제품은 스테이징으로 유지됩니다. 이 시간 동안 팀의 모든 구성원이 제품을 사용할 준비가 되었는지 확인할 수 있습니다.

> 데이터 센터 간 복제에는 최대 48시간이 걸립니다. 복제가 완료되면 [Azure 마켓플레이스](http://azure.microsoft.com/marketplace)에 제품이 나열됩니다.

## 다음 단계
제품이 "스테이징"되었고 제품의 기능 및 마케팅 콘텐츠를 테스트했으므로 최종 게시 단계인 **4단계**, [마켓플레이스에 제품 배포](marketplace-publishing-push-to-production.md)를 진행할 수 있습니다.

## 참고 항목
- [시작: Azure 마켓플레이스에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)

<!---HONumber=Nov15_HO4-->