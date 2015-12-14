<properties
	pageTitle="DocumentDB 계정 제한 증가 요청 | Microsoft Azure"
	description="허용되는 컬렉션, 저장 프로시저 및 쿼리 절 수와 같은 DocumentDB 제한의 조정을 요청하는 방법을 알아봅니다."
	services="documentdb"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/22/2015"
	ms.author="anhoh"/>

# DocumentDB 계정 제한 증가 요청

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)에는 일련의 기본 제한 및 할당량 적용이 있습니다. Azure 지원에 문의하여 여러 할당량을 조정할 수 있습니다. 이 문서에서는 계정 제한 증가를 요청하는 방법을 보여 줍니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

-	Azure 지원에 문의하여 조정할 수 있는 DocumentDB 계정 할당량은 무엇인가요?
-	어떻게 DocumentDB 계정 할당량 조정을 요청할 수 있나요?

##<a id="AdjustableQuotas"></a> 조정 가능한 DocumentDB 계정 할당량

다음 표에서는 Azure 지원에 문의하여 조정할 수 있는 DocumentDB 할당량에 대해 설명합니다.

|엔터티 |할당량(표준 제공)|
|-------|--------|
|데이터베이스 계정 |5
|컬렉션당 저장 프로시저, 트리거 및 UDF 수 |각 25개
|데이터베이스 계정당 최대 컬렉션 |100
|데이터베이스(100개 컬렉션)당 최대 문서 저장소 |1TB
|쿼리당 최대 UDF 수 |2
|쿼리당 최대 JOIN 수 |5
|쿼리당 최대 AND 절 수 |20
|쿼리당 최대 OR 절 수 |10
|IN 식당 최대 값 수 |100
|ST\_WITHIN 쿼리의 다각형 인수에 있는 점의 최대 수 |16
|분당 최대 컬렉션 생성 수 |5
|분당 최대 크기 조정 작업 수 |5

##<a id="RequestQuotaIncrease"></a> 할당량 조정 요청
다음 단계에서는 할당량 조정을 요청하는 방법을 보여 줍니다.

1. [Azure 포털](https://portal.azure.com)에서 **찾아보기**를 클릭한 다음 **도움말+지원**을 클릭합니다.

	![도움말 및 지원 시작의 스크린샷](media/documentdb-increase-limits/helpsupport.png)

2. **도움말+지원** 블레이드에서 **지원 받기**를 클릭합니다.

	![지원 티켓 만들기의 스크린샷](media/documentdb-increase-limits/getsupport.png)

3. **새 지원 요청 블레이드**에서 **기본**을 클릭하세요. 다음, **문제 유형**을 **할당량**으로, **구독**을 DocumentDB에 호스트하는 구독으로, **서비스**를 **DocumentDB**로, **지원 계획**을 **할당량 지원 -포함**으로 설정합니다. 마지막으로 **다음**을 클릭합니다.

	![지원 티켓 요청 유형의 스크린샷](media/documentdb-increase-limits/supportrequest1.png)

4. **문제** 블레이드에서 심각도를 선택합니다. **문제 유형**을 **DocumentDB**로 선택하고 할당량 증가에 대한 정보를 **세부사항**에 포함합니다. **다음**을 클릭합니다.

	![지원 티켓 구독 선택의 스크린샷](media/documentdb-increase-limits/supportrequest2.png)

5. 마지막으로 **연락처 정보 블레이드**에 연락처 정보를 입력합니다.

	![지원 티켓 리소스 선택의 스크린샷](media/documentdb-increase-limits/supportrequest3.png)

지원 티켓이 생성되면 메일을 통해 지원 요청 번호를 받게 됩니다. 또한 **도움말+지원** 블레이드의 **지원 요청 관리**를 클릭하여 지원 요청을 볼 수 있습니다.

![지원 요청 블레이드의 스크린샷](media/documentdb-increase-limits/supportrequest4.png)


##<a name="NextSteps"></a> 다음 단계
- DocumentDB에 대해 자세히 알아보려면 [여기](http://azure.com/docdb)를 클릭하세요.

<!---HONumber=AcomDC_1203_2015-->