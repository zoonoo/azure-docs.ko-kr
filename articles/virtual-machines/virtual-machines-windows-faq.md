---
title: "Windows VM에 대한 FAQ | Microsoft Docs"
description: "리소스 관리자 모델을 사용하여 만든 Windows 가상 컴퓨터에 대해 가장 일반적인 질문 중 일부에 대한 답변을 제공합니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/16/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: f78c66904a3e07ffde5908167f27882e51e781e7


---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Windows 가상 컴퓨터에 대한 자주 묻는 질문과 대답
이 문서에서는 Azure에서 리소스 관리자 배포 모델을 사용하여 만든 Windows 가상 컴퓨터에 대한 일부 일반적인 질문을 해결합니다. 이 항목의 Linux 버전에 대해서는 [Linux 가상 컴퓨터에 대한 자주 묻는 질문과 대답](virtual-machines-linux-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Azure VM에서 무엇을 실행할 수 있습니까?
모든 구독자는 Azure 가상 컴퓨터에서 서버 소프트웨어를 실행할 수 있습니다. Azure에서 Microsoft 서버 소프트웨어 실행을 위한 지원 정책에 대한 자세한 내용은 [Azure 가상 컴퓨터에 대한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/kb/2721672)

Windows 7 및 Windows 8.1의 특정 버전은 MSDN Azure 혜택 구독자와 MSDN 개발 및 테스트 종량제 구독자가 개발 및 테스트 작업을 위해 사용할 수 있습니다. 지침과 제한 사항을 포함한 자세한 내용은 [MSDN 구독자를 위한 Windows 클라이언트 이미지](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/)를 참조하세요. 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>가상 컴퓨터에 얼마나 많은 용량의 저장소를 사용할 수 있습니까?
각 데이터 디스크의 최대 용량은 1 TB 입니다. 사용할 수 있는 데이터 디스크의 수는 가상 컴퓨터의 크기에 따라 달라집니다. 자세한 내용은 [가상 컴퓨터의 크기](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

Azure 저장소 계정은 운영 체제 디스크 및 모든 데이터 디스크에 대한 저장소를 제공합니다. 각 디스크는 페이지 blob으로 저장된 .vhd 파일입니다. 가격 책정에 대한 자세한 내용은 [저장소 가격 세부 정보](https://azure.microsoft.com/pricing/details/storage/)를 참조하세요.

## <a name="how-can-i-access-my-virtual-machine"></a>나의 가상 컴퓨터에 액세스 하려면 어떻게 해야 합니까?
RDP(원격 데스크톱 연결)를 사용하여 Windows VM에 대한 원격 연결을 설정합니다. 자세한 내용은 [Windows를 실행하는 Azure 가상 컴퓨터에 연결하고 로그온하는 방법](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 서버가 원격 데스크톱 서비스 세션 호스트로 구성되지 않으면 최대 2개의 동시 연결이 지원됩니다.  

원격 데스크톱에 문제가 있는 경우 [Windows 기반 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 

Hyper-V에 친숙한 경우 VMConnect와 유사한 도구를 찾을 수 있습니다. 가상 컴퓨터에 대한 콘솔 액세스가 지원되지 않으므로 Azure는 유사한 도구를 제공하지 않습니다.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>임시 디스크(기본적으로 D: 드라이브)를 사용하여 데이터를 저장할 수 있나요?
데이터를 저장하는 데 임시 디스크를 사용해서는 안 됩니다. 해당 드라이브는 임시 저장소일 뿐이므로 복구할 수 없는 데이터가 손실될 위험이 있습니다. Virtual Machine가 다른 호스트로 이동하면 데이터가 손실될 수 있습니다. 가상 컴퓨터 크기를 조정하고, 호스트를 업데이트 하거나, 호스트의 하드웨어가 실패하는 경우가, 가상 컴퓨터가 이동할 수 있는 몇 가지 이유가 됩니다.

D: 드라이브 문자를 사용해야 하는 응용 프로그램이 있는 경우 드라이브 문자를 재할당하여 임시 디스크가 D: 외의 다른 드라이브 문자를 사용하도록 할 수 있습니다. 지침에 대한 자세한 내용은 [Windows 임시 디스크의 드라이브 문자 변경](virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>임시 디스크의 드라이브 문자 변경을 어떻게 합니까?
페이지 파일을 이동하고 드라이브 문자를 다시 할당하여 드라이브 문자를 변경할 수는 있지만, 이렇게 하려면 관련 단계를 특정 순서에 따라 수행해야 합니다. 지침에 대한 자세한 내용은 [Windows 임시 디스크의 드라이브 문자 변경](virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>가용성 집합에 기존 VM을 추가할 수 있나요?
아니요. VM이 가용성 집합에 속하려면 집합 내에서 VM을 만들어야 합니다. 현재는 VM을 만든 이후에 가용성 집합에 추가하는 방법이 없습니다.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>가상 컴퓨터를 Azure에 업로드할 수 있나요?
예. 자세한 내용은 [Azure에 Windows VM 이미지 업로드 ](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="can-i-resize-the-os-disk"></a>OS 디스크의 크기를 조정할 수 있나요?
예. 자세한 내용은 [Azure 리소스 그룹에서 가상 컴퓨터의 OS 드라이브를 확장하는 방법](virtual-machines-windows-expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>기존 Azure VM을 복사 또는 복제할 수 있나요?
예. 자세한 내용은 [Resource Manager 배포 모델에서 Windows 가상 컴퓨터 복사본을 만드는 방법](virtual-machines-windows-vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Azure Resource Manager를 통해 캐나다 중부 및 캐나다 동부 지역이 보이지 않는 이유가 무엇인가요?
캐나다 중부 및 캐나다 동부의 새로운 두 지역은 기존의 Azure 구독에 대한 가상 컴퓨터 만들기에 자동으로 등록되지 않습니다. 가상 컴퓨터가 Azure 포털을 통해 Azure Resource Manager를 사용하는 다른 지역에 배포될 때 자동으로 등록됩니다. 가상 컴퓨터가 다른 Azure 지역에 배포된 후 새로운 지역은 다음 가상 컴퓨터에 대해 사용할 수 있어야 합니다.

## <a name="does-azure-support-linux-vms"></a>Azure에서 Linux VM을 지원하나요?
예. 사용해 보기 위해 Linux VM을 신속하게 만들려면 [포털을 사용하여 Azure에서 Linux VM 만들기](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>VM을 만든 후에 NIC를 추가할 수 있나요?
아니요. NIC를 추가하는 작업은 생성 시에만 수행할 수 있습니다.

## <a name="are-there-any-computer-name-requirements"></a>컴퓨터 이름 요구 사항이 있나요?
예. 컴퓨터 이름은 15자까지 지정할 수 있습니다. 리소스 명명과 관련된 자세한 내용은 [인프라 명명 지침](virtual-machines-windows-infrastructure-naming-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 을 참조하세요.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>VM을 만들 때의 사용자 이름 요구 사항은 무엇인가요?
사용자 이름은 20자까지 지정할 수 있으며 마침표(".")로 끝날 수 없습니다. 

다음 사용자 이름은 사용할 수 없습니다.

<table>
    <tr>
        <td style="text-align:center">administrator </td><td style="text-align:center"> 관리자 </td><td style="text-align:center"> 사용자 </td><td style="text-align:center"> user1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">backup </td><td style="text-align:center"> console </td><td style="text-align:center"> david </td><td style="text-align:center"> guest</td>
    </tr>
    <tr>
        <td style="text-align:center">john </td><td style="text-align:center"> owner </td><td style="text-align:center"> root </td><td style="text-align:center"> server</td>
    </tr>
    <tr>
        <td style="text-align:center">sql </td><td style="text-align:center"> support </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>VM을 만들 때의 암호 요구 사항은 무엇인가요?
암호는 8~123자 사이로 지정해야 하며 다음의 4가지 복잡성 요구 사항 중 3가지를 충족해야 합니다.

* 소문자 포함
* 대문자 포함
* 숫자 포함
* 특수 문자 포함(정규식 일치 [\W_])

사용할 수 없는 암호:

<table>
    <tr>
        <td style="text-align:center">abc@123</td><td style="text-align:center">P@$$w0rd</td><td style="text-align:center">P@ssw0rd</td><td style="text-align:center">P@ssword123</td><td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td><td style="text-align:center">Password!</td><td style="text-align:center">Password1</td><td style="text-align:center">Password22</td><td style="text-align:center">iloveyou!</td>
    </tr>
</table>



<!--HONumber=Nov16_HO3-->


