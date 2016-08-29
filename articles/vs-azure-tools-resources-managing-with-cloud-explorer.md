<properties 
   pageTitle="클라우드 탐색기를 사용하여 Azure 리소스 관리 | Microsoft Azure"
   description="클라우드 탐색기를 사용하여 Visual Studio 내에서 Azure 리소스를 검색 및 관리하는 방법에 대해 알아봅니다."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# 클라우드 탐색기를 사용하여 Azure 리소스 관리

##개요

클라우드 탐색기는 Visual Studio IDE 내에서 Azure 리소스를 보다 쉽고 빠르게 찾아보고 관리할 수 있도록 설계되었습니다. 예를 들어, [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040) 또는 브라우저에서 웹앱을 열거나, 디버거를 연결하거나 Blob 컨테이너의 속성을 볼 수 있으며 Blob 컨테이너 편집기에서 열 수 있습니다.

[Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에서와 마찬가지로 Azure Resource Manager 스택에 클라우드 탐색기가 빌드됩니다. Azure 리소스 그룹과 같은 리소스 및 논리 앱과 API 앱과 같은 Azure 서비스를 이해하고 [역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)(RBAC)를 지원합니다. 추가되거나 변경된 Azure 리소스를 보려면 클라우드 탐색기 도구 모음에서 **새로고침** 단추를 선택합니다.

클라우드 탐색기는 Azure SDK용 Visual Studio Tools 2.7의 일부로 설치됩니다.

## 필수 조건

- Visual Studio 2015 RTM.

- Azure SDK용 Visual Studio 도구입니다.
- 클라우드 탐색기에서 Azure 리소스를 보려면 Azure 계정이 있고 로그인되어 있어야 합니다. 없는 경우에는 몇 분 만에 계정을 만들 수 있습니다. MSDN 구독이 있는 경우 [MSDN 구독자를 위한 Azure 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요. 그렇지 않으면 [무료 평가판 계정 만들기](https://azure.microsoft.com/pricing/free-trial/)를 참조하세요.

- 클라우드 탐색기가 표시되지 않는 경우 메뉴 모음에서 **보기**, **다른 창**, **클라우드 탐색기**를 선택하여 표시할 수 있습니다.

## Azure 계정 및 구독 관리

클라우드 탐색기에서 Azure 리소스를 보려면 하나 이상의 구독이 활성화된 Azure 계정에 로그인해야 합니다. Azure 계정이 둘 이상 있으면 클라우드 탐색기에서 추가한 다음 클라우드 탐색기 리소스 보기에 포함하려는 구독을 선택합니다.

이전에 Azure를 사용하지 않았거나 Visual Studio에 필요한 계정을 추가하지 않은 경우, 추가하라는 메시지가 표시됩니다.

## Azure 계정을 클라우드 탐색기에 추가하려면

1. 클라우드 탐색기 도구 모음에서 설정 아이콘을 선택합니다.

1. **계정 추가** 링크를 선택합니다. 검색하려는 해당 리소스의 Azure 계정에 로그인합니다. 방금 추가한 계정을 계정 선택 드롭다운 목록에서 선택해야 합니다. 해당 계정에 대한 구독은 계정 항목 아래에 나타납니다.

    ![Azure 구독 추가](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819514.png)

    ![Azure 구독 선택](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819515.png)

1. 탐색할 계정 구독에 대한 확인란을 선택한 다음 **적용** 단추를 선택합니다.

    선택한 구독에 대한 Azure 리소스는 클라우드 탐색기에 나타납니다.

## Azure 계정을 제거하려면

1. 메뉴 모음에서 **파일**, **계정 설정**을 선택합니다.

1. **계정 설정** 대화 상자의 **모든 계정** 섹션에서, 제거하려는 계정 옆에 있는 **제거** 명령을 선택합니다. 이 명령은 Visual Studio에서 계정만을 제거합니다. Azure 계정 자체에만 영향을 주지 않습니다.

## 리소스 유형 또는 그룹 보기

Azure 리소스를 보려면 **리소스 종류** 또는 **리소스 그룹** 보기를 선택할 수 있습니다.

![리소스 보기 드롭다운](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819516.png)

- **리소스 종류** 보기는 [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에서 사용되는 일반적인 보기로, 웹앱, 저장소 계정 및 가상 컴퓨터와 같은 형식으로 분류된 Azure 리소스를 보여 줍니다. Azure 리소스가 서버 탐색기에 나타나는 방법과 유사합니다.

- 리소스 그룹 보기는 연관된 Azure 리소스 그룹으로 Azure 리소스를 분류합니다.

 
	리소스 그룹은 일반적으로 특정 응용 프로그램에서 사용되는 Azure 리소스 번들입니다. Azure 리소스 그룹에 대한 자세한 내용은 [Azure 리소스 관리자 개요](./resource-group-overview.md)를 참조하세요.

## 리소스 보기 및 탐색

클라우드 탐색기에서 Azure 리소스를 탐색하고 정보를 보려면, 항목의 형식 또는 연결된 리소스 그룹을 확장한 다음 리소스를 선택합니다. 리소스를 선택하면 클라우드 탐색기의 아래쪽의 두 탭에 정보가 나타납니다.

![리소스 뷰 선택](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819517.png)

- **작업** 탭은 선택한 리소스에 대해 클라우드 탐색기에서 수행할 수 있는 동작을 표시합니다. 리소스의 바로 가기 메뉴에서 사용할 수 있는 작업을 볼 수도 있습니다.

- **속성** 탭은 해당 유형, 로캘 및 연관된 리소스 그룹과 같은 리소스의 속성을 표시합니다.

모든 리소스에는 **포털에서 열기** 작업이 있습니다. 이 작업을 선택하면 클라우드 탐색기는 [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에서 선택한 리소스를 표시합니다. 이 기능은 여러 층으로 중첩된 리소스를 탐색하기에 더욱 간편합니다.

추가 작업 및 속성 값은 Azure 리소스에 따라 다르게 나타날 수도 있습니다. 예를 들어 웹앱 및 논리 앱에는 **포털에서 열기** 외에 **브라우저에서 열기** 및 **디버거 연결** 작업도 있습니다. 저장소 계정 blob, 큐 또는 테이블을 선택하면 편집기를 열려는 작업이 나타납니다. Azure 앱에는 **URL** 및 **상태** 속성이 있으며, 저장소 리소스에는 키와 연결 문자열 속성이 있습니다.

## 리소스 검색

Azure 계정 구독에서 특정 이름의 리소스를 찾으려면, 클라우드 탐색기의 검색 상자에 이름을 입력합니다.

![클라우드 탐색기에서 리소스 찾기](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC820394.png)

검색 상자에 문자를 입력하면 해당 문자와 일치하는 리소스만 리소스 트리에 나타납니다.

<!---HONumber=AcomDC_0817_2016-->