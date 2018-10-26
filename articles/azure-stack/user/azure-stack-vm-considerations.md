---
title: 차이점 및 Azure Stack에서 virtual machines에 대 한 고려 사항 | Microsoft Docs
description: Azure Stack에서 virtual machines를 사용 하 여 작업 하는 경우 차이점 및 고려 사항에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.openlocfilehash: cb02ecb06728f5f36a0d51a3ec22cc8ba5cb44e7
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094758"
---
# <a name="considerations-for-using-virtual-machines-in-azure-stack"></a>Azure Stack에서 virtual machines 사용 시 고려 사항

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack virtual machines는 주문형으로 확장 가능한 컴퓨팅 리소스를 제공합니다. 가상 머신 (Vm)를 배포 하기 전에 Azure Stack에서 사용할 수 있는 가상 머신 기능 및 Microsoft Azure의 차이점을 이해 해야 합니다. 이 문서에서는 이러한 차이점을 설명 하 고 가상 머신 배포를 계획 하는 것에 대 한 주요 고려 사항이 식별 합니다. Azure Stack 및 Azure 간의 대략적인 차이 대 한 자세한 내용은 참조는 [고려 사항 키](azure-stack-considerations.md) 문서.

## <a name="cheat-sheet-virtual-machine-differences"></a>치트 시트: 가상 머신 차이점

| 기능 | Azure (전역) | Azure Stack |
| --- | --- | --- |
| 가상 머신 이미지 | Azure Marketplace 가상 컴퓨터를 만드는 데 사용할 수 있는 이미지를 포함 합니다. 참조 된 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) 페이지를 통해 Azure Marketplace에서 사용할 수 있는 이미지 목록을 확인 합니다. | 기본적으로 없습니다 이미지가 사용 가능한 Azure Stack marketplace에서. Azure Stack 클라우드 관리자에 게시 하거나 사용자가 사용할 수 전에 Azure Stack marketplace 이미지를 다운로드 해야 합니다. |
| 가상 머신 크기 | Azure 가상 머신에 대 한 다양 한 크기를 지원합니다. 사용 가능한 크기 및 옵션에 대 한 자세한 참조를 [Windows 가상 머신 크기](../../virtual-machines/virtual-machines-windows-sizes.md) 하 고 [Linux 가상 머신 크기](../../virtual-machines/linux/sizes.md) 항목입니다. | Azure Stack Azure에서 사용할 수 있는 가상 머신 크기의 하위 집합을 지원 합니다. 지원 되는 크기의 목록을 보려면를 참조 합니다 [가상 머신 크기](#virtual-machine-sizes) 이 문서의 섹션입니다. |
| 가상 컴퓨터 할당량 | [할당량 한도](../../azure-subscription-service-limits.md#service-specific-limits) Microsoft에서 설정 됩니다 | 가상 컴퓨터 사용자에 게 제공 하기 전에 Azure Stack 클라우드 관리자가 할당량을 할당 해야 합니다. |
| 가상 머신 확장 |Azure는 다양 한 가상 머신 확장을 지원합니다. 사용 가능한 확장에 대 한 자세한 내용은 참조는 [가상 머신 확장 및 기능](../../virtual-machines/windows/extensions-features.md) 문서.| Azure Stack Azure에서 사용할 수 있는 확장의 하위 집합을 지원 하 고 확장의 각 특정 버전이 있습니다. Azure Stack 클라우드 관리자가 사용자에 게 사용할 수 있는 확장을 선택할 수 있습니다. 지원 되는 확장 목록을 보려면를 참조 합니다 [가상 머신 확장](#virtual-machine-extensions) 이 문서의 섹션입니다. |
| 가상 머신 네트워크 | 테 넌 트 가상 컴퓨터에 할당 된 공용 IP 주소는 인터넷을 통해 액세스할 수 있습니다.<br><br><br>Azure 가상 컴퓨터의 고정된 DNS 이름 | 테 넌 트 가상 컴퓨터에 할당 된 공용 IP 주소는 Azure Stack 개발 키트 환경만 액세스할 수 있습니다. 사용자를 통해 Azure Stack 개발 키트에 액세스할 수 있어야 합니다. [RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) 하거나 [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) Azure Stack에서 만든 가상 머신에 연결 합니다.<br><br>특정 Azure Stack 인스턴스 내에서 만든 가상 컴퓨터는 클라우드 관리자가 구성한 값을 기반으로 DNS 이름이 있어야 합니다. |
| 가상 머신 저장소 | 지원 [디스크를 관리 합니다.](../../virtual-machines/windows/managed-disks-overview.md) | Managed disks는 1808 이상 버전을 사용 하 여 Azure Stack에서 지원 됩니다. |
| 가상 머신 디스크 성능 | 디스크 유형 및 크기에 따라 달라 집니다. | 디스크가 연결 된 VM의 VM 크기에 따라 달라 집니다를 참조 하는 [Azure Stack에서 지원 되는 가상 머신 크기](azure-stack-vm-sizes.md) 문서.
| API 버전 | Azure는 항상 모든 가상 머신 기능에 대 한 최신 API 버전을 가집니다. | Azure Stack 이러한 서비스에 대 한 특정 Azure 서비스 및 특정 API 버전을 지원합니다. 지원 되는 API 버전의 목록을 보려면를 참조 합니다 [API 버전](#api-versions) 이 문서의 섹션입니다. |
|가상 컴퓨터 가용성 집합|여러 장애 도메인 (2 또는 3 지역당)<br>여러 업데이트 도메인<br>관리 되는 디스크 지원|여러 장애 도메인 (2 또는 3 지역당)<br>여러 업데이트 도메인 (최대 20)<br>관리 디스크 지원 되지 않습니다|
|가상 머신 크기 집합|자동 크기 조정 지원|자동 크기 조정 지원 되지 않습니다.<br>확장 집합 포털, Resource Manager 템플릿 또는 PowerShell을 사용 하 여에 더 많은 인스턴스를 추가 합니다.

## <a name="virtual-machine-sizes"></a>가상 머신 크기

Azure Stack에서 리소스 제한을 (server 로컬 및 서비스 수준입니다.) 리소스의 사용 방지 이러한 한도 다른 테 넌 트 리소스 사용의 영향을 줄여 테 넌 트 환경을 개선 합니다.

- VM에서 네트워크 송신에 대 한 위치에 대역폭 caps는. Azure Stack에서 caps 용량을 Azure에서와 동일합니다.
- Azure Stack 저장소 리소스에 대 한 저장소 액세스에 대 한 테 넌 트에서 리소스의 기본 믿지 하지 않으려면 저장소 IOPS 한도 구현 합니다.
- 여러 개의 연결 된 데이터 디스크가 있는 Vm에 대 한 각 데이터 디스크의 최대 처리량은 500 IOPS Hdd 및 Ssd에 대 한 IOPS는 2300까지 합니다.

다음 표에서 해당 구성과 함께 Azure Stack에서 지원 되는 Vm을 나열 합니다.

| type           | 크기          | 지원 되는 크기 범위 |
| ---------------| ------------- | ------------------------ |
|범용 가상 컴퓨터 |Basic A        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|범용 가상 컴퓨터 |표준 A     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|범용 가상 컴퓨터 |D 시리즈       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|범용 가상 컴퓨터 |Dv2 시리즈     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|범용 가상 컴퓨터 |DS 시리즈      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|범용 가상 컴퓨터 |DSv2 시리즈    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|메모리에 최적화|D 시리즈       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|메모리에 최적화|DS 시리즈      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|메모리에 최적화|Dv2 시리즈     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|메모리에 최적화|DSv2 시리즈-  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

가상 머신 크기 및 해당 관련된 리소스 수량에는 Azure Stack 및 Azure 간에 일관 됩니다. 메모리, 코어 수 및 만들 수 있는 데이터 디스크의 수/크기의 양을 포함 됩니다. 그러나 동일한 크기를 사용 하 여 Vm 성능을 특정 Azure Stack 환경의 기본 특성에 따라 달라 집니다.

## <a name="virtual-machine-extensions"></a>가상 머신 확장

 Azure Stack에는 작은 확장 집합이 포함 되어 있습니다. 업데이트 및 추가 확장 Marketplace 배포를 통해 사용할 수 있습니다.

다음 PowerShell 스크립트를 사용 하 여 Azure Stack 환경에서 사용할 수 있는 가상 머신 확장의 목록을 가져옵니다.

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

## <a name="api-versions"></a>API 버전

Azure Stack에서 가상 머신 기능에는 다음 API 버전을 지원합니다.

![VM 리소스 종류](media/azure-stack-vm-considerations/vm-resoource-types.png)

Azure Stack 환경에서 사용할 수 있는 가상 머신 기능에 대 한 API 버전을 가져오려면 다음 PowerShell 스크립트를 사용할 수 있습니다.

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```

클라우드 운영자는 Azure Stack 환경과 최신 버전으로 업데이트 하는 경우 지원 되는 리소스 종류 및 API 버전의 목록은 달라질 수 있습니다.

## <a name="windows-activation"></a>Windows 정품 인증

Windows 제품이 제품 사용권 및 Microsoft 사용 조건에 따라 사용 되어야 합니다. 다음을 사용 하 여 azure Stack [자동 VM 정품 인증](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) AVMA () Windows Server virtual machines (Vm)를 활성화 합니다.

- Azure Stack 호스트는 Windows Server 2016 용 AVMA 키를 사용 하 여 Windows를 활성화합니다. Windows Server 2012를 실행 하거나 나중에 자동으로 활성화 되는 모든 Vm입니다.
- Vm을 실행된 하는 Windows Server 2008 R2 자동으로 활성화 되지 않으면 사용 하 여 활성화 해야 합니다 [MAK 정품 인증](https://technet.microsoft.com/library/ff793438.aspx)합니다. MAK 정품 인증을 사용 하려면 고유한 제품 키를 제공 해야 합니다.

Microsoft Azure Windows Vm을 활성화 하려면 KMS 정품 인증을 사용 합니다. Azure Stack에서 발생 하 고 Azure로 VM을 활성화 문제를 이동 하는 경우 참조 [문제 해결 Azure Windows 가상 머신 정품 인증 문제](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems)합니다. 추가 정보를 찾을 수 있습니다 합니다 [Azure Vm에서 문제 해결 Windows 정품 인증 오류](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) Azure 지원 팀 블로그 게시물.

## <a name="next-steps"></a>다음 단계

[Azure Stack에서 PowerShell을 사용 하 여 Windows 가상 머신 만들기](azure-stack-quick-create-vm-windows-powershell.md)
