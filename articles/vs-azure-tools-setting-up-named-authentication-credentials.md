<properties
   pageTitle="명명된 인증 자격 증명 설정 | Microsoft Azure"
   description="Visual Studio가 사용할 수 있는 자격 증명을 제공하여 Azure에 대한 요청을 인증하고 응용 프로그램을 Visual Studio에서 Azure로 게시하거나 기존 클라우드 서비스를 모니터링하는 방법에 대해 알아봅니다."
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
   ms.date="05/08/2016"
   ms.author="tarcher" />

# 명명된 인증 자격 증명 설정

Visual Studio에서 Azure에 응용 프로그램을 게시하거나 기존 클라우드 서비스를 모니터링하려면 Visual Studio에서 Azure에 대한 요청을 인증하는 데 사용할 수 있는 자격 증명을 제공해야 합니다. 이러한 자격 증명을 제공하기 위해 로그인 할 수 있는 Visual Studio의 여러 위치 사항이 있습니다. 예를 들어 서버 탐색기에서 **Azure** 노드에 대한 바로 가기 메뉴를 열고 **Azure에 연결**을 선택할 수 있습니다. 로그인 할 때 Azure 계정에 연결된 구독 정보를 Visual Studio에서 사용할 수 있으며 아무 작업도 수행할 필요가 없습니다.

또한 Azure 도구는 구독 파일(.publishsettings 파일)을 사용하 여 자격 증명을 제공하는 이전 방법을 지원합니다. 이 항목에서는 Azure SDK 2.2에서 여전히 지원되는 이 메서드를 설명합니다.

Azure에 인증하려면 다음 항목이 필요합니다.

- 구독 ID

- 유효한 X.509 v3 인증서

>[AZURE.NOTE] X.509 v3 인증서 키의 길이는 2048비트 이상이어야 합니다. Azure는 이 요구 사항을 충족하지 않거나 올바르지 않은 모든 인증서를 거부합니다.

Visual Studio는 자격 증명으로 인증서 데이터와 함께 구독 ID를 사용합니다. 적절한 자격 증명은 인증서에 대한 공용 키가 포함된 구독 파일(.publishsettings 파일)에서 참조됩니다. 구독 파일에는 둘 이상의 구독에 대한 자격 증명이 포함될 수 있습니다.

이 항목의 뒷부분에 설명된 대로 **구독 새로 만들기/편집** 대화 상자에서 구독 정보를 편집할 수 있습니다.

인증서를 만들 려는 경우 [Azure용 관리 인증서 만들기 및 업로드](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx)에서 지침을 참조한 다음 [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)에 수동으로 인증서를 업로드합니다.

>[AZURE.NOTE] Visual Studio에서 클라우드 서비스를 관리해야 하는 이러한 자격 증명은 Azure 저장소 서비스에 대한 요청을 인증하는데 필요한 자격 증명과 동일하지 않습니다.

## Visual Studio에서 인증 자격 증명 수정 또는 내보내기

또한 다음 동작 중 하나를 수행하는 경우 나타나는 **새 구독** 대화 상자에서 인증 자격 증명을 설정, 수정 또는 내보낼 수 있습니다.

- **서버 탐색기**에서 **Azure** 노드에 대한 바로 가기 메뉴를 열어 **구독 관리**를 선택하고, **인증서** 탭을 선택한 다음 **새로 만들기** 또는 **편집** 단추를 선택합니다.

- **Azure 응용 프로그램 게시** 마법사에서 Azure 클라우드 서비스를 게시하는 경우 **구독 선택** 목록에서 **관리**를 선택하고 인증서 탭을 선택한 다음 **새로 만들기** 또는 **편집** 단추를 선택합니다.

다음 절차는 **새 구독** 대화 상자가 열려 있다고 가정합니다.

### Visual Studio에서 인증 자격 증명 설정

1. 인증 목록에 대한 **기존 인증서 선택**에서 인증서를 선택합니다.

1. **전체 경로 복사** 단추를 선택합니다. 인증서(.cer 파일)에 대한 경로가 클립보드에 복사됩니다.

    >[AZURE.IMPORTANT] Visual Studio에서 Azure 응용 프로그램을 게시하려면 [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)에 이 인증서를 업로드해야 합니다.

1. [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)에 인증서를 업로드하려면:

    1. Azure 포털 링크를 선택합니다.

         [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)이 열립니다.

    1. [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)에 로그인한 다음 **클라우드 서비스** 단추를 선택합니다.

    1. 관심 있는 클라우드 서비스를 선택합니다.

        해당 서비스에 대한 페이지가 열립니다.

    1. **인증서** 탭에서 **업로드** 단추를 선택합니다.

    1. 방금 만든 .cer 파일의 전체 경로를 붙여넣은 다음 지정한 암호를 입력합니다.

<!---HONumber=AcomDC_0511_2016-->