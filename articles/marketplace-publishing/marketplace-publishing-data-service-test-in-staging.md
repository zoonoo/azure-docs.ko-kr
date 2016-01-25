<properties
   pageTitle="마켓플레이스용 데이터 서비스 제품 테스트 | Microsoft Azure"
   description="Azure 마켓플레이스용 데이터 서비스 제품을 테스트 하는 방법을 이해합니다."
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
   ms.date="01/04/2016"
   ms.author="hascipio; avikova" />

# 스테이징에서 데이터 서비스 제품 테스트
[Microsoft 개발자 계정 만들기](marketplace-publishing-accounts-creation-registration.md) 및 [게시 포털에서 데이터 서비스 제품 만들기](marketplace-publishing-data-service-creation.md)의 처음 두 단계를 완료하고 나면 Azure 마켓플레이스에 제품을 제공할 준비가 된 것입니다. 이 문서는 “스테이징”이라는 첫 번째 중간 단계를 안내합니다.

준비 단계에서는 제품을 프로덕션에 게시하기 전에 개인 "샌드박스"에 배포하여 기능을 테스트 및 확인할 수 있습니다. 제품이 해당 제품을 배포한 고객에게 보이는 것처럼 준비 단계에 표시됩니다.

## 1단계. 제품을 스테이징으로 푸시
스테이징으로 제품을 푸시하면 미래의 구독자에게 제품이 제공되기 전에 테스트할 수 있습니다. 데이터를 구독하는 사람들에게 제품이 어떻게 나타나고 작동하는지 볼 수 있습니다.

  ![그리기](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1.	[게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2.	왼쪽 탐색 창에서 **데이터 서비스**를 선택합니다.
3.	스테이징에 푸시할 제품을 선택합니다. 위와 같은 화면이 표시됩니다.
4.	**Push To Staging**(스테이징으로 푸시) 단추를 클릭합니다.  
5.	스테이징에 푸시하기 전에 제품에 대해 마무리해야 하는 문제가 있으면 해당 목록이 표시됩니다. 목록에서 각 항목을 클릭하여 이런 문제를 해결합니다. 해결이 완료되면 **Push to Staging**(스테이징으로 푸시) 단추를 다시 클릭합니다.

제품에 문제가 없으면 아래와 같은 팝업 창이 표시됩니다.

Azure 포털에 제품을 공개할 계획이 없거나 승인을 받지 못한 경우에는(현재 용량이 제한되어 있음) 팝업 창을 닫습니다.

Azure 포털(과 DataMarket 포털)에서 데이터 서비스를 테스트하려면, 테스트에 사용할 Azure 구독 ID가 필요합니다. 구독 ID는 제품 테스트가 허용되는 계정을 식별합니다.

구독 ID를 잘라내어 붙여넣고 계속하려면 확인 표시를 클릭합니다.

  ![그리기](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [AZURE.NOTE]Azure 구독 ID는 [Azure 관리 포털](https://manage.windowsazure.com) 내에서의 테스트와 스테이징에만 필요합니다. Azure 마켓플레이스에서 테스트하는 경우에는 필요하지 않습니다.

다음으로 표시되는 화면에서는 아래에 노란색으로 강조 표시된 “진행 중” 아이콘을 표시하여 게시가 이루어지고 있다는 것을 보여줍니다. 스테이징으로 푸시는 10분에서 15분 정도 소요됩니다. 이보다 오래 걸리면 우선 브라우저를 새로 고칩니다. (IE에서 F5키를 누릅니다.) 드문 경우지만, 한 시간이 지난 후에도 제품을 스테이징으로 푸시하고 있으면, 문의처 링크를 클릭하여 문제가 있다는 것을 알려 주세요.

  ![그리기](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

스테이징으로 푸시가 완료되면 “진행 중” 아이콘이 움직임을 멈추고 상태가 “준비됨”으로 업데이트됩니다. 이제 제품을 테스트할 준비가 되었습니다.

## 2단계. DataMarket에서 준비된 제품 테스트

**“See Your service offer at…”** 텍스트 다음에 나오는 링크를 클릭하여 제품이 프로덕션으로 이동하고 DataMarket에 공개되면 구독자에게 보여질 화면을 표시합니다.

  ![그리기](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

위에 표시된 12개 항목을 일일이 테스트하거나 검사하여 로고, 가격/트랜잭션, 텍스트, 이미지, 설명서, 링크가 모두 맞는지, 제대로 작동하는지 확인합니다. 제품을 만들 때 입력한 테스트 값이 실제 값으로 변경되었는지를 확인할 수 있는 좋은 기회입니다.

1. 제품 로고
2. Offer name
3. 게시자 이름/회사 웹 사이트 링크
4. 제품에 대한 검색 범주
5. 구독자 지원을 위한 제품 지원 링크
6. 제품에 대한 상황별 설명
7. 청구 세부 정보를 설명하는 제품 계획
8. 구현 코드에 대한 링크
9. 제품 데이터 사용을 보여주는 샘플 이미지
10. 제품 내 각 서비스에 대한 입/출력 메타데이터
11. 제품 사용 약관
12. 제품 데이터 미리 보기


마지막으로 “EXPLORE THIS DATASET”(DATASET 탐색) 링크를 클릭하여 서비스가 Datamarket을 통과하는지 확인합니다. 서비스에 대한 쿼리의 결과를 미리 볼 수 있도록 “Service Explorer”(서비스 탐색기)라는 도구에 새 창이 열립니다. 창에서 필요한 매개 변수를 입력하고 서비스에 대한 쿼리를 통해 표시되는 결과를 볼 수 있습니다. 쿼리에 대한 URL로 표시됩니다.

> [AZURE.NOTE]위쪽에 표시되는 서비스에 대한 텍스트 설명을 반드시 검토합니다. 제품이 둘 이상의 서비스 호출로 구성되는 경우에는 아래쪽의 탭을 클릭하고 다음 서비스로 전환하여 검토하고 테스트합니다.



## 다음 단계
문제가 있고 해결하기 위해서 도움이 필요하면 [Azure Publisher Support](http://go.microsoft.com/fwlink/?LinkId=272975)에 문의하세요.

결과가 만족스럽고 제품을 게시할 준비가 되면 [Request Approval to Push To Production](marketplace-publishing-push-to-production.md)(프로덕션에 푸시할 승인 요청) 설명서를 읽어보세요.

## 참고 항목
- [시작: Azure 마켓플레이스에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_0114_2016-->