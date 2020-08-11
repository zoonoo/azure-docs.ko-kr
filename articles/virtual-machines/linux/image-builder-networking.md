---
title: Azure 이미지 작성기 서비스 네트워킹 옵션
description: Azure VM 이미지 작성기 서비스를 배포할 때 네트워킹 옵션 이해
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: dfd0929ea03cd99033482f71579e91aaf6fc131c
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068244"
---
# <a name="azure-image-builder-service-networking-options"></a>Azure 이미지 작성기 서비스 네트워킹 옵션

기존 VNET을 사용 하거나 사용 하지 않고 Azure 이미지 작성기를 배포 하도록 선택 해야 합니다.

## <a name="deploy-without-specifying-an-existing-vnet"></a>기존 VNET을 지정 하지 않고 배포

기존 VNET을 지정 하지 않으면 Azure 이미지 작성기는 스테이징 리소스 그룹에 VNET 및 서브넷을 만듭니다. 공용 IP 리소스는 네트워크 보안 그룹에서 Azure 이미지 작성기 서비스로의 인바운드 트래픽을 제한 하는 데 사용 됩니다. 공용 IP는 이미지 빌드 중에 Azure 이미지 작성기 명령에 대 한 채널을 용이 하 게 합니다. 빌드가 완료 되 면 VM, 공용 IP, 디스크 및 VNET이 삭제 됩니다. 이 옵션을 사용 하려면 VNET 속성을 지정 하지 마십시오.

## <a name="deploy-using-an-existing-vnet"></a>기존 VNET을 사용 하 여 배포

VNET 및 서브넷을 지정 하는 경우 Azure 이미지 작성기는 선택한 VNET에 빌드 VM을 배포 합니다. VNET에서 액세스할 수 있는 리소스에 액세스할 수 있습니다. 다른 VNET에 연결 되지 않은 사일로 VNET을 만들 수도 있습니다. VNET을 지정 하는 경우 Azure 이미지 작성기는 공용 IP 주소를 사용 하지 않습니다. Azure Image Builder 서비스에서 빌드 VM으로의 통신은 Azure 개인 링크 기술을 사용 합니다.

자세한 내용은 다음 예제 중 하나를 참조 하세요.

* [Windows Vm 용 Azure 이미지 작성기를 사용 하 여 기존 Azure VNET에 액세스 허용](../windows/image-builder-vnet.md)
* [Linux Vm에 Azure 이미지 작성기를 사용 하 여 기존 Azure VNET에 액세스 허용](image-builder-vnet.md)

### <a name="what-is-azure-private-link"></a>Azure Private Link란?

Azure 개인 링크는 가상 네트워크에서 Azure PaaS (platform as a service), 고객 소유 또는 Microsoft 파트너 서비스로 개인 연결을 제공 합니다. 네트워크 아키텍처를 간소화 하 고 공용 인터넷에 대 한 데이터 노출을 제거 하 여 Azure에서 끝점 간의 연결을 보호 합니다. 자세한 내용은 [개인 링크 설명서](https://docs.microsoft.com/azure/private-link)를 참조 하세요.

### <a name="required-permissions-for-an-existing-vnet"></a>기존 VNET에 대 한 필수 권한

Azure 이미지 작성기에서 기존 VNET을 사용 하려면 특정 권한이 필요 합니다. 자세한 내용은 [Azure CLI를 사용 하 여 Azure 이미지 작성기 서비스 권한 구성](image-builder-permissions-cli.md) 또는 [PowerShell을 사용 하 여 Azure 이미지 작성기 서비스 권한 구성](image-builder-permissions-powershell.md)을 참조 하세요.

### <a name="what-is-deployed-during-an-image-build"></a>이미지 빌드 중에 배포 되는 항목

기존 VNET을 사용 하는 경우 Azure 이미지 작성기는 추가 VM (프록시 VM) 및 개인 링크에 연결 된 Azure Load Balancer (ALB)를 배포 합니다. AIB 서비스의 트래픽은 ALB에 대 한 개인 링크를 통해 이동 합니다. ALB는 Linux OS의 경우 포트 60001을 사용 하 고 Windows OS의 경우 6만를 사용 하 여 프록시 VM과 통신 합니다. 프록시는 Linux OS의 경우 포트 22, Windows OS의 경우 5986을 사용 하 여 빌드 VM에 명령을 전달 합니다.

> [!NOTE]
> VNET은 Azure 이미지 빌더 서비스 지역과 동일한 지역에 있어야 합니다.
> 

### <a name="why-deploy-a-proxy-vm"></a>프록시 VM을 배포 하는 이유

공용 IP가 없는 VM이 내부 Load Balancer 뒤에 있으면 인터넷에 액세스할 수 없습니다. VNET에 사용 되는 Azure Load Balancer는 내부입니다. 프록시 VM은 빌드 중에 빌드 VM에 대 한 인터넷 액세스를 허용 합니다. 연결 된 네트워크 보안 그룹을 사용 하 여 빌드 VM 액세스를 제한할 수 있습니다.

배포 된 프록시 VM 크기는 빌드 VM 외에도 표준 A1_v2입니다. 프록시 VM은 Azure 이미지 작성기 서비스와 빌드 VM 간에 명령을 전송 하는 데 사용 됩니다. 크기 또는 OS를 포함 하 여 프록시 VM 속성을 변경할 수 없습니다. Windows 및 Linux 이미지 빌드 모두에 대 한 프록시 VM 속성을 변경할 수 없습니다.

### <a name="image-template-parameters-to-support-vnet"></a>VNET을 지원 하기 위한 이미지 템플릿 매개 변수
```json
"VirtualNetworkConfig": {
        "name": "",
        "subnetName": "",
        "resourceGroupName": ""
        },
```

| 설정 | Description |
|---------|---------|
| name | 필드 기존 가상 네트워크의 이름입니다. |
| subnetName | 지정 된 가상 네트워크 내에 있는 서브넷의 이름입니다. *Name* 이 지정 된 경우에만 지정 해야 합니다. |
| resourceGroupName | 지정 된 가상 네트워크를 포함 하는 리소스 그룹의 이름입니다. *Name* 이 지정 된 경우에만 지정 해야 합니다. |

개인 링크 서비스에는 지정 된 VNET 및 서브넷의 IP가 필요 합니다. 현재 Azure는 이러한 Ip에서 네트워크 정책을 지원 하지 않습니다. 따라서 서브넷에서 네트워크 정책을 사용 하지 않도록 설정 해야 합니다. 자세한 내용은 [개인 링크 설명서](https://docs.microsoft.com/azure/private-link)를 참조 하세요.

### <a name="checklist-for-using-your-vnet"></a>VNET을 사용 하기 위한 검사 목록

1. ALB (Azure Load Balancer)에서 NSG의 프록시 VM과 통신할 수 있도록 허용
    * [AZ CLI 예제](image-builder-vnet.md#add-network-security-group-rule)
    * [PowerShell 예제](../windows/image-builder-vnet.md#add-network-security-group-rule)
2. 서브넷에서 개인 서비스 정책 사용 안 함
    * [AZ CLI 예제](image-builder-vnet.md#disable-private-service-policy-on-subnet)
    * [PowerShell 예제](../windows/image-builder-vnet.md#disable-private-service-policy-on-subnet)
3. Azure 이미지 작성기에서 ALB을 만들고 VNET에 Vm을 추가 하도록 허용 합니다.
    * [AZ CLI 예제](image-builder-permissions-cli.md#existing-vnet-azure-role-example)
    * [PowerShell 예제](image-builder-permissions-powershell.md#permission-to-customize-images-on-your-vnets)
4. Azure 이미지 작성기에서 원본 이미지를 읽고 쓰고 이미지를 만들 수 있도록 허용
    * [AZ CLI 예제](image-builder-permissions-cli.md#custom-image-azure-role-example)
    * [PowerShell 예제](image-builder-permissions-powershell.md#custom-image-azure-role-example)
5. Azure 이미지 빌더 서비스 지역과 동일한 지역에서 VNET을 사용 하 고 있는지 확인 합니다.


## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure 이미지 작성기 개요](image-builder-overview.md)를 참조 하세요.