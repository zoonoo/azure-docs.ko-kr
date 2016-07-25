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
	ms.date="06/28/2016"
	ms.author="anhoh"/>

# DocumentDB 계정 제한 증가 요청

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)에는 일련의 기본 제한 및 할당량 적용이 있습니다. Azure 지원에 문의하여 여러 할당량을 조정할 수 있습니다. 이 문서에서는 계정 제한 증가를 요청하는 방법을 보여 줍니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

-	Azure 지원에 문의하여 조정할 수 있는 DocumentDB 계정 할당량은 무엇인가요?
-	어떻게 DocumentDB 계정 할당량 조정을 요청할 수 있나요?

##<a id="Quotas"></a> DocumentDB 계정 할당량

다음 표에 DocumentDB 할당량이 나와 있습니다. 별표(*)가 표시된 할당량은 Azure 지원부에 문의하여 조정할 수 있습니다.

[AZURE.INCLUDE [azure-documentdb-limits](../../includes/azure-documentdb-limits.md)]


##<a id="RequestQuotaIncrease"></a> 할당량 조정 요청
다음 단계에서는 할당량 조정을 요청하는 방법을 보여 줍니다.

1. [Azure 포털](https://portal.azure.com)에서 **찾아보기**를 클릭한 다음 **도움말+지원**을 클릭합니다.

	![도움말 및 지원 시작의 스크린샷](media/documentdb-increase-limits/helpsupport.png)

2. **도움말 + 지원** 블레이드에서 **지원 요청 만들기**를 클릭합니다.

	![지원 티켓 만들기의 스크린샷](media/documentdb-increase-limits/getsupport.png)

3. **새 지원 요청 블레이드**에서 **기본**을 클릭하세요. 다음, **문제 유형**을 **할당량**으로, **구독**을 DocumentDB 계정을 호스트하는 구독으로, **할당량 형식**을 **DocumentDB**로, **지원 계획**을 **할당량 지원 - 포함**으로 설정합니다. 마지막으로 **다음**을 클릭합니다.

	![지원 티켓 요청 유형의 스크린샷](media/documentdb-increase-limits/supportrequest1.png)

4. **문제** 블레이드에서 심각도를 선택하고 할당량 증가에 대한 정보를 **세부사항**에 포함합니다. **다음**을 클릭합니다.

	![지원 티켓 구독 선택의 스크린샷](media/documentdb-increase-limits/supportrequest2.png)

5. 마지막으로 **연락처 정보** 블레이드에 연락처 정보를 입력하고 **만들기**를 클릭합니다.

	![지원 티켓 리소스 선택의 스크린샷](media/documentdb-increase-limits/supportrequest3.png)

지원 티켓이 생성되면 메일을 통해 지원 요청 번호를 받게 됩니다. 또한 **도움말+지원** 블레이드의 **지원 요청 관리**를 클릭하여 지원 요청을 볼 수 있습니다.

![지원 요청 블레이드의 스크린샷](media/documentdb-increase-limits/supportrequest4.png)


##<a name="NextSteps"></a> 다음 단계
- DocumentDB에 대해 자세히 알아보려면 [여기](http://azure.com/docdb)를 클릭하세요.

<!---HONumber=AcomDC_0713_2016-->