---
title: Azure의 Linux VM에 대해 자주 묻는 질문
description: 리소스 관리자 모델을 사용하여 만든 Linux 가상 머신에 대해 가장 일반적인 질문 중 일부에 대한 답변을 제공합니다.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: d510fa35163a4c4a79352f5f35af9c384bf85304
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104596317"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Linux Virtual Machines에 대한 질문과 대답
이 문서에서는 Azure에서 Resource Manager 배포 모델을 사용하여 만든 Linux 가상 머신에 대한 일반적인 질문을 일부 해결합니다. 이 항목의 Windows 버전에 대해서는 [Windows Virtual Machines에 대한 질문과 대답](../windows/faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>Azure VM에서 무엇을 실행할 수 있습니까?
모든 구독자는 Azure 가상 머신에서 서버 소프트웨어를 실행할 수 있습니다. 자세한 내용은 [Azure 인증 배포의 Linux](endorsed-distros.md)를 참조하세요.

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>가상 컴퓨터에 얼마나 많은 용량의 스토리지를 사용할 수 있습니까?
각 데이터 디스크의 최대 용량은 32,767GiB입니다. 사용할 수 있는 데이터 디스크의 수는 가상 머신의 크기에 따라 달라집니다. 자세한 내용은 [Virtual Machines의 크기](../sizes.md)를 참조하세요.

Azure Managed Disks는 데이터 영구 저장을 위해 Azure Virtual Machines와 함께 사용하기 적합한 디스크 스토리지 제품입니다. 각 Virtual Machine과 함께 여러 Managed Disks를 사용할 수 있습니다. Managed Disks는 프리미엄 Managed Disks와 표준 Managed Disks 등 내구성이 뛰어난 두 가지 스토리지 옵션을 제공합니다. 가격 책정 정보는 [Managed Disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks)을 참조하세요.

Azure Stroage 계정은 운영 체제 디스크 및 모든 데이터 디스크에 대한 스토리지도 제공할 수 있습니다. 각 디스크는 페이지 blob으로 저장된 .vhd 파일입니다. 가격 책정에 대한 자세한 내용은 [스토리지 가격 세부 정보](https://azure.microsoft.com/pricing/details/storage/)를 참조하세요.

## <a name="how-can-i-access-my-virtual-machine"></a>나의 가상 머신에 액세스 하려면 어떻게 해야 합니까?
가상 머신에 로그인하려면 SSH(Secure Shell)를 사용하여 원격 연결을 설정합니다. [Windows에서](ssh-from-windows.md) 또는 [Linux 및 Mac에서](mac-create-ssh-keys.md) 연결하는 방법은 지침을 참조하세요. 기본적으로, SSH는 최대 10개의 동시 연결을 허용합니다. 구성 파일을 편집하여 이 수를 늘릴 수 있습니다.

문제가 있는 경우 [SSH(Secure Shell) 연결 문제 해결](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 확인하세요.

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>임시 디스크(/dev/sdb1)를 데이터 저장에 사용할 수 있나요?
임시 디스크(/dev/sdb1)를 데이터 저장에 사용하지 마세요. 임시 디스크는 임시 스토리지로만 사용해야 합니다. 복구할 수 없는 데이터는 손실될 위험이 있습니다.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>기존 Azure VM을 복사 또는 복제할 수 있나요?
예. 자세한 내용은 [리소스 관리자 배포 모델에서 Linux 가상 머신의 복사본을 만드는 방법](copy-vm.md)을 참조하세요.

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Azure Resource Manager를 통해 캐나다 중부 및 캐나다 동부 지역이 보이지 않는 이유가 무엇인가요?
캐나다 중부 및 캐나다 동부의 새로운 두 지역은 기존의 Azure 구독에 대한 가상 머신 만들기에 자동으로 등록되지 않습니다. 가상 머신이 Azure 포털을 통해 Azure Resource Manager를 사용하는 다른 지역에 배포될 때 자동으로 등록됩니다. 가상 머신이 다른 Azure 지역에 배포된 후 새로운 지역은 다음 가상 머신에 대해 사용할 수 있어야 합니다.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>VM을 만든 후에 NIC를 추가할 수 있나요?
예, 이제 가능합니다. 먼저 VM에 대한 할당 취소를 중지해야 합니다. 그런 다음 NIC를 추가하거나 제거할 수 있습니다(VM에 있는 마지막 NIC가 아닌 경우). 

## <a name="are-there-any-computer-name-requirements"></a>컴퓨터 이름 요구 사항이 있나요?
예. 컴퓨터 이름은 64자까지 지정할 수 있습니다. [명명 규칙 및 제한 사항](/azure/architecture/best-practices/resource-naming)을 참조하여 리소스 이름 지정에 대해 자세히 알아보세요.

## <a name="are-there-any-resource-group-name-requirements"></a>리소스 그룹 이름에 대한 요구 사항이 있나요?
예. 리소스 그룹 이름은 90자까지 지정할 수 있습니다. [명명 규칙 및 제한 사항](/azure/architecture/best-practices/resource-naming)을 참조하여 리소스 그룹에 대해 자세히 알아보세요.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>VM을 만들 때의 사용자 이름 요구 사항은 무엇인가요?

사용자 이름의 길이는 1~32자 여야 합니다.

다음 사용자 이름은 사용할 수 없습니다.

- `1`
- `123`
- `a`
- `actuser`
- `adm`
- `admin`
- `admin1`
- `admin2`
- `administrator`
- `aspnet`
- `backup`
- `console`
- `david`
- `guest`
- `john`
- `owner`
- `root`
- `server`
- `sql`
- `support_388945a0`
- `support`
- `sys`
- `test`
- `test1`
- `test2`
- `test3`
- `user`
- `user1`
- `user2`
- `user3`
- `user4`
- `user5`
- `video`


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>VM을 만들 때의 암호 요구 사항은 무엇인가요?

사용하는 도구에 따라 서로 다른 암호 길이를 요구합니다.
 - 포털 - 12-72자 사이
 - PowerShell - 8-123자 사이
 - CLI - 12-123자 사이
 - ARM(Azure Resource Manager) 템플릿 - 12-72자에 제어용 문자 사용 불가
 

암호는 다음의 4가지 복잡성 요구 사항 중 3가지를 충족해야 합니다.

* 소문자 포함
* 대문자 포함
* 숫자 포함
* 특수 문자 포함(Regex 일치 [\W_])

사용할 수 없는 암호:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Password!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>