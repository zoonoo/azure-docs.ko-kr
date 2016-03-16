<properties
   pageTitle="Azure 이벤트 허브로 공용 데이터 풀 | Microsoft Azure"
   description="웹 샘플에서 이벤트 허브 가져오기 개요"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor=""/>

<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="spyros;sethm" />

# Azure 이벤트 허브로 공용 데이터 풀

일반적인 IoT(사물 인터넷) 시나리오에서는 데이터를 Azure로 푸시하도록 프로그래밍할 수 있는 장치인 Azure 이벤트 허브 또는 IoT Hub를 사용합니다. 이 두 허브는 Microsoft Azure에서 사용할 수 있는 다양한 도구로 저장, 분석 및 시각화하기 위해 Azure로 들어가는 진입점입니다. 그러나 둘 다 데이터를 푸시해야 하고 형식이 JSON으로 지정되어야 하며 특정한 방식으로 보안되어야 합니다. 이에 대해 다음 사항을 질문할 수 있습니다. 데이터가 웹 서비스 또는 일종의 피드로 노출되는 공용 또는 개인 원본에서 데이터를 가져오려고 하지만 데이터를 게시하는 방법을 변경하는 기능이 없는 경우 어떻게 하나요? 날씨, 교통 또는 주식 시세를 생각해 보겠습니다. 이벤트 허브에 푸시를 구성하기 위해 NOAA 또는 WSDOT 또는 NASDAQ에 이야기할 수는 없습니다. 이 문제를 해결하기 위해 원본 등에서 데이터를 풀하여 이벤트 허브에 푸시하는, 수정 및 배포 가능한 작은 클라우드 샘플을 작성하여 오픈 소스화했습니다. 여기에서 제목은 물론 생산자의 사용 조건까지 원하는 모든 작업을 수행할 수 있습니다. [여기](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/)에서 응용 프로그램을 찾을 수 있습니다.

이 샘플의 코드는 일반적인 웹 피드에서 데이터를 풀하는 방법 및 Azure 이벤트 허브에 쓰는 방법만 보여 줍니다. 이 샘플은 프로덕션 응용 프로그램용이 아니며 그런 환경에서 사용하기에 적합하도록 만들어지지도 않았습니다. 이는 엄격하게 DIY로, 개발자에게만 초점을 맞춘 예제입니다. 또한 이 샘플은 데이터를 Azure에 **푸시**하기보다는 **풀**해야 한다는 것도 아닙니다. 보안, 성능, 기능 및 비용 요소를 검토한 다음 종단 간 아키텍처를 해결해야 합니다.

## 응용 프로그램 구조

응용 프로그램은 C#으로 작성되고 [샘플 설명](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/)은 응용 프로그램을 수정, 빌드 및 게시해야 하는 모든 정보를 포함합니다. 다음 섹션에서는 응용 프로그램 동작에 대한 대략적인 개요를 제공합니다.

데이터 피드에 대한 액세스 권한이 있다고 가정하고 시작하겠습니다. 예를 들어 워싱턴 주 운수부에서 교통 데이터를 가져오거나 NOAA에서 날씨 데이터를 가져와서 사용자 지정 보고서를 표시하거나 해당 데이터를 응용 프로그램의 다른 데이터와 조합할 수 있습니다. 또한 Azure 이벤트 허브를 설정하거나 여기에 액세스하기 위해 필요한 연결 문자열을 알아야 할 수 있습니다.

GenericWebToEH 솔루션을 시작하면 다음과 같은 많은 항목을 얻기 위해 구성 파일(App.config)을 읽습니다.

1. 데이터를 게시하는 사이트에 대한 URL 또는 URL 목록. 이상적으로 이는 WSDOT [여기](http://www.wsdot.wa.gov/Traffic/api/)에서 참조된 것과 같이 JSON 형식으로 데이터를 게시하는 사이트입니다. 
2. 필요한 경우 URL에 대한 자격 증명. 많은 공용 원본에서 자격 증명을 필요로 하지 않거나 URL 문자열에 자격 증명을 넣을 수 있습니다. 별도로 제공해야 하는 경우도 있습니다. (이 응용 프로그램에서는 하나의 자격 증명 집합만 지정할 수 있으므로 URL 목록이 아닌 URL을 하나만 지정하는 경우에만 작동합니다.)
3. 데이터를 푸시하는 서비스 버스 연결 문자열 및 해당 서비스 버스 네임스페이스의 이벤트 허브 이름. 이 정보는 Azure 클래식 포털에서 찾을 수 있습니다.
4. 공용 데이터 사이트를 폴링하는 간격을 위한 밀리초 단위의 절전 간격. 이 설정은 신중히 생각해야 합니다. 데이터를 너무 가끔 폴링하면 데이터를 놓칠 수 있습니다. 반면에 너무 자주 폴링하면 반복되는 데이터가 많이 발생하거나 심한 경우 악의적인 봇으로 차단될 수 있습니다. 데이터 원본을 업데이트하는 빈도는 날씨나 교통 데이터는 15분마다 새로 고칠 수 있지만 주식은 어디서 그 정보를 얻느냐에 따라 몇 초마다 새로 고쳐야 할 수 있습니다. 
5. 응용 프로그램에 데이터가 JSON 또는 XML로 제공되는지 여부를 알려 주는 플래그입니다. 이벤트 허브에 데이터를 푸시해야 하므로 보내기 전에 XML을 JSON으로 변환하는 모듈이 응용 프로그램에 있습니다.

구성 파일을 읽고 나면 응용 프로그램이 루프를 수행합니다. 즉, 공용 웹 사이트에 액세스하여 필요에 따라 데이터를 변환하고 이벤트 허브에 쓴 다음 절전 간격을 기다렸다가 다시 반복해서 이 작업을 수행합니다. 구체적으로 살펴보면 다음과 같습니다.

  * 공용 웹 사이트를 읽습니다. 보낼 준비가 된 데이터를 받기 위해 RawXMLWithHeaderToJsonReader 클래스의 인스턴스가 Azure/GenericWebToEH/ApiReaders/RawXMLWithHeaderToJsonReader.cs에서 사용됩니다. Getdata() 메서드에서 원본 스트림을 읽은 다음 GetXmlFromOriginalText를 사용하여 더 작게(예: 레코드) 분할합니다. 이 메서드는 XML뿐만 아니라 잘 구성된(Well-Formed) JSON 또는 JSON 배열도 읽습니다. 그런 다음 App.config의 MergeToXML 구성(기본값은 비어 있음)을 사용하여 처리가 시작됩니다.
  * 수신 및 전송 데이터는 Program.cs의 Process() 메서드에서 루프로 구현됩니다. Getdata()에서 출력 결과를 받은 후 메서드가 구분된 값을 이벤트 허브의 큐에 넣습니다.

## 다음 단계

솔루션을 배포하려면 [GenericWebToEH](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) 응용 프로그램을 복제하거나 다운로드하고 App.config 파일을 편집하고 빌드한 다음 마지막으로 게시합니다. 응용 프로그램을 게시하고 나면 클라우드 서비스의 Azure 클래식 포털에서 응용 프로그램이 실행되는 것이 보이고 **구성** 탭의 일부 구성 설정(예: 이벤트 허브 대상 및 절전 간격)을 변경할 수 있습니다.

[Azure 샘플 갤러리](https://azure.microsoft.com/documentation/samples/?service=event-hubs) 및 [MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)에서 더 많은 이벤트 허브 샘플을 볼 수 있습니다.

<!---HONumber=AcomDC_0211_2016-->