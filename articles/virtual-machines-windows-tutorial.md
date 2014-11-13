<properties urlDisplayName="Create a virtual machine" pageTitle="Azure에서 Windows를 실행하는 가상 컴퓨터 만들기" metaKeywords="Azure capture image vm, capturing vm" description="Azure에서 Windows VM(가상 컴퓨터)을 만든 다음 로그온하고 데이터 디스크를 연결하는 방법에 대해 알아봅니다." metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="kathydav, rasquill" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/12/2014" ms.author="kathydav" />

# Windows를 실행하는 가상 컴퓨터 만들기

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/virtual-machines-windows-tutorial/" title="Azure 포털" class="current">Azure 포털</a><a href="/ko-kr/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure 미리 보기 포털">Azure 미리 보기 포털</a></div>

이 자습서에서는 Azure 관리 포털의 이미지 갤러리에서 Windows Server 이미지를 예로 사용하여 Windows를 실행하는 Azure VM(가상 컴퓨터)를 쉽게 만드는 방법을 보여 줍니다. 이미지 갤러리는 Windows 운영 체제, Linux 기반 운영 체제, 응용 프로그램 이미지 등 다양한 이미지를 제공합니다.

> [WACOM.NOTE] 이전에 Azure VM을 사용한 경험이 없어도 이 자습서를 완료할 수 있습니다. 그러나 Azure 계정은 필요합니다. 몇 분 만에 무료 체험 계정을 만들 수 있습니다. 자세한 내용은 [Azure 계정 만들기][Azure 계정 만들기](영문)를 참조하세요.

이 자습서에서는 다음을 보여 줍니다.

-   [가상 컴퓨터를 만드는 방법][가상 컴퓨터를 만드는 방법]
-   [가상 컴퓨터를 만든 후 로그온하는 방법][가상 컴퓨터를 만든 후 로그온하는 방법]
-   [새 가상 컴퓨터에 데이터 디스크를 연결하는 방법][새 가상 컴퓨터에 데이터 디스크를 연결하는 방법]

더 자세히 알고 싶으면 [가상 컴퓨터][가상 컴퓨터]를 참조하세요.

## <span id="createvirtualmachine"></span> </a>가상 컴퓨터를 만드는 방법

이 섹션에서는 관리 포털의 **갤러리에서** 방법을 사용하여 가상 컴퓨터를 만드는 방법을 보여 줍니다. 이 옵션은 **빠른 생성** 옵션보다 많은 구성 선택 항목을 제공합니다. 예를 들어 가상 컴퓨터를 가상 네트워크에 가입하려는 경우 **갤러리에서** 옵션을 사용해야 합니다.

> [WACOM.NOTE] 갤러리에서 사용 가능한 이미지의 수와 종류는 보유 중인 구독의 유형에 따라 달라집니다. 이 자습서에서는 Windows Server 이미지를 사용하지만 MSDN 구독에는 데스크톱 이미지와 같은 추가 이미지가 제공될 수도 있습니다.

> 또한 보다 다양하며 사용자 지정 가능한 [Azure 미리 보기 포털][1]에서 가상 컴퓨터를 만들고, 여러 컴퓨터 응용 프로그램 템플릿의 배포를 자동화하고, 고급 VM 모니터링 및 진단 기능을 사용해 볼 수 있습니다. 두 포털에서 사용 가능한 VM 구성 옵션은 상당히 중복되지만 동일하지는 않습니다.

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <span id="logon"></span> </a>가상 컴퓨터를 만든 후 로그온하는 방법

이 섹션에서는 가상 컴퓨터에서 실행되는 설정 및 응용 프로그램을 관리할 수 있도록 가상 컴퓨터에 로그인하는 방법을 보여 줍니다.

[WACOM.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

## <span id="attachdisk"></span> </a>새 가상 컴퓨터에 데이터 디스크를 연결하는 방법

이 섹션에서는 가상 컴퓨터에 빈 데이터 디스크를 연결하는 방법을 보여 줍니다. 빈 디스크 연결 및 기존 디스크를 연결하는 방법에 대한 자세한 내용은 [데이터 디스크 연결 자습서][데이터 디스크 연결 자습서]를 참조하세요.

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.

2.  **가상 컴퓨터**를 클릭한 다음 **MyTestVM** 가상 컴퓨터를 선택합니다.

    ![MyTestVM 선택][MyTestVM 선택]

3.  먼저 빠른 시작 페이지로 이동될 수 있습니다. 이동하면 맨 위에서 **대시보드**를 선택합니다.

    ![대시보드 선택][대시보드 선택]

4.  명령 모음에서 **연결**을 클릭한 다음, 팝업이 표시되면 **빈 디스크 연결**을 클릭합니다.

    ![명령 모음에서 연결 선택][명령 모음에서 연결 선택]

5.  **Virtual Machine Name**, **Storage Location**, **File Name** 및 **Host Cache Preference**는 이미 정의되어 있습니다. 원하는 디스크 크기만 입력하면 됩니다. **Size** 필드에 **5**를 입력합니다. 확인 표시를 클릭하여 가상 컴퓨터에 빈 디스크를 연결합니다.

    > [WACOM.NOTE] Azure의 디스크 이미지는 Azure 저장소에 페이지 Blob로 저장됩니다. Azure 외부에서 가상 하드 디스크는 VHD 또는 VHDX 형식을 사용할 수 있습니다. 또한 고정, 동적 확장 또는 차이점 보관용 디스크일 수도 있습니다. Azure는 VHD 형식의 고정 디스크를 지원합니다. 고정 형식은 디스크 오프셋 X가 blob 오프셋 X에 저장되도록 파일 내에 선형적으로 논리적 디스크를 배치합니다. blob 끝의 작은 바닥글에서는 VHD의 속성을 설명합니다. 고정 형식은 대부분의 디스크에서 사용되지 않는 공간 범위가 크기 때문에 공간이 낭비되는 경우가 많습니다. 하지만 Azure에서는 .vhd 파일을 스파스 형식으로 저장하기 때문에 고정 및 동적 디스크의 장점을 동시에 얻을 수 있습니다. 여기에 대한 자세한 내용은 [Azure의 VHD 정보][Azure의 VHD 정보]에서 확인할 수 있습니다.

    ![빈 디스크의 크기 지정][빈 디스크의 크기 지정]

    > [WACOM.NOTE] 모든 디스크는 Windows Azure 저장소의 VHD 파일에서 만들어집니다. **파일 이름**에서 저장소에 추가되는 VHD 파일의 이름은 지정할 수 있지만 디스크 이름은 Azure에서 자동으로 생성합니다.

6.  대시보드로 돌아와 빈 데이터 디스크가 가상 컴퓨터에 연결되어 있는지 확인합니다. 이 디스크는 **디스크** 목록에서 OS 디스크와 함께 두 번째 디스크로 나열됩니다.

    ![빈 디스크 연결][빈 디스크 연결]

    데이터 디스크를 가상 컴퓨터에 연결한 후 디스크는 오프라인 상태이며 초기화되지 않았습니다. 가상 컴퓨터에 로그온한 다음 디스크를 초기화해야 데이터를 저장하는 데 사용할 수 있습니다.

7.  이전 섹션인 [가상 컴퓨터를 만든 후 로그온하는 방법][가상 컴퓨터를 만든 후 로그온하는 방법]의 단계에 따라 가상 컴퓨터에 연결합니다(\#logon).

8.  가상 컴퓨터에 로그온한 후 **Server Manager**를 엽니다. 왼쪽 창에서 **파일 및 저장소 서비스**를 선택합니다.

    ![서버 관리자에서 파일 및 저장소 서비스 확장][서버 관리자에서 파일 및 저장소 서비스 확장]

9.  확장된 메뉴에서 **디스크**를 선택합니다.

    ![서버 관리자에서 파일 및 저장소 서비스 확장][2]

10. **디스크** 섹션의 목록에는 디스크 0, 디스크 1, 디스크 2의 3개 디스크가 있습니다. 디스크 0은 OS 디스크이며, 디스크 1은 임시 리소스 디스크(데이터 저장소에 사용되지 않음), 디스크 2는 가상 컴퓨터에 연결한 데이터 디스크입니다. 데이터 디스크의 용량은 앞서 지정한 대로 5GB입니다. 디스크 2를 마우스 오른쪽 단추로 클릭한 다음 **초기화**를 선택합니다.

    ![초기화 시작][초기화 시작]

11. **예**를 클릭하여 초기화 프로세스를 시작합니다.

    ![초기화 계속][초기화 계속]

12. 디스크 2를 마우스 오른쪽 단추로 클릭하고 **새 볼륨**을 선택합니다.

    ![볼륨 만들기][볼륨 만들기]

13. 제공된 기본값을 사용하여 마법사를 완료합니다. 마법사를 완료하면 새 볼륨이 **볼륨** 섹션에 나열됩니다.

    ![볼륨 만들기][3]

    이제 디스크가 온라인 상태이며 새 드라이브 문자로 사용할 수 있습니다.

## 다음 단계

Azure에서 Windows 가상 컴퓨터를 구성하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

[클라우드 서비스에서 가상 컴퓨터를 연결하는 방법][클라우드 서비스에서 가상 컴퓨터를 연결하는 방법]

[Windows Server 운영 체제를 포함하는 가상 하드 디스크를 만들고 업로드하는 방법][Windows Server 운영 체제를 포함하는 가상 하드 디스크를 만들고 업로드하는 방법]

[가상 컴퓨터의 가용성 관리][가상 컴퓨터의 가용성 관리]

[Azure VM 구성 설정 정보][Azure VM 구성 설정 정보]

[비디오: VHD 시작 - 백그라운드 작업][비디오: VHD 시작 - 백그라운드 작업]

[비디오: Mark Russinovich의 FAQ 소개 - Microsoft Azure에서 Windows 실행][비디오: Mark Russinovich의 FAQ 소개 - Microsoft Azure에서 Windows 실행]

[비디오: 다시 사용할 수 있는 이미지를 만들어 웹 팜에 새 가상 컴퓨터 추가][비디오: 다시 사용할 수 있는 이미지를 만들어 웹 팜에 새 가상 컴퓨터 추가]

[비디오: 가상 하드 드라이브와 저장소 계정을 추가하고 가상 컴퓨터 크기 조정][비디오: 가상 하드 드라이브와 저장소 계정을 추가하고 가상 컴퓨터 크기 조정]

[비디오: Scott Guthrie의 가상 컴퓨터 소개][비디오: Scott Guthrie의 가상 컴퓨터 소개]

[비디오: Azure 가상 컴퓨터의 저장소 및 디스크 기본 사항][비디오: Azure 가상 컴퓨터의 저장소 및 디스크 기본 사항]

  [Azure 미리 보기 포털]: /ko-kr/documentation/articles/virtual-machines-windows-tutorial-azure-preview/ "Azure 미리 보기 포털"
  [Azure 계정 만들기]: http://www.windowsazure.com/ko-kr/develop/php/tutorials/create-a-windows-azure-account/
  [가상 컴퓨터를 만드는 방법]: #createvirtualmachine
  [가상 컴퓨터를 만든 후 로그온하는 방법]: #logon
  [새 가상 컴퓨터에 데이터 디스크를 연결하는 방법]: #attachdisk
  [가상 컴퓨터]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [1]: https://portal.azure.com
  [데이터 디스크 연결 자습서]: http://www.windowsazure.com/ko-kr/documentation/articles/storage-windows-attach-disk/
  [Azure 관리 포털]: http://manage.windowsazure.com
  [MyTestVM 선택]: ./media/virtual-machines-windows-tutorial/selectvm.png
  [대시보드 선택]: ./media/virtual-machines-windows-tutorial/dashboard.png
  [명령 모음에서 연결 선택]: ./media/virtual-machines-windows-tutorial/commandbarattach.png
  [Azure의 VHD 정보]: http://msdn.microsoft.com/ko-kr/library/azure/dn790344.aspx
  [빈 디스크의 크기 지정]: ./media/virtual-machines-windows-tutorial/emptydisksize.png
  [빈 디스크 연결]: ./media/virtual-machines-windows-tutorial/disklistwithdatadisk.png
  [서버 관리자에서 파일 및 저장소 서비스 확장]: ./media/virtual-machines-windows-tutorial/fileandstorageservices.png
  [2]: ./media/virtual-machines-windows-tutorial/selectdisks.png
  [초기화 시작]: ./media/virtual-machines-windows-tutorial/initializedisk.png
  [초기화 계속]: ./media/virtual-machines-windows-tutorial/yesinitialize.png
  [볼륨 만들기]: ./media/virtual-machines-windows-tutorial/initializediskvolume.png
  [3]: ./media/virtual-machines-windows-tutorial/newvolumecreated.png
  [클라우드 서비스에서 가상 컴퓨터를 연결하는 방법]: http://www.windowsazure.com/ko-kr/documentation/articles/cloud-services-connect-virtual-machine/
  [Windows Server 운영 체제를 포함하는 가상 하드 디스크를 만들고 업로드하는 방법]: http://www.windowsazure.com/ko-kr/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [가상 컴퓨터의 가용성 관리]: http://www.windowsazure.com/ko-kr/documentation/articles/manage-availability-virtual-machines/
  [Azure VM 구성 설정 정보]: http://msdn.microsoft.com/library/azure/dn763935.aspx
  [비디오: VHD 시작 - 백그라운드 작업]: http://azure.microsoft.com/ko-kr/documentation/videos/getting-started-with-azure-virtual-machines
  [비디오: Mark Russinovich의 FAQ 소개 - Microsoft Azure에서 Windows 실행]: http://azure.microsoft.com/ko-kr/documentation/videos/mark-russinovich-windows-on-azure
  [비디오: 다시 사용할 수 있는 이미지를 만들어 웹 팜에 새 가상 컴퓨터 추가]: http://azure.microsoft.com/ko-kr/documentation/videos/adding-virtual-machines-web-farm
  [비디오: 가상 하드 드라이브와 저장소 계정을 추가하고 가상 컴퓨터 크기 조정]: http://azure.microsoft.com/ko-kr/documentation/videos/adding-drives-scaling-virtual-machines
  [비디오: Scott Guthrie의 가상 컴퓨터 소개]: http://azure.microsoft.com/ko-kr/documentation/videos/virtual-machines-scottgu
  [비디오: Azure 가상 컴퓨터의 저장소 및 디스크 기본 사항]: http://azure.microsoft.com/ko-kr/documentation/videos/storage-and-disks-virtual-machines
