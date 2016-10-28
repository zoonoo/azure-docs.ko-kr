<properties 
   pageTitle="Azure 응용 프로그램 게시 마법사 | Microsoft Azure"
   description="Azure 응용 프로그램 게시 마법사"
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

# Azure 응용 프로그램 게시 마법사

## 개요

Visual Studio에서, 웹 응용 프로그램을 개발한 후, **Azure 응용 프로그램 게시** 마법사를 사용하여 해당 응용 프로그램을 Azure 클라우드 서비스에 쉽게 게시할 수 있습니다. 첫 번째 섹션에서는 마법사를 사용하기 전에 완료해야 하는 단계를 설명하며, 나머지 섹션에서는 마법사의 기능을 설명합니다.

>[AZURE.NOTE] 이 토픽은 웹 사이트가 아닌 클라우드 서비스에 배포에 대한 것입니다. 웹 사이트에 배포에 대한 자세한 내용은 [Azure 웹 사이트에 배포하는 방법](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false)을 참조하세요.

## 필수 조건

Azure에 웹 응용 프로그램을 게시하기 전에 Azure 구독과 Microsoft 계정이 있어야 하고 Azure 클라우드 서비스와 웹 응용 프로그램을 연결해야 합니다. 이미 이 작업을 완료한 경우 다음 섹션으로 건너뛸 수 있습니다.

1. Microsoft 계정과 Azure 구독을 가져옵니다. 1개월 무료 Azure 구독을 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 시도할 수 있습니다.

1. Azure에서 클라우드 서비스 및 저장소 계정을 만듭니다. Visual Studio의 서버 탐색기에서 또는 [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)을 사용하여 수행할 수 있습니다.

1. Azure용 웹 응용 프로그램을 사용합니다. 웹 응용 프로그램을 사용하여 Visual Studio에서 Azure에 게시하려면, Visual Studio에서 Azure 클라우드 서비스 프로젝트와 연결해야 합니다. 연관된 클라우드 서비스 프로젝트를 만드려면, 웹 응용 프로그램용 프로젝트에 대 한 바로 가기 메뉴를 열고 **Azure 클라우드 서비스 프로젝트로 변환**을 선택합니다.

1. 클라우드 서비스 프로젝트가 솔루션에 추가된 후, 동일한 바로 가기 메뉴를 다시 열고 **게시**를 선택합니다. Azure 용 응용 프로그램을 사용하는 방법에 대한 자세한 내용은 [방법: Visual Studio에서 Azure 클라우드 서비스에 웹 응용 프로그램 마이그레이션 및 게시](https://msdn.microsoft.com/library/azure/hh420322.aspx)를 참조하세요.

>[AZURE.NOTE] 관리자 자격 증명(관리자 권한으로 실행)으로 Visual Studio를 시작해야 합니다.

1. 응용 프로그램을 게시할 준비가 되면 Azure 클라우드 서비스 프로젝트에 대한 바로 가기 메뉴를 열고 **게시**를 선택합니다. 다음 단계에서는 Azure 응용 프로그램 게시 마법사를 보여줍니다.

## 구독 선택

### 구독을 선택하려면

1. 처음으로 마법사를 사용하기 전에 로그인해야 합니다. **로그인** 링크를 선택합니다. 메시지가 표시되면 Azure 포털에 로그인하고 Azure 사용자 이름 및 암호를 제공합니다.

    ![다음은 게시 마법사 화면 중 하나입니다.](./media/vs-azure-tools-publish-azure-application-wizard/IC799159.png)

    구독 목록이 계정과 연결된 구독으로 채워집니다. 또한 사용자가 이전에 가져온 모든 구독 파일에서 구독을 볼 수 있습니다.

1. **구독 선택** 목록에서 이 배포에 사용할 구독을 선택합니다.

   **<관리...>**를 선택하면, **구독 관리** 대화 상자가 나타나고, 사용하려는 구독 및 사용자 계정을 선택할 수 있습니다. **계정** 탭은 모든 사용자 계정을 표시하며 **구독** 탭은 계정과 관련된 모든 구독을 표시합니다. 또한 Azure 리소스를 사용할 뿐만 아니라 Azure 포털에서 구독에 대한 인증서를 만들거나 가져올 지역을 선택할 수도 있습니다. 구독 파일에서 구독을 가져온 경우 연결 된 인증서는 **인증서** 탭에 표시됩니다. 완료되면 **닫기** 단추를 선택합니다.

    ![Manage subscriptions](./media/vs-azure-tools-publish-azure-application-wizard/IC799160.png)

    >[AZURE.NOTE] A subscription file can contain more than one subscription.

1. **다음** 단추를 선택하여 계속합니다.

    구독에 모든 클라우드 서비스가 없는 경우, 프로젝트를 호스팅하는 클라우드 서비스를 Azure에서 만들어야 합니다. **클라우드 서비스 및 저장소 계정 만들기** 대화 상자가 나타납니다.

    클라우드 서비스의 새 이름을 지정합니다. 이름은 Azure에서 고유해야 합니다. 그런 다음 사용자 또는 대부분의 클라이언트에 가까운 데이터 센터의 지역 또는 선호도 그룹을 지정합니다. 이 이름은 Azure가 클라우드 서비스에 대해 만든 새 저장소 계정에 대해서도 사용됩니다.

1. 이 배포에 대해 원하는 수정을 수정한 다음 **게시** 단추를 선택하여 게시합니다(다음 섹션은 다양한 설정에 대한 자세한 정보를 제공합니다.). 게시하기 전에 설정을 검토하려면 **다음** 단추를 선택합니다.

    >[AZURE.NOTE] 이 단계에서 게시를 선택한 경우, Visual Studio에서이 배포의 상태를 모니터링할 수 있습니다.

**Azure 응용 프로그램 게시** 마법사를 사용하여 배포에 대한 일반 및 고급 설정 모두를 수정할 수 있습니다. 예를 들어, 릴리스하기 전에 테스트 환경에 응용 프로그램을 배포하는 설정을 선택할 수 있습니다. 다음 그림에서는 Azure 배포에 대한 **일반 설정** 탭을 표시합니다.

![일반 설정](./media/vs-azure-tools-publish-azure-application-wizard/IC749013.png)

## 게시 설정 구성

### 게시 설정을 구성하려면

1. **클라우드 서비스** 목록에서 다음 단계 중 하나를 수행합니다.

   1. 드롭다운 목록 상자에서 기존 클라우드 서비스를 선택합니다. 서비스에 대한 데이터 센터 위치가 표시됩니다. 이 위치를 확인하고 저장소 계정 위치가 동일한 데이터 센터에 있는지 확인해야 합니다.

    1. **새로 만들기**를 선택하고 Azure가 호스트하는 클라우드 서비스를 만듭니다. **클라우드 서비스 만들기** 대화 상자에서 서비스에 대한 이름을 입력한 다음 지역 또는 선호도 그룹을 지정하여 이 클라우드 서비스를 호스트할 데이터 센터의 위치를 지정합니다. 이름은 Azure에서 고유해야 합니다.

1. **환경** 목록에서 **프로덕션** 또는 **준비**를 선택합니다. 테스트 환경에 응용 프로그램을 배포하려는 경우 스테이징 환경을 선택합니다. 나중에 응용 프로그램을 프로덕션 환경으로 이동할 수 있습니다.

1. **빌드 구성** 목록에서 **디버그** 또는 **릴리스**를 선택합니다.

1. **서비스 구성** 목록에서 **클라우드** 또는 **로컬**을 선택합니다.

    서비스에 원격으로 연결하려면 **모든 역할에 대한 원격 데스크톱 사용** 확인란을 선택합니다. 이 옵션은 주로 문제 해결을 위해 사용됩니다. 이 확인란을 선택하는 경우 **원격 데스크톱 구성** 대화 상자가 나타납니다. 구성을 변경하려면 설정 링크를 선택합니다.

    서비스에 대한 웹 배포를 사용하려면 **모든 웹 역할에 대해 웹 배포 사용** 확인란을 선택합니다. 이 기능을 사용하여 원격 데스크톱을 설정해야 합니다. 자세한 내용은 [[Azure Tools를 사용하여 클라우드 서비스 게시](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx)를 참조하세요. 웹 배포에 대한 자세한 내용은 [[Azure Tools를 사용하여 클라우드 서비스 게시](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx)를 참조하세요.

1. **고급 설정** 탭을 선택합니다. **배포 레이블** 필드에서, 기본 이름을 그대로 사용 하거나 사용자가 선택한 이름을 입력합니다. 배포 레이블에 날짜를 추가하려면 확인란을 선택한 상태로 둡니다.

    ![게시 마법사의 세 번째 화면](./media/vs-azure-tools-publish-azure-application-wizard/IC749014.png)

1. **저장소 계정** 목록에서 이 배포에 사용할 저장소 계정을 선택합니다. 클라우드 서비스 및 저장소 계정에 대한 데이터 센터의 위치를 비교합니다. 이상적으로 이 위치는 같아야 합니다.

    >[AZURE.NOTE] Azure 저장소 계정은 응용 프로그램 배포용 패키지를 저장합니다. 응용 프로그램이 배포된 후 패키지는 저장소 계정에서 제거됩니다.

1. 업데이트된 구성 요소만 배포하려는 경우 **배포 업데이트** 확인란을 선택합니다. 이 유형의 배포는 전체 배포보다 빠를 수 있습니다. **설정** 링크를 선택하여 다음 그림에 표시된 **배포 업데이트 설정** 대화 상자를 엽니다.

    ![배포 설정](./media/vs-azure-tools-publish-azure-application-wizard/IC617060.png)

    업데이트 배포에 대한 두 옵션, 증분 또는 동시 중 하나를 선택할 수 있습니다. 증분 배포는 배포된 하나의 인스턴스를 한 번에 업데이트하므로 응용 프로그램이 온라인 상태로 남아 사용자가 사용할 수 있습니다. 동시 배포는 배포된 모든 인스턴스를 동시에 업데이트합니다. 동시 업데이트는 증분 업데이트보다 빠르지만 이 옵션을 선택하는 경우 업데이트 과정 중에는 응용 프로그램을 사용할 수 없습니다.

    배포를 업데이트할 수 없는 경우 이 확인란을 선택해야 하며, 업데이트 배포가 실패한 경우 전체 배포가 자동으로 수행되도록 하려면 전체 배포를 선택해야 합니다. 전체 배포는 클라우드 서비스에 대한 가상 IP(VIP) 주소를 다시 설정합니다. 자세한 내용은 [방법: 클라우드 서비스에 대한 일정한 가상 IP 주소 유지](https://msdn.microsoft.com/library/azure/jj614593.aspx)를 참조하세요.


1. 서비스를 디버깅하려면 **IntelliTrace 사용** 확인란을 선택하거나 **디버그** 구성을 배포하고 Azure에서 클라우드 서비스를 디버깅하려는 경우 **모든 역할에 대해 원격 디버거 사용** 확인란을 선택하여 원격 디버깅 서비스를 배포합니다.

2. 응용 프로그램을 프로파일링하려면 **프로파일링 사용** 확인란을 선택한 다음 **설정** 링크를 선택하여 프로파일링 옵션을 표시합니다.


    >[AZURE.NOTE] Visual Studio Ultimate에서 IntelliTrace 또는 계층 상호작용 프로파일링(TIP)를 사용하도록 설정해야 하며 동시에 모두 사용할 수 없습니다.

    자세한 내용은 [IntelliTrace 및 Visual Studio를 사용하여 게시된 클라우드 서비스 디버깅](https://msdn.microsoft.com/library/azure/ff683671.aspx) 및 [클라우드 서비스의 성능 테스트](https://msdn.microsoft.com/library/azure/hh369930.aspx)를 참조하세요.

1. **다음**을 선택하여 응용 프로그램에 대한 요약 페이지를 볼 수 있습니다.

## 응용 프로그램 게시

1. 선택한 설정에서 게시 프로필을 만들 수 있습니다. 예를 들어, 테스트 환경에 대한 하나의 프로필을 만들고 프로덕션용으로 다른 하나를 만들 수 있습니다. 이 프로필을 저장하려면 **저장** 아이콘을 선택합니다. 마법사는 프로필을 만들고 Visual Studio 프로젝트에 저장합니다. 프로필 이름을 수정하려면 **대상 프로필** 목록을 연 다음 **<관리...>**를 선택합니다.

    ![게시 마법사의 요약 화면](./media/vs-azure-tools-publish-azure-application-wizard/IC749015.png)

    >[AZURE.NOTE] 게시 프로필은 Visual Studio의 솔루션 탐색기에 나타나며 프로필 설정은 확장명이 .azurePubxml인 파일로 기록됩니다. 설정은 XML 태그의 특성으로 저장됩니다.

1. **게시**를 선택하여 응용 프로그램을 게시합니다. Visual Studio의 **출력** 창에서 프로세스 상태를 모니터링할 수 있습니다.

## 참고 항목

[방법: Visual Studio에서 Azure 클라우드 서비스로 웹 응용 프로그램 마이그레이션 및 게시](https://msdn.microsoft.com/library/azure/hh420322.aspx)

[Azure Tools를 사용하여 클라우드 서비스 게시](https://msdn.microsoft.com/library/azure/ff683672.aspx)

[IntelliTrace 및 Visual Studio를 사용하여 게시된 클라우드 서비스 디버깅](https://msdn.microsoft.com/library/azure/ff683671.aspx)

[클라우드 서비스의 성능 테스트](https://msdn.microsoft.com/library/azure/hh369930.aspx)

<!---HONumber=AcomDC_0817_2016-->