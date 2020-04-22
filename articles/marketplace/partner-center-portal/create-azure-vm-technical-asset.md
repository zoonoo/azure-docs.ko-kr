---
title: Azure 가상 머신 기술 자산 만들기
description: Azure Marketplace용 가상 시스템(VM) 오퍼에 대한 기술 자산을 만들고 구성하는 방법을 알아봅니다.
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 4d2d33f9d83132147b5b257ffcd6d659f272b8ec
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730714"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Azure 가상 머신 기술 자산 만들기

> [!IMPORTANT]
> Azure 가상 컴퓨터 의 관리를 클라우드 파트너 포털에서 파트너 센터로 이전합니다. 오퍼가 마이그레이션될 때까지 [Cloud Partner Portal에 대한 가상 시스템 오퍼에 대한 기술 자산 만들기의](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-technical-assets) 지침을 따라 오퍼를 관리하십시오.

이 문서에서는 Azure Marketplace에 대한 VM(가상 시스템) 오퍼에 대한 기술 자산을 만들고 구성하는 방법에 대해 설명합니다. VM에는 운영 체제 가상 하드 디스크(VHD) 및 선택적 관련 데이터 디스크 VHD의 두 가지 구성 요소가 포함되어 있습니다.

* **운영 체제 VHD** - 오퍼와 함께 배포하는 운영 체제 및 솔루션을 포함합니다. VHD를 준비하는 과정은 Linux 기반, Windows 기반 또는 사용자 지정 기반 VM인지 여부에 따라 다릅니다.
* **데이터 디스크 VHD** - VM을 위한 전용 영구 스토리지입니다. 운영 체제 VHD(예: C: 드라이브)를 사용하여 영구 정보를 저장하지 마십시오.

VM 이미지에는 하나의 운영 체제 디스크와 최대 16개의 데이터 디스크가 포함되어 있습니다. 디스크가 비어 있더라도 데이터 디스크당 하나의 VHD를 사용합니다.

> [!NOTE]
> 사용하는 운영 체제에 관계없이 솔루션에 필요한 최소 데이터 디스크 수만 추가합니다. 고객은 배포 시 이미지의 일부인 디스크를 제거할 수 없지만 배포 중이나 배포 후에 언제든지 디스크를 추가할 수 있습니다.

> [!IMPORTANT]
> 계획의 모든 VM 이미지에는 동일한 수의 데이터 디스크가 있어야 합니다.

## <a name="fundamental-technical-knowledge"></a>기본 기술 지식

이러한 자산을 디자인, 빌드 및 테스트하려면 시간이 걸리며 Azure 플랫폼과 오퍼를 빌드하는 데 사용되는 기술에 대한 기술 지식이 필요합니다. 솔루션 도메인 외에도 엔지니어링 팀은 다음과 같은 Microsoft 기술에 대한 지식을 가지고 있어야 합니다.

* [Azure 서비스](https://azure.microsoft.com/services/)에 대한 기본적 이해
* [Azure 애플리케이션을 디자인 및 설계](https://azure.microsoft.com/solutions/architecture/)하는 방법
* [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) 및 [Azure 네트워킹](https://azure.microsoft.com/services/?filter=networking)에 대한 실무 지식
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)에 대한 실무 지식
* [JSON](https://www.json.org/)에 대한 실무 지식

## <a name="suggested-tools--optional"></a>추천 도구 – 선택 사항

다음 스크립팅 환경 중 하나를 사용하여 VM 및 VHD를 관리하는 것이 좋습니다.

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://code.visualstudio.com/)

또한 개발 환경에 다음 도구를 추가하는 것이 좋습니다.

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)
  * 확장: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * 확장: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * 확장: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

[Azure 개발자 도구](https://azure.microsoft.com/product-categories/developer-tools/) 페이지에서 사용 가능한 도구를 검토하고 Visual Studio를 사용하는 경우 Visual Studio [마켓플레이스.](https://marketplace.visualstudio.com/)

## <a name="create-a-vm-image-using-an-approved-base"></a>승인된 베이스를 사용하여 VM 이미지 만들기

> [!NOTE]
> 사용자 고유의 구내에서 빌드한 이미지를 사용하여 가상 컴퓨터 기술 자산을 만들려면 [사용자 고유의 이미지를 사용하여 VM 만들기로](#create-a-vm-using-your-own-image)이동합니다.

이 섹션에서는 RDP(원격 데스크톱 프로토콜) 사용, VM 크기 선택, 최신 Windows 업데이트 설치 및 VHD 이미지 일반화와 같은 승인된 기본 사용의 다양한 측면에 대해 설명합니다.

다음 섹션에서는 주로 창 기반 VHD에 중점을 둡니다. Linux 기반 VHD 를 만드는 방법에 대한 자세한 내용은 [Azure에서 승인한 배포판에 대한 Linux를](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)참조하십시오.

> [!WARNING]
> 이 항목의 지침을 따라 Azure를 사용하여 미리 구성된 승인된 운영 체제가 포함된 VM을 만듭니다. 솔루션과 호환되지 않는 경우 승인된 운영 체제를 사용하여 온-프레미스 VM을 만들고 구성할 수 있습니다. 그런 다음, [Azure에 업로드할 Windows VHD 또는 VHDX 준비](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)에서 설명한 대로 업로드할 수 있도록 구성하고 준비할 수 있습니다.

### <a name="select-an-approved-base"></a>승인된 기본 이미지 선택

Windows 운영 체제 또는 Linux를 기본으로 선택합니다.

#### <a name="windows"></a>Windows

Windows 기반 VM 이미지의 운영 체제 VHD는 Windows 서버 또는 SQL Server를 포함하는 Azure 승인 기본 이미지를 기반으로 해야 합니다. 시작하려면 Azure 포털에서 다음 이미지 중 하나에서 VM을 만듭니다.

* Windows Server([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014(엔터프라이즈,](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) 표준, 웹)
* [SQL Server 2012 SP2(엔터프라이즈,](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) 표준, 웹)

> [!NOTE]
> 현재 Azure 포털 또는 Azure PowerShell을 사용하는 경우 2014년 9월 8일 이후에 게시된 Windows Server 이미지가 승인됩니다.

#### <a name="linux"></a>Linux

Azure는 승인된 Linux 배포판의 범위를 제공합니다. 현재 목록은 [Azure 보증 배포판의 Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)를 참조하세요.

### <a name="create-vm-in-the-azure-portal"></a>Azure Portal에서 VM 만들기

다음 단계에 따라 [Azure 포털에서](https://ms.portal.azure.com/)기본 VM 이미지를 만듭니다.

1. VM 오퍼를 게시하는 데 사용할 Azure 구독과 연결된 Microsoft 계정으로 Azure [포털에](https://ms.portal.azure.com/) 로그인합니다.
2. 새 리소스 그룹을 만들고, **리소스 그룹 이름**, **구독** 및 **리소스 그룹 위치**를 제공합니다. 자세한 내용은 [리소스 관리를](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)참조하십시오.
3. 왼쪽에 **있는 가상 컴퓨터를** 선택하여 가상 시스템 세부 정보 페이지를 표시합니다.
4. **+ 추가를** 선택하여 **가상 시스템 만들기 환경을**엽니다.
5. 드롭다운 목록에서 이미지를 선택하거나 **모든 공개 및 비공개 이미지 찾아보기를** 클릭하여 사용 가능한 모든 가상 시스템 이미지를 검색하거나 찾아봅을 클릭합니다.
6. 다음 권장 사항을 사용하여 배포할 VM의 크기를 선택합니다.
    * VHD 온-프레미스를 개발하려는 경우 크기는 중요하지 않습니다. 더 작은 VM 중 하나를 사용하는 것이 좋습니다.
    * Azure에서 이미지를 개발하려는 경우 선택된 이미지에 대한 권장 VM 크기 중 하나를 사용하는 것이 좋습니다.

7. 디스크 섹션에서 **고급** 섹션을 확장하고 **관리 디스크 사용** 옵션을 **아니요로** **설정합니다.**
8. VM을 만드는 데 필요한 다른 세부 정보를 제공합니다.
9. **검토 + 만들기를** 선택하여 선택 사항을 검토합니다. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

Azure는 지정한 가상 시스템 프로비저닝을 시작합니다. 왼쪽에 있는 **가상 컴퓨터** 탭을 선택하여 진행 상황을 추적할 수 있습니다. 상태가 만들어지면 상태가 **실행 중으로**변경됩니다.

새 Azure 기반 VHD를 만드는 데 어려움이 있는 경우 [VHD 생성(FAQ) 중 일반적인 문제를 참조하세요.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-issues-during-vhd-creation)

### <a name="connect-to-your-azure-vm"></a>Azure VM에 연결

이 섹션에서는 Azure에서 만든 VM에 연결하고 로그인하는 방법을 설명합니다. 성공적으로 연결되면 호스트 서버에 로컬로 로그인한 것처럼 VM을 사용할 수 있습니다.

#### <a name="connect-to-a-windows-based-vm"></a>Windows 기반 VM에 연결

원격 데스크톱 클라이언트를 사용하여 Azure에서 호스팅되는 Windows 기반 VM에 연결합니다. 대부분의 Windows 버전에는 기본적으로 RDP(원격 데스크톱 프로토콜)에 대한 지원이 포함되어 있습니다. 다른 운영 체제의 경우 원격 데스크톱 [클라이언트에서](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)클라이언트에 대한 자세한 정보를 찾을 수 있습니다.

이 문서에서는 기본 제공 Windows RDP 지원을 사용하여 VM에 연결하는 [방법: Windows를 실행하는 Azure 가상 컴퓨터에 연결하고 로그온하는 방법에](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)대해 자세히 설명합니다.

> [!TIP]
> 이 과정에서 보안 경고가 발생할 수 있습니다. 예를 들어 ".rdp 파일은 알 수 없는 게시자에서 온 것"또는 "사용자 자격 증명을 확인할 수 없습니다." 등의 경고가 있습니다. 이러한 경고는 무시해도 됩니다.

#### <a name="connect-to-a-linux-based-vm"></a>Linux 기반 VM에 연결

Linux 기반 VM에 연결하려면 보안 셸 프로토콜(SSH) 클라이언트가 필요합니다. 다음 단계는 무료 [PuTTY](https://www.ssh.com/ssh/putty/) SHH 터미널을 사용합니다.

1. [Azure 포털](https://ms.portal.azure.com/)로 이동합니다.
2. **가상 머신**을 검색하여 선택합니다.
3. 연결할 VM을 선택합니다.
4. VM이 아직 실행되지 않은 경우 VM을 시작합니다.
5. **개요** 페이지를 열려면 VM 이름을 선택합니다.
6. VM의 공용 IP 주소와 DNS 이름을 확인합니다(이러한 값이 설정되지 않은 경우 [네트워크 인터페이스를 만들어야](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)합니다).
7. PuTTY 애플리케이션을 엽니다.
8. [PuTTY 구성] 대화 상자에서 VM의 IP 주소 또는 DNS 이름을 입력합니다.

    :::image type="content" source="media/avm-putty.png" alt-text="PuTTY 터미널 설정을 보여 줍니다. 호스트 이름 또는 IP 주소와 포트 상자가 강조 표시됩니다.":::

9. PuTTY 터미널을 열려면 **열기를** 선택합니다.
10. 메시지가 표시되면 Linux VM 계정의 계정 이름과 암호를 입력합니다.

연결 문제가 있는 경우 SSH 클라이언트에 대한 설명서를 참조하십시오. 예를 들어, [10장: 일반적인 오류 메시지](https://www.ssh.com/ssh/putty/putty-manuals).

프로비저닝된 Linux VM에 데스크톱을 추가하는 방법을 포함하여 자세한 내용은 [Azure의 Linux VM에 연결하도록 원격 데스크톱 설치 및 구성을](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop)참조하세요.

## <a name="create-a-vm-using-your-own-image"></a>사용자 고유의 이미지를 사용하여 VM 만들기

이 섹션에서는 사용자가 제공한 VM(가상 시스템) 이미지를 만들고 배포하는 방법에 대해 설명합니다. Azure에서 배포한 VHD(가상 하드 디스크)에서 운영 체제 및 데이터 디스크 VHD 이미지를 제공하여 이 작업을 수행할 수 있습니다.

> [!NOTE]
> 선택적으로 승인된 기본 이미지를 사용하려면 [승인된 베이스를 사용하여 VM 이미지 만들기의](#create-a-vm-image-using-an-approved-base)지침을 따릅니다.

1. Azure Storage 계정에 이미지를 업로드합니다.
2. VM 이미지를 배포합니다.
3. VM 이미지를 캡처합니다.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Azure 저장소 계정에 이미지 업로드

1. [Azure 포털](https://portal.azure.com/)에 로그온합니다.
2. 일반화된 운영 체제 VHD 및 데이터 디스크 VHD를 Azure 저장소 계정에 업로드합니다.

### <a name="deploy-your-image"></a>이미지 배포

Azure 포털 또는 Azure PowerShell을 사용하여 이미지를 만듭니다.

#### <a name="deploy-using-the-azure-portal"></a>Azure Portal을 사용하여 배포

1. 홈 페이지에서 리소스 **만들기를**선택하고 "템플릿 배포"를 검색하고 **에서 만들기를**선택합니다.
2. **편집기에서 사용자 고유의 템플릿 빌드를 선택합니다.**

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="사용자 지정 배포 페이지를 보여 줍니다.":::

3. 이 [JSON 템플릿을](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template) 편집기에 붙여넣고 **저장을**선택합니다.
4. 표시된 **사용자 지정 배포** 속성 페이지에 대한 매개 변수 값을 제공합니다.

    | 매개 변수 | 설명 |
    | ------------ | ------------- |
    | 사용자 스토리지 계정 이름 | 셀 2의 내용 |
    | 사용자 스토리지 컨테이너 이름 | 일반화된 VHD가 있는 스토리지 계정 이름 |
    | 공용 IP에 대한 DNS 이름 | 공용 IP DNS 이름입니다. 오퍼를 배포한 후 Azure 포털에서 공용 IP 주소에 대한 DNS 이름을 정의합니다. |
    | 관리자 사용자 이름 | 새 VM에 대한 관리자 계정의 사용자 이름 |
    | 관리자 암호 | 새 VM에 대한 관리자 계정의 암호 |
    | OS 유형 | VM 운영 체제 : 윈도우 또는 리눅스 |
    | 구독 ID | 선택한 구독의 식별자 |
    | 위치 | 배포의 지리적 위치 |
    | VM 크기 | [Azure VM 크기,](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)예를 들어 Standard_A2 |
    | 공용 IP 주소 이름 | 공용 IP 주소의 이름 |
    | VM 이름 | 새 VM의 이름 |
    | Virtual Network 이름 | VM에서 사용하는 가상 네트워크의 이름 |
    | NIC 이름 | 가상 네트워크를 실행하는 네트워크 인터페이스 카드의 이름 |
    | VHD URL | 완전한 OS 디스크 VHD URL |
    |  |  |

5. 이러한 값을 제공한 후 **구입을**선택합니다.

Azure가 배포를 시작합니다. 지정된 저장소 계정 경로에 지정된 관리되지 않는 VHD가 있는 새 VM을 만듭니다. 포털 왼쪽에 있는 **가상 컴퓨터를** 선택하여 Azure 포털의 진행 률을 추적할 수 있습니다. VM을 만들면 상태가 시작에서 실행으로 변경됩니다.

#### <a name="deploy-using-azure-powershell"></a>Azure PowerShell을 사용하여 배포

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>VM 이미지 캡처

접근 방식에 해당하는 다음 지침을 사용합니다.

* Azure PowerShell: [Azure VM에서 관리되지 않는 VM 이미지를 만드는 방법](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* Azure CLI: [가상 머신 또는 VHD의 이미지를 만드는 방법](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API: [Virtual Machines - 캡처](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>가상 시스템 구성

이 섹션에서는 Azure VM의 크기를 조정, 업데이트 및 일반화하는 방법에 대해 설명합니다. 이러한 단계는 Azure 마켓플레이스에 배포할 VM을 준비하는 데 필요합니다.

### <a name="sizing-the-vhds"></a>VHD 크기 조정

운영 체제(및 선택적으로 추가 서비스)로 미리 구성된 VM 중 하나를 선택한 경우 이미 표준 Azure VM 크기를 선택했습니다. 사전 구성된 OS로 솔루션을 시작하는 것이 좋습니다. 그러나 OS를 수동으로 설치하는 경우 VM 이미지에서 기본 VHD의 크기를 조정해야 합니다.

* 윈도우의 경우 운영 체제 VHD는 127-128GB 고정 포맷 VHD로 만들어야 합니다.
* 리눅스의 경우 이 VHD는 30-50GB 고정 포맷 VHD로 만들어야 합니다.

물리적 크기가 127~128GB 미만이면 VHD를 확장할 수 있어야 합니다(스파스/다이내믹). 제공된 기본 Windows 및 SQL Server 이미지는 이미 이러한 요구 사항을 충족하므로 VHD의 형식이나 크기를 변경하지 마십시오.

데이터 디스크는 1TB이하여야 합니다. 크기를 결정할 때는 고객이 배포 시 이미지 내에서 VH의 크기를 조정할 수 없다는 점을 기억하십시오. 데이터 디스크 VHD는 고정 형식 VHD로 만들어야 합니다. 또한 확장 가능해야 합니다(스파스/동적). 처음에는 데이터 디스크가 비어 있거나 데이터를 포함할 수 있습니다.

### <a name="install-the-most-current-updates"></a>최신 업데이트 설치

운영 체제 VM의 기본 이미지에는 게시된 날짜까지의 최신 업데이트가 포함되어야 합니다. 만든 운영 체제 VHD를 게시하기 전에 모든 최신 보안 및 유지 관리 패치를 통해 OS 및 설치된 모든 서비스를 업데이트해야 합니다.

Windows 서버의 경우 **업데이트 확인** 명령을 실행합니다.

Linux 배포판의 경우 일반적으로 명령줄 도구 또는 그래픽 유틸리티를 통해 업데이트를 다운로드하고 설치합니다. 예를 들어 Ubuntu Linux는 [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) 명령과 [업데이트 관리자](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) 도구를 제공하여 OS를 업데이트합니다.

### <a name="perform-additional-security-checks"></a>추가 보안 검사 수행

Azure 마켓플레이스에서 솔루션 이미지에 대해 높은 수준의 보안을 유지합니다. 다음 문서에서는 [Azure Marketplace 이미지에 대한 보안 권장 사항입니다.](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images) 이러한 권장 사항 중 일부는 Linux 기반 이미지에만 적용되지만, 대부분은 모든 VM 이미지에 적용됩니다.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>사용자 지정 구성 및 예약된 작업 수행

추가 구성이 필요한 경우 시작 시 실행되는 예약된 작업을 사용하여 VM을 배포한 후 최종 변경합니다. 다음 권장 사항도 고려하세요.

* 한 번 실행된 작업인 경우 작업이 성공적으로 완료된 후 자체적으로 삭제해야 합니다.
* 이 두 드라이브만 항상 존재하도록 보장되므로 구성은 C 또는 D 이외의 드라이브에 의존해서는 안 됩니다(드라이브 C는 운영 체제 디스크이고 드라이브 D는 임시 로컬 디스크임).

Linux 사용자 지정에 대한 자세한 내용은 [Linux용 가상 머신 확장 및 기능](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)을 참조하세요.

## <a name="generalize-the-image"></a>이미지 일반화

Azure Marketplace의 모든 이미지는 일반적으로 다시 사용할 수 있어야 합니다. 이를 위해서는 운영 체제 VHD를 일반화해야 하며, 이는 VM에서 모든 인스턴스별 식별자 및 소프트웨어 드라이버를 제거하는 작업입니다.

### <a name="windows"></a>Windows

Windows OS 디스크는 [sysprep 도구](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)를 사용하여 일반화됩니다. 나중에 OS를 업데이트하거나 재구성하면 sysprep을 다시 실행해야 합니다.

> [!WARNING]
> 업데이트가 자동으로 실행될 수 있으므로 sysprep을 실행한 후 배포될 때까지 VM을 끕니다. 이 종료는 운영 체제 또는 설치된 서비스에 대한 인스턴스별 변경을 하지 않도록 합니다. sysprep 실행에 대한 자세한 내용은 [VHD를 일반화하는 단계를](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)참조하십시오.

### <a name="linux"></a>Linux

다음 프로세스는 Linux VM을 일반화하고 별도의 VM으로 다시 배포합니다. 자세한 내용은 [가상 컴퓨터 또는 VHD의 이미지를 만드는 방법을](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)참조하십시오. "캡처한 이미지에서 VM 만들기" 섹션에 도달하면 중지할 수 있습니다.

1. **Azure Linux 에이전트 제거**

    1. SSH 클라이언트를 사용하여 Linux VM에 연결합니다.
    2. SSH 창에서 다음 명령을 `sudo waagent -deprovision+user`입력합니다.
    3. **계속하려면 Y** 유형(확인 단계를 피하기 위해 이전 명령에 **-force** 매개 변수를 추가할 수 있음).
    d. 명령이 완료된 후 **종료를** 입력하여 SSH 클라이언트를 닫습니다.

2. **가상 머신 중지**

    1. Azure 포털에서 리소스 그룹(RG)을 선택하고 VM을 할당 취소합니다.
    2. 이제 VHD가 일반화되어 이 VHD를 사용하여 새 VM을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

새 Azure 기반 VHD를 만드는 데 어려움이 있는 경우 [일반적인 VHD 만들기 문제](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues)를 참조하세요.

그렇지 않은 경우는 다음과 같습니다.

* [VM 이미지를 인증하면](https://docs.microsoft.com/azure/marketplace/partner-center-portal/get-sas-uri) Azure 인증 도구에 *대한 인증 테스트 도구를* 받을 수 있는 위치와 이를 사용하여 VM 이미지를 인증하는 방법을 포함하여 Azure Marketplace 인증을 위한 VM 이미지를 테스트하고 제출하는 방법을 설명합니다.
