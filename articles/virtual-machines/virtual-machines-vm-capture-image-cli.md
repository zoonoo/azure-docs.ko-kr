<properties
	pageTitle="CLI를 사용하여 Linux를 실행하는 가상 컴퓨터의 이미지 캡처"
	description="Linux를 실행하는 Azure VM(가상 컴퓨터)의 이미지를 캡처하는 방법에 대해 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="karthmut"
	manager="madhana"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="karthmut"/>




# CLI의 템플릿으로 사용할 Linux 가상 컴퓨터를 캡처하는 방법##



이 문서에서는 Linux가 실행되는 Azure 가상 컴퓨터를 캡처하여 다른 가상 컴퓨터를 만들 때 템플릿처럼 사용하는 방법을 소개합니다. 이 템플릿에는 OS 디스크를 비롯해 가상 컴퓨터에 연결되는 모든 데이터 디스크가 포함됩니다. 네트워킹 구성은 포함되지 않으므로 템플릿을 사용하는 다른 가상 컴퓨터를 만들 때 구성해야 합니다.



Azure에서는 이 템플릿이 이미지로 처리되며 이미지 목록에 저장됩니다. 이 위치에 업로드한 이미지도 저장됩니다. 이미지에 대한 자세한 내용은 [Azure의 가상 컴퓨터 이미지 정보][]를 참조하세요.



##시작하기 전에##



이러한 단계는 이미 Azure 가상 컴퓨터를 만들었으며 데이터 디스크 연결을 비롯해 운영 체제 구성을 완료했다는 것을 전제로 합니다. 아직 완료하지 못한 경우 다음 지침을 참조하세요.



- [사용자 지정 가상 컴퓨터를 만드는 방법][]

- [가상 컴퓨터에 데이터 디스크를 연결하는 방법][]



##가상 컴퓨터 캡처##



1. 가상 컴퓨터에 연결합니다. 자세한 내용은 [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법][]을 참조하십시오.



2. 상태가 **StoppedDeallocated**인 경우에만 VM을 캡처할 수 있습니다. SSH 창에서 다음 명령을 입력하여 VM을 종료합니다.



        vm shutdown [options] <vm-name>



    `vm shutdown`의 옵션 중 하나가 `-p`인 경우, 종료 시 계산 리소스가 보존됩니다. VM을 캡처하려면 프로비전을 해제해야 하므로 사용하도록 설정하면 **안됩니다**.



3. 다음 명령을 실행하여 VM 이미지를 캡처합니다.



        vm capture <vm-name> <target-image-name> --deleted



    이미지를 캡처하려면 VM을 삭제해야 합니다. 이를 수행하려면 `--deleted` 또는 `-t`를 사용할 수 있습니다.



4. VM 이미지 목록을 확인하여 이미지가 캡처되었는지를 확인할 수 있습니다.



        vm image list | grep <target-image-name>



    `| grep <target-image-name>` 옵션은 선택 사항이지만 사용하는 경우 목록에서 이미지를 보다 쉽게 찾을 수 있습니다.



아래에는 터미널 출력을 포함하여 가상 컴퓨터를 캡처하는 샘플 연습이 나와 있습니다.


    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status            Location  DNS Name                  IP Address

    data:    -----------  ----------------  --------  ------------------------  ------------

    data:    kmorig       RoleStateUnknown  West US   kmorig.cloudapp.net       100.92.56.16

    info:    vm list command OK

    ~$ azure vm shutdown kmorig

    info:    Executing command vm shutdown

    + Getting virtual machines

    + Shutting down VM

    info:    vm shutdown command OK

    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status              Location  DNS Name                  IP Address

    data:    -----------  ------------------  --------  ------------------------  ----------

    data:    kmorig       StoppedDeallocated  West US   kmorig.cloudapp.net

    info:    vm list command OK

    ~$ azure vm capture kmorig kmcaptured --deleted

    info:    Executing command vm capture

    + Getting virtual machines

    + Checking image with name kmcaptured exists

    + Capturing VM

    info:    vm capture command OK

    ~$ azure vm image list | grep kmcaptured

    data:    kmcaptured



자세한 내용과 추가 명령을 확인하려면 [Azure CLI 설명서 페이지][]를 참조하세요.


[Azure CLI 설명서 페이지]: ../virtual-machines-command-line-tools.md

[Linux를 실행하는 가상 컴퓨터에 로그온하는 방법]: virtual-machines-linux-how-to-log-on.md

[Azure의 가상 컴퓨터 이미지 정보]: http://msdn.microsoft.com/library/azure/dn790290.aspx

[사용자 지정 가상 컴퓨터를 만드는 방법]: virtual-machines-create-custom.md

[가상 컴퓨터에 데이터 디스크를 연결하는 방법]: storage-windows-attach-disk.md

<!---HONumber=58--> 