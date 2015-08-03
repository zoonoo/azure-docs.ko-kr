<properties 
	pageTitle="인코딩 단위를 추가하는 방법" 
	description=".NET을 사용하여 인코딩 단위를 추가하는 방법 알아보기"  
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="juliako"/>




#.NET SDK를 사용하여 인코딩 크기를 조정하는 방법

이 문서는 [미디어 서비스 주문형 비디오 워크플로](media-services-video-on-demand-workflow.md) 시리즈의 일부입니다.
  
##개요

미디어 서비스 계정은 인코딩 작업을 처리하는 속도를 결정하는 예약 단위 유형에 연관됩니다. Basic, Standard 또는 Premium 예약 단위 유형 중에서 선택할 수 있습니다. 예를 들어 Standard 예약 단위 유형을 사용하는 경우 Basic 유형에 비해 동일한 인코딩 작업이 더 빠르게 실행됩니다. 자세한 내용은 [Milan Gada](http://azure.microsoft.com/blog/author/milanga/)가 작성한 블로그인 "인코딩 예약 단위 유형"을 참조하세요.

예약 단위 유형을 지정하는 것 외에도 인코딩 예약 단위를 사용하여 계정을 프로비전하도록 지정할 수 있습니다. 프로비전된 인코딩 예약 단위의 수는 특정 계정에서 동시에 처리할 수 있는 미디어 작업의 수를 결정합니다. 예를 들어 계정에 5개의 예약 단위가 있는 경우 처리할 미디어 작업이 있다면 5개의 미디어 작업이 동시에 실행됩니다. 나머지 작업은 큐에 대기하다가 실행 중인 작업이 완료되자마자 순차적으로 처리를 위해 선택됩니다. 계정에 프로비전된 예약 단위가 없는 경우에는 작업이 순차적으로 선택됩니다. 이 경우 한 작업 완료와 다음 작업 시작 사이의 대기 시간은 시스템의 리소스 가용성에 따라 다릅니다.

.NET SDK를 사용하여 예약 단위 유형 및 인코딩 예약 단위 수를 변경하려면 다음을 수행합니다.

	IEncodingReservedUnit encodingBasicReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
	encodingBasicReservedUnit.ReservedUnitType = ReservedUnitType.Basic;
	encodingBasicReservedUnit.Update();
	Console.WriteLine("Reserved Unit Type: {0}", encodingBasicReservedUnit.ReservedUnitType);
	
	encodingBasicReservedUnit.CurrentReservedUnits = 2;
	encodingBasicReservedUnit.Update();
	
	Console.WriteLine("Number of reserved units: {0}", encodingBasicReservedUnit.CurrentReservedUnits);

##지원 티켓 열기

기본적으로 모든 미디어 서비스 계정은 인코딩 예약 단위 최대 25개, 주문형 스트리밍 예약 단위 최대 5개까지 확장할 수 있습니다. 지원 티켓을 열어 더 높은 한도를 요청할 수 있습니다.

###지원 티켓 열기

지원 티켓을 열려면 다음을 수행합니다.

1. [지원 받기](https://manage.windowsazure.com/?getsupport=true)를 클릭합니다. 로그인하지 않은 경우 자격 증명을 입력하라는 메시지가 표시됩니다.

1. 사용 중인 구독을 선택합니다.
 
1. 지원 유형으로 "기술적"을 선택합니다.
 
1. "티켓 만들기"를 클릭합니다.
 
1. 다음 페이지에 나타나는 제품 목록에서 "Azure 미디어 서비스"를 선택합니다.
 
1. 사용자의 문제에 적절한 "문제 유형"을 선택합니다.
 
1. 계속을 클릭합니다.
 
1. 다음 페이지의 지시에 따라 문제에 대한 세부 정보를 입력합니다.
 
1. 제출을 클릭하여 티켓을 엽니다.
 

 

<!---HONumber=July15_HO4-->