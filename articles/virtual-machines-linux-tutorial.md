<properties pageTitle="Azure에서 Linux를 실행하는 가상 컴퓨터 만들기" description="Azure의 이미지를 사용하여 Linux를 실행하는 Azure VM(가상 컴퓨터)을 만드는 방법에 대해 알아봅니다." services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor="tysonn"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="1/19/2015" ms.author="kathydav"/>

#Linux를 실행하는 가상 컴퓨터 만들기 

Azure 관리 포털에서 이미지 갤러리를 사용하면 Linux를 실행하는 가상 컴퓨터를 쉽게 만들 수 있습니다. 이 가이드에서는 Azure를 사용해 본 적이 없다고 가정하여 해당 작업을 수행하는 방법을 설명합니다.

> [AZURE.NOTE] 이전에 Azure VM에서 이 자습서를 완료한 적은 없어도 되지만 Azure 계정은 있어야 합니다. 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 계정 만들기](http://www.windowsazure.com/ko-kr/develop/php/tutorials/create-a-windows-azure-account/)를 참조하세요. 

이 자습서에서는 다음 내용을 다룹니다.

- [Azure의 가상 컴퓨터 정보] []
- [가상 컴퓨터를 만드는 방법] []
- [가상 컴퓨터를 만든 후 로그온하는 방법] []
- [새 가상 컴퓨터에 데이터 디스크를 연결하는 방법] []

**중요**: 이 자습서에서는 가상 네트워크에 연결되지 않은 가상 컴퓨터를 만듭니다. 가상 컴퓨터에서 가상 네트워크를 사용하려는 경우 가상 컴퓨터를 만들 때 가상 네트워크를 지정해야 합니다. 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크 개요](http://go.microsoft.com/fwlink/p/?LinkID=294063)를 참조하세요.

## <a id="virtualmachine"> </a>Azure의 가상 컴퓨터 정보 ##

Azure의 가상 컴퓨터는 제어 및 관리할 수 있는 클라우드의 서버입니다. Azure에서 가상 컴퓨터를 만든 후 필요할 때마다 삭제하고 다시 만들 수 있으며 사무실에 있는 서버와 마찬가지로 가상 컴퓨터에 액세스할 수 있습니다. VHD(가상 하드 디스크) 파일은 가상 컴퓨터를 만드는 데 사용됩니다. 가상 컴퓨터에 사용되는 VHD 유형은 다음과 같습니다.

- **이미지** - 새 가상 컴퓨터를 만들기 위한 템플릿으로 사용되는 VHD입니다. 이미지에는 컴퓨터 이름, 사용자 계정 설정 등 실행 중인 가상 컴퓨터와 같은 특정 설정이 없으므로 템플릿입니다. 이미지를 사용하여 가상 컴퓨터를 만드는 경우 새 가상 컴퓨터에 대한 운영 체제 디스크가 자동으로 만들어집니다.
- **디스크** - 디스크는 운영 체제의 실행 버전으로 부팅 및 탑재할 수 있는 VHD입니다. 이미지가 프로비전되면 디스크가 됩니다. 이미지를 사용하여 가상 컴퓨터를 만드는 경우 항상 디스크가 만들어집니다. 가상화된 하드웨어에 연결되어 서비스의 일부로 실행되는 모든 VHD는 디스크입니다.

이미지를 사용하여 가상 컴퓨터를 만드는 경우 사용할 수 있는 옵션은 다음과 같습니다.

- Azure 관리 포털의 이미지 갤러리에 제공된 이미지를 사용하여 가상 컴퓨터를 만듭니다.
- 이미지가 포함된 .vhd 파일을 만들고 Azure에 업로드한 후 이미지를 사용하여 가상 컴퓨터를 만듭니다. 사용자 지정 이미지를 만들고 업로드하는 방법에 대한 자세한 내용은 [Linux 운영 체제가 포함된 가상 하드 디스크 만들기 및 업로드](/ko-kr/manage/linux/common-tasks/upload-a-vhd/)를 참조하세요.

각 가상 컴퓨터는 개별적으로 또는 다른 가상 컴퓨터와 그룹화되어 클라우드 서비스에 상주합니다. 동일한 클라우드 서비스에 가상 컴퓨터를 배치하여 가상 컴퓨터가 서로 통신할 수 있게 하고, 가상 컴퓨터 간에 네트워크 트래픽 부하를 분산하며, 컴퓨터의 고가용성을 유지할 수 있습니다. 클라우드 서비스 및 가상 컴퓨터에 대한 자세한 내용은 [Azure 소개](http://go.microsoft.com/fwlink/p/?LinkId=311926)를 참조하세요.

## <a id="custommachine"> </a>가상 컴퓨터를 만드는 방법 ##

이 자습서에서는 **빨리 만들기** 방법에 비해 더 많은 옵션을 제공하는 **갤러리에서** 방법을 사용하여 가상 컴퓨터를 만듭니다. 필요한 경우 연결된 리소스, DNS 이름 및 네트워크 연결을 선택할 수 있습니다.

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../includes/virtual-machines-create-LinuxVM.md)]

[AZURE.INCLUDE [virtual-machines-Linux-tutorial-log-on-attach-disk](../includes/virtual-machines-Linux-tutorial-log-on-attach-disk.md)]

> [AZURE.NOTE] SSH 키를 식별에 사용하여 Linux 가상 컴퓨터에 연결할 수도 있습니다. SSH 키를 구성하고 연결하는 방법에 대한 자세한 내용은 [Azure에서 Linux와 함께 SSH를 사용 하는 방법](./virtual-machines-linux-use-ssh-key/)을 참조하세요.

##다음 단계 

Azure의 Linux에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure의 Linux 소개](http://www.windowsazure.com/ko-kr/documentation/articles/introduction-linux/)

- [Mac 및 Linux에서 Azure 명령줄 도구를 사용하는 방법](http://www.windowsazure.com/ko-kr/documentation/articles/xplat-cli/)

- [Azure VM 구성 설정 정보](http://msdn.microsoft.com/library/azure/dn763935.aspx)

- [Azure의 Linux용 Docker 가상 컴퓨터 확장](../virtual-machines-docker-vm-extension/)


[다음 단계]: #next
[Azure의 가상 컴퓨터 정보]: #virtualmachine
[가상 컴퓨터를 만드는 방법]: #custommachine
[가상 컴퓨터를 만든 후 로그온하는 방법]: #logon
[새 가상 컴퓨터에 데이터 디스크를 연결하는 방법]: #attachdisk


<!--HONumber=42-->
