---
title: Azure Stack 가상 머신 소개
description: Azure Stack virtual machines에 알아보기
services: azure-stack
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: cd42a347de236de2e3374d7ac854779f4c222a00
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768149"
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Azure Stack 가상 머신 소개

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack은 주문형이 고 확장 가능한 컴퓨팅 리소스의 형식으로 가상 머신 (Vm)를 제공합니다. 계산 환경을 통해 더 많이 제어 해야 하는 경우 VM을 선택할 수 있습니다. 이 문서에서는 첫 번째 VM을 만들기 전에 세부 정보를 제공 합니다.

Azure Stack VM 클러스터 또는 개별 컴퓨터를 관리할 필요가 없는 가상화의 유연성을 제공 합니다. 그러나 여전히 해야 구성, 패치 및에서 실행 하는 소프트웨어를 설치 하는 등의 작업을 수행 하 여 VM을 유지 합니다.

여러 가지 방법으로 Azure Stack virtual machines를 사용할 수 있습니다. 예: 

- **개발 및 테스트**  
    Azure Stack Vm을 사용 하 여 코드에 필요한 특정 구성을 사용 하 여 컴퓨터를 만들고 응용 프로그램을 테스트할 수 있습니다.

- **클라우드에서 응용 프로그램**  
    응용 프로그램에 대 한 수요 변동할 수 있으므로 Azure Stack에서 VM에서 실행 하는 것이 경제적 해야 합니다. 필요 하 고 그렇지 않은 경우에 종료 하도록 하는 경우 추가 Vm에 대 한 지불 합니다.

- **데이터 센터 확장된**  
    Azure Stack virtual network에 가상 컴퓨터 또는 Azure 나 조직의 네트워크에 연결할 수 있습니다.

응용 프로그램에 사용을 확장 하거나 요구를 충족 하는 데 필요한 확장 수 있는 Vm.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>VM을 만들기 전에 고려해 야 하나요

항상 디자인 고려 사항이 Azure Stack에서 응용 프로그램 인프라를 구축 하는 경우 있습니다. VM의 이러한 측면은 인프라를 만들기를 시작 하기 전에 고려해 야 하는 것이 중요 합니다.

- 응용 프로그램 리소스의 이름입니다.
- VM의 크기입니다.
- 만들 수 있는 Vm의 최대 수입니다.
- VM에서 실행 되는 운영 체제입니다.
- 시작 된 후 VM의 구성입니다.
- VM에 필요한 관련된 리소스입니다.

### <a name="naming"></a>이름 지정

가상 컴퓨터에 할당 된 이름 및 운영 체제의 일부로 구성 된 컴퓨터 이름으로 바꿉니다. VM 이름은 최대 15자로 제한됩니다.

Azure Stack을 사용 하 여 운영 체제 디스크를 만드는 경우 컴퓨터 이름과 가상 머신 이름은 동일 합니다. 업로드 하 고 이전에 구성 된 운영 체제를 포함 하는 사용자 고유의 이미지 사용을 사용 하 여 가상 컴퓨터를 만들 경우 이름은 다를 수 있습니다. 사용자 고유의 이미지 파일을 업로드 하는 경우 모범 사례로 해야 컴퓨터 이름을 운영 체제 및 가상 머신 이름을 동일 합니다.

### <a name="vm-size"></a>VM 크기

사용 하는 VM의 크기는 실행 하려는 워크 로드에 따라 결정 됩니다. 그런 다음 선택하는 크기는 처리 성능, 메모리 및 저장소 용량 등의 요소를 결정합니다. Azure Stack은 다양 한 다양 한 유형의 사용을 지원 하기 위해 크기를 제공 합니다.

### <a name="vm-limits"></a>VM 제한

구독에 프로젝트에 대 한 Vm의 배포 영향을 줄 수 있는 기본 할당량 한도가 있습니다. 구독별 기준으로 현재 제한은 지역당 20대의 VM입니다.

### <a name="operating-system-disks-and-images"></a>운영 체제 디스크 및 이미지

가상 머신은 VHD(가상 하드 디스크)를 사용하여 해당 OS(운영 체제) 및 데이터를 저장합니다. VHD는 OS를 설치하도록 선택할 수 있는 이미지에도 사용됩니다. Azure Stack 다양 한 버전 및 운영 체제의 종류를 사용 하는 marketplace를 제공 합니다. Marketplace 이미지는 이미지 게시자, 제품, SKU 및 버전으로 식별 됩니다 (일반적으로 지정 된 버전으로 **최신**.)

다음 표에서 이미지에 대 한 정보를 찾는 방법을 보여 줍니다.

|방법|설명|
|---------|---------|
|Azure Stack 포털|사용할 이미지를 선택할 때 사용자에 적합한 값이 자동으로 지정됩니다.|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|REST API     |[이미지 게시자 나열](/rest/api/compute/platformimages/platformimages-list-publishers)<br>[이미지 제안 나열](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[이미지 Sku 나열](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

업로드 하 고 사용자 고유의 이미지를 사용 하도록 선택할 수 있습니다. 이렇게 하면 게시자 이름, 제품 및 SKU 사용 되지 않습니다.

### <a name="extensions"></a>확장

VM 확장 배포 후 구성 및 자동화 된 작업을 통해 VM 추가 기능을 제공합니다.
다음과 같은 일반 작업은 확장을 사용하여 수행할 수 있습니다.

- **사용자 지정 스크립트를 실행 합니다.**  
    사용자 지정 스크립트 확장을 사용 하면 VM 프로 비전 될 때 스크립트를 실행 하 여 VM에서 워크 로드를 구성 하도록 도와줍니다.

- **구성 배포 및 관리**  
    PowerShell Desired State Configuration (DSC) 확장 구성과 환경을 관리 하는 VM에서 DSC를 설정할 수 있습니다.

- **진단 데이터 수집**  
    Azure 진단 확장 응용 프로그램의 상태를 모니터링 데 사용할 수 있는 진단 데이터를 수집 하도록 VM을 구성할 수 있습니다.

### <a name="related-resources"></a>관련 리소스

다음 표에 나와 있는 리소스는 VM에 사용 되며 이미 존재 하거나 VM을 만들 때 만들 수 있습니다:

|리소스|필수|설명|
|---------|---------|---------|
|리소스 그룹|예|VM은 리소스 그룹에 포함되어야 합니다.|
|Storage 계정|아닙니다.|VM에 Managed Disks를 사용 하는 경우 해당 가상 하드 디스크를 저장 하려면 저장소 계정이 필요 하지 않습니다. <br>VM에는 관리 되지 않는 디스크를 사용 하는 경우 해당 가상 하드 디스크를 저장 하려면 저장소 계정이 필요지 않습니다.|
|가상 네트워크|예|VM은 가상 네트워크의 구성원이어야 합니다.|
|공용 IP 주소|아닙니다.|원격으로 액세스하기 위해 VM에 할당된 공용 IP 주소가 있을 수 있습니다.|
|Linux|예|네트워크에서 통신하기 위해 VM에 네트워크 인터페이스가 필요합니다.|
|데이터 디스크|아닙니다.|VM은 저장소 기능을 확장하기 위해 데이터 디스크를 포함할 수 있습니다.|

## <a name="create-your-first-vm"></a>첫 번째 VM 만들기

VM을 만드는 몇 가지 옵션이 있습니다. 선택한 환경에 따라 달라 집니다. 다음 표에서 VM을 만들기 시작 하는 데는 정보를 제공 합니다.

|방법|문서|
|---------|---------|
|Azure Stack 포털|Azure Stack 포털을 사용 하 여 Windows 가상 머신 만들기<br>[Azure Stack 포털을 사용 하 여 Linux 가상 머신 만들기](azure-stack-quick-linux-portal.md)|
|템플릿|Azure Stack 빠른 시작 템플릿 위치에 있습니다.<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Azure Stack에서 PowerShell을 사용 하 여 Windows 가상 머신 만들기](azure-stack-quick-create-vm-windows-powershell.md)<br>[Azure Stack에서 PowerShell을 사용 하 여 Linux 가상 머신 만들기](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[Azure Stack에서 CLI를 사용 하 여 Windows 가상 머신 만들기](azure-stack-quick-create-vm-windows-cli.md)<br>[Azure Stack에서 CLI를 사용 하 여 Linux 가상 머신 만들기](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>VM 관리

스크립팅 또는 Api를 통해 직접 지 원하는 명령줄 도구는 브라우저 기반 포털을 사용 하 여 Vm을 관리할 수 있습니다. 수행할 수 있는 몇 가지 일반적인 관리 작업입니다.

- VM에 대 한 정보 가져오기
- VM에 연결
- 가용성 관리
- 백업

### <a name="get-information-about-your-vm"></a>VM에 대 한 정보 가져오기

다음 표에서 보여 줍니다 일부의 VM에 대 한 정보를 가져올 수 있습니다.

|방법|설명|
|---------|---------|
|Azure Stack 포털|허브 메뉴에서 가상 머신을 클릭 하 고 목록에서 VM을 선택 합니다. VM에 대 한 페이지의 값을 설정 하 고 모니터링 되는 메트릭 개요 정보에 액세스할을 수 있습니다.|
|Azure PowerShell|Vm을 관리 하는 것에서 Azure 및 Azure Stack는 것과 비슷합니다. PowerShell을 사용 하는 방법에 대 한 자세한 내용은 다음 Azure 항목을 참조 합니다.<br>[만들기 및 Azure PowerShell 모듈을 사용 하 여 Windows Vm 관리](../../virtual-machines/windows/tutorial-manage-vm.md#understand-vm-sizes)|
|클라이언트 SDK|Vm을 관리 C#을 사용한 Azure 및 Azure Stack에서 비슷합니다. 자세한 내용은 Azure 항목을 참조 합니다.<br>[C#을 사용 하 여 Azure에서 Windows Vm 만들기 및 관리](../../virtual-machines/windows/csharp.md)|

### <a name="connect-to-your-vm"></a>VM에 연결

사용할 수는 **Connect** VM에 연결할 Azure Stack 포털에는 단추입니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack에서 Virtual Machines에 대 한 고려 사항](azure-stack-vm-considerations.md)
