<properties
   pageTitle="Visual Studio를 사용하여 Azure 프로젝트 만들기 | Microsoft Azure"
   description="Visual Studio를 사용하여 Azure 프로젝트 만들기 "
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# Visual Studio를 사용하여 Azure 프로젝트 만들기

Azure Tools for Visual Studio는 템플릿을 제공하여 Azure에 대한 클라우드 서비스를 만들 수 있습니다. 또한 이 도구는 Azure에 클라우드 서비스를 구성, 디버깅 및 배포하는 데 도움이 됩니다.

Azure 클라우드 서비스 솔루션에는 다음 형식의 프로젝트가 포함됩니다.

- **Azure 프로젝트**

    Azure 프로젝트에는 솔루션의 역할 프로젝트에 대한 연결이 있습니다. 서비스 정의 및 서비스 구성 파일도 포함됩니다. 서비스 정의 파일은 필요한 역할, 끝점 및 가상 컴퓨터 크기를 포함하여 응용 프로그램에 대한 런타임 설정을 정의합니다. 서비스 구성 파일은 얼마나 많은 역할의 인스턴스가 실행되는지와 역할에 대해 정의된 설정의 값을 구성합니다. 이러한 설정에 대한 자세한 내용은 [방법: Visual Studio를 사용하여 Azure 클라우드 서비스에 대한 역할 구성](vs-azure-tools-configure-roles-for-cloud-service.md)을 참조하세요.

- **웹 역할 프로젝트**

    작업자 역할은 백그라운드 처리를 수행합니다. 작업자 역할은 저장소 서비스 및 다른 인터넷 기반 서비스와 통신할 수 있습니다. 작업자 역할에는 임의 개수의 HTTP, HTTPS 또는 TCP 끝점이 있을 수 있습니다.

    - **ASP.NET 웹 역할**: 웹 프런트엔드로 ASP.NET 응용 프로그램을 구축
    - **ASP.NET MVC5 웹 역할**
    - **ASP.NET MVC4 웹 역할**
    - **ASP.NET MVC3 웹 역할**
    - **WCF 서비스 웹 역할**: WCF 서비스를 구축
    - **Silverlight 비즈니스 응용 프로그램 웹 역할** (Visual Studio 2012 필요)

- **캐시 작업자 역할**

    응용 프로그램에 전용된 캐시를 제공하는 역할입니다.

- **서비스 버스 큐가 있는 작업자 역할**

    작업자 프로세스와 통신하는 메시지 큐 기능을 제공하는 서비스 버스 큐. 자세한 내용은 [서비스 버스 큐를 사용하는 방법](http://go.microsoft.com/fwlink/?LinkId=260560)을 참조하십시오.

## Visual Studio에서 Azure 클라우드 서비스 프로젝트를 만들려면

1. 관리자 권한으로 Microsoft Visual Studio를 시작합니다.

1. 주 메뉴에서 **파일**, **새로 만들기**, **프로젝트**를 선택합니다.

1. **프로젝트 형식** 창의 Visual C# 또는 Visual Basic 프로젝트 템플릿 노드에서 **클라우드**를 선택합니다.

1. **템플릿** 창에서 **Azure 클라우드 서비스**를 선택합니다.

1. 프로젝트 개발에 사용하려는 .NET Framework의 버전을 지정합니다.

1. 프로젝트에 대한 이름과 위치 및 솔루션에 대한 이름을 입력합니다. **확인** 단추를 선택합니다.

1. **새 Azure 프로젝트** 대화 상자에서 추가 하려는 역할을 선택하고 오른쪽 화살표 단추를 선택하여 솔루션에 역할을 추가 합니다. 필요한 만큼의 역할을 추가 할 수 있습니다.

1. 로젝트에 추가한 역할의 이름을 바꾸려면 **새 Azure 프로젝트** 대화 상자의 역할에 마우스 포인터를 가리키고 역할의 오른쪽에 있는 **이름바꾸기** 아이콘을 선택합니다. 또한 추가 된 후 솔루션 내에서 역할의 이름을 바꿀 수 있습니다.

<!---HONumber=AcomDC_0817_2016-->