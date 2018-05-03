

이미지는 Azure에서 운영 체제에 새 가상 컴퓨터를 제공하는 데에 사용됩니다. 이미지에는 데이터 디스크가 하나 이상 있을 수 있습니다. 이미지는 여러 원본에서 사용할 수 있습니다.

* Azure에서는 [마켓플레이스](https://azure.microsoft.com/gallery/virtual-machines/)에 있는 이미지를 제공합니다. 최신 버전 Windows Server 및 Linux 운영 체제 배포판이 제공됩니다. 일부 이미지에는 SQL Server와 같은 응용 프로그램이 포함되어 있습니다. MSDN 혜택 및 MSDN 종량제 구독자는 추가 이미지에 액세스할 수 있습니다.
* 오픈 소스 커뮤니티에서는 [VM 디포](http://vmdepot.msopentech.com/List/Index)를 통해 이미지를 제공합니다.
* Azure에서 기존 Azure 가상 컴퓨터를 캡처하여 이미지로 사용하거나 이미지를 업로드하여 직접 이미지를 저장하고 사용할 수도 있습니다.

## <a name="about-vm-images-and-os-images"></a>VM 이미지 및 OS 이미지 정보
Azure에서 *VM 이미지* 및 *OS 이미지*의 두 가지 이미지 형식을 사용할 수 있습니다. VM 이미지에는 운영 체제 및 이미지를 만들 때 가상 컴퓨터에 연결된 모든 디스크가 포함됩니다. VM 이미지는 최신 형식 이미지입니다. VM 이미지가 도입되기 전에는 Azure에서 이미지에 일반화된 운영 체제만 포함할 수 있었고 추가 디스크가 없었습니다. 일반화된 운영 체제만 포함된 VM 이미지는 기본적으로 이미지의 원래 형식인 OS 이미지와 같습니다.

Azure의 가상 컴퓨터나 다른 곳에서 실행 중이지만 복사하여 업로드한 가상 컴퓨터를 기반으로 직접 이미지를 만들 수도 있습니다. 이미지를 사용하여 둘 이상의 가상 컴퓨터를 만들려는 경우에는 일반화를 통해 이미지로 사용할 준비를 해야 합니다. Windows Server 이미지를 만들려면 .vhd 파일을 업로드 하기 전에 서버에서 Sysprep 명령을 실행합니다. Sysprep에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://go.microsoft.com/fwlink/p/?LinkId=392030) 및 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)을 참조하세요. Sysprep를 실행하기 전에 VM을 백업합니다. Linux 이미지를 만드는 작업은 배포판에 따라 다릅니다. 일반적으로는 배포판에 해당하는 명령 집합을 실행한 다음 Azure Linux 에이전트를 실행합니다.
