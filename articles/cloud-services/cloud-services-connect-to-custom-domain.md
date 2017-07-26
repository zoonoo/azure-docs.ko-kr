---
title: "클라우드 서비스를 사용자 지정 도메인 컨트롤러에 연결 | Microsoft Docs"
description: "PowerShell 및 AD 도메인 확장을 사용하여 사용자 지정 AD 도메인에 웹/작업자 역할을 연결하는 방법을 알아봅니다."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 1e2d7c87-d254-4e7a-a832-67f84411ec95
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: bc7e9a53f71eff828eaf8c45c104c5dc73018824
ms.contentlocale: ko-kr
ms.lasthandoff: 05/25/2017

---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Azure Cloud Services 역할을 Azure에서 호스팅되는 사용자 지정 AD 도메인 컨트롤러에 연결
먼저 Azure에서 Virtual Network(VNet)를 설정합니다. 그런 다음 VNet에 Active Directory 도메인 컨트롤러(Azure 가상 컴퓨터에서 호스팅되는)를 추가합니다. 그런 다음, 기존 클라우드 서비스 역할을 사전에 만든 VNet에 추가한 후 도메인 컨트롤러에 연결합니다.

시작하기 전에 다음 몇 가지를 유의하십시오.

1. 이 자습서에서는 PowerShell을 사용하므로, Azure PowerShell이 설치되어 있고 사용할 수 있는지 확인하세요. Azure PowerShell 설정에 대한 도움을 얻으려면 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.
2. 사용자의 AD 도메인 컨트롤러와 웹/작업자 역할 인스턴스는 VNet에 있어야 합니다.

이 단계별 가이드를 따르고 문제가 발생하는 경우 문서 끝에 의견을 남겨 주세요. 담당자가 연락드립니다(물론, 저희는 여러분의 의견을 확인합니다.).

클라우드 서비스에 의해 참조되는 네트워크는 **클래식 Virtual Network**여야 합니다.

## <a name="create-a-virtual-network"></a>Virtual Network 만들기
Azure Portal 또는 PowerShell을 사용하여 Azure에서 Virtual Network를 만들 수 있습니다. 이 자습서에서는 PowerShell을 사용합니다. Azure Portal을 사용하여 Virtual Network를 만들려면 [Virtual Network 만들기](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)를 참조하세요.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>가상 컴퓨터 만들기
Virtual Network 설정을 완료한 후에 AD 도메인 컨트롤러를 만들어야 합니다. 이 자습서는 Azure 가상 컴퓨터에서 AD 도메인 컨트롤러를 설정합니다.

이 작업을 수행하려면 다음 명령을 사용하여 PowerShell을 통해 가상 컴퓨터를 만듭니다.

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>가상 컴퓨터를 도메인 컨트롤러로 승격
가상 컴퓨터를 AD 도메인 컨트롤러로 구성하려면 VM에 로그인하여 구성해야 합니다.

VM에 로그인하려면 PowerShell을 통해 RDP 파일을 가져올 수 있으며 다음 명령을 사용합니다.

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

VM에 로그인한 후 [고객 AD 도메인 컨트롤러 설치 방법에 대한 단계별 가이드](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx)에 따라 가상 컴퓨터를 AD 도메인 컨트롤러로 설정합니다.

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Virtual Network에 클라우드 서비스 추가
그런 다음 새 VNet에 클라우드 서비스 배포를 추가해야 합니다. 이 작업을 수행하려면 Visual Studio 또는 원하는 편집기를 사용하여 cscfg에 관련 섹션을 추가하여 클라우드 서비스 cscfg를 수정합니다.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

그런 다음 Cloud Services 프로젝트를 빌드하고 Azure에 배포합니다. 서비스 패키지를 Azure에 클라우드 배포에 대한 도움을 얻으려면 [Cloud Services 만들기 및 배포 방법](cloud-services-how-to-create-deploy.md#how-to-deploy-a-cloud-service)을 참조하세요.

## <a name="connect-your-webworker-roles-to-the-domain"></a>웹/작업자 역할을 도메인에 연결
Azure에서 클라우드 서비스 프로젝트가 배포되면 AD 도메인 확장명을 사용하여 사용자의 역할 인스턴스가 사용자 지정 AD 도메인에 연결합니다. AD 도메인 확장명을 기존 Cloud Services 배포에 추가하고 사용자 지정 도메인에 가입하려면 PowerShell에서 다음 명령을 실행합니다.

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

지금까지 전반적인 내용을 알아보았습니다.

Cloud Services가 사용자 지정 도메인 컨트롤러에 가입되어 있어야 합니다. AD 도메인 확장을 구성하는 방법에 대해 사용할 수 있는 다른 옵션에 대한 자세한 내용을 보려면 PowerShell 도움말을 사용합니다. 다음은 몇 가지 예제입니다.

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```

