<properties 
   pageTitle="Visual Studio에서 연결된 서비스를 사용하여 Azure 저장소 추가 | Microsoft Azure"
   description="Visual Studio 연결된 서비스 추가 대화 상자를 사용하여 Azure 저장소를 앱에 추가"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# Visual Studio 연결 서비스를 사용하여 Azure 저장소 추가

## 개요

Visual Studio 2015에서 **연결된 서비스 추가** 대화 상자를 사용하여 C# 클라우드 서비스, .NET 백 엔드 모바일 서비스, ASP.NET 웹 사이트 또는 서비스, ASP.NET 5 서비스 또는 Azure WebJob 서비스를 Azure 저장소에 연결할 수 있습니다. 연결된 서비스 기능은 필요한 모든 참조와 연결 코드를 추가하고 구성 파일을 적절하게 수정합니다. 대화 상자를 통해 blob 저장소, 큐 및 테이블을 시작하는 다음 단계를 알려주는 설명서로 이동합니다.

## 지원되는 프로젝트 형식

다음 프로젝트 형식으로 Azure 저장소에 연결하려면 연결된 서비스 대화 상자를 사용할 수 있습니다.

- ASP.NET 웹 프로젝트

- ASP.NET 5 프로젝트

- .NET 클라우드 서비스 웹 역할 및 작업자 역할 프로젝트

- .NET 모바일 서비스 프로젝트

- Azure WebJob 프로젝트


## 연결된 서비스 대화 상자를 사용하여 Azure 저장소에 연결

1. Azure 계정이 있어야 합니다. Azure 계정이 없으면 [무료 평가판](http://go.microsoft.com/fwlink/?LinkId=518146)에 등록할 수 있습니다. Azure 계정이 있으면 저장소 계정을 만들고, 모바일 서비스를 작성하고, Azure Active Directory를 구성할 수 있습니다.

1. Visual Studio에서 프로젝트를 열고, 솔루션 탐색기에서 **참조** 노드의 상황에 맞는 메뉴를 연 다음 **연결된 서비스 추가**를 선택합니다.

    ![연결된 서비스 추가](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. **연결 서비스 추가** 대화 상자에서 **Azure 저장소**를 선택한 다음 **구성** 단추를 선택합니다. 아직 수행하지 않은 경우 Azure에 로그인하라는 메시지가 표시될 수 있습니다.

    ![연결된 서비스 추가 대화 상자 - 저장소](./media/vs-azure-tools-connected-services-storage/IC796703.png)

1. **Azure 저장소** 대화 상자에서, 기존 저장소 계정을 선택한 다음 **추가**를 선택합니다.

    새 저장소 계정을 만들어야 하는 경우 다음 단계로 이동합니다. 그렇지 않은 경우, 6단계로 건너뜁니다.

    ![Azure 저장소 대화 상자](./media/vs-azure-tools-connected-services-storage/IC796704.png)

1. 새 저장소 계정 만들기:

    1. Azure 저장소 대화 상자 하단에 있는 **새 저장소 계정 만들기** 단추를 선택합니다.

    1. **저장소 계정 만들기** 대화 상자를 채운 다음 **만들기** 단추를 선택합니다.
    
        ![Azure 저장소 대화 상자](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)

        **Azure 저장소** 대화 상자로 돌아가면 새 저장소가 목록에 나타납니다.

    1. 목록에서 새 저장소를 선택하고 **추가**를 선택합니다.

1. 연결된 저장소 서비스가 WebJob 프로젝트의 서비스 참조 노드 아래에 나타납니다.

    ![웹 작업 프로젝트에서 Azure 저장소](./media/vs-azure-tools-connected-services-storage/IC796705.png)

1. 표시되는 시작 페이지를 검토하고 프로젝트를 수정하는 방법에 대해 알아봅니다. 연결된 서비스를 추가할 때마다 시작 페이지가 브라우저에 나타납니다. 제안된 다음 단계 및 코드 예제를 검토하거나 변경된 내용 페이지로 전환하여 프로젝트에 추가된 참조 및 코드와 구성 파일이 수정된 방법을 볼 수 있습니다.

## 프로젝트를 수정하는 방법

대화 상자를 완료하면 Visual Studio는 참조를 추가하고 특정 구성 파일을 수정합니다. 특정 변경 내용은 프로젝트 형식에 따라 달라집니다.

 - ASP.NET 프로젝트는 [변경된 내용 – ASP.NET 프로젝트](http://go.microsoft.com/fwlink/p/?LinkId=513126)를 참조하세요.
 - ASP.NET 5 프로젝트는 [변경된 내용 – ASP.NET 5 프로젝트](http://go.microsoft.com/fwlink/p/?LinkId=513124)를 참조하세요.
 - 클라우드 서비스 프로젝트(웹 역할 및 작업자 역할)는 [변경된 내용 – 클라우드 서비스 프로젝트](http://go.microsoft.com/fwlink/p/?LinkId=516965)를 참조하세요.
 - WebJob 프로젝트는 [변경된 내용 -WebJob 프로젝트](./storage/vs-storage-webjobs-what-happened.md)를 참조하세요.

## 다음 단계

1. 시작 코드 샘플을 가이드로 사용하여 원하는 저장소 유형을 만든 다음 저장소 계정에 액세스하는 코드 작성을 시작합니다!

1. 질문하고 도움 받기
     - [MSDN 포럼: Azure 저장소](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)

     - [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/)

     - [azure.microsoft.com의 저장소](https://azure.microsoft.com/services/storage/)

     - [azure.microsoft.com의 저장소 설명서](https://azure.microsoft.com/documentation/services/storage/)

<!---HONumber=AcomDC_0817_2016-->