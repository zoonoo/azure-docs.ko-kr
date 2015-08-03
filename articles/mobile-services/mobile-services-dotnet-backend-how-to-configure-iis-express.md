<properties 
	pageTitle="모바일 서비스의 로컬 테스트를 위해 IIS Express를 구성합니다." 
	description="테스트를 위해 로컬 모바일 서비스 프로젝트에 대한 연결을 허용하도록 IIS Express를 구성하는 방법을 알아봅니다." 
	authors="ggailey777" 
	manager="dwrede" 
	services="mobile-services" 
	documentationCenter="" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="glenga"/>

# 로컬 모바일 서비스에 연결할 수 있도록 로컬 웹 서버 구성 

Azure 모바일 서비스에서는 지원되는 .NET 언어 중 하나를 사용하여 Visual Studio에서 모바일 서비스를 만든 후 Azure에 게시할 수 있습니다. 모바일 서비스에 .NET 백 엔드를 사용할 경우 주요 이점 중 하나는 Azure에 게시하기 전에 로컬 컴퓨터나 가상 컴퓨터에서 모바일 서비스를 실행, 테스트 및 디버그할 수 있다는 것입니다.

에뮬레이터, 가상 컴퓨터 또는 별도의 워크스테이션에서 클라이언트를 실행하여 모바일 서비스를 로컬에서 테스트할 수 있으려면 워크스테이션의 IP 주소 및 포트에 연결할 수 있도록 로컬 웹 서버와 호스트 컴퓨터를 구성해야 합니다. 이 항목에서는 로컬에서 호스트한 모바일 서비스에 연결할 수 있도록 IIS Express를 구성하는 방법을 보여 줍니다.

[AZURE.INCLUDE [mobile-services-how-to-configure-iis-express](../../includes/mobile-services-how-to-configure-iis-express.md)]

 

<!---HONumber=July15_HO4-->