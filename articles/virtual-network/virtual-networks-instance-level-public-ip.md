<properties 
   pageTitle="인스턴스 수준 공용 IP(ILPIP)"
   description="ILPIP(PIP) 및 관리 방법 이해"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# 인스턴스 수준 공용 IP(ILPIP) 개요
인스턴스 수준 공용 IP(ILPIP)는 해당 VM 또는 역할 인스턴스가 상주하는 클라우드 서비스가 아닌 VM 또는 역할 인스턴스에 직접 할당할 수 있는 IP 주소입니다. 클라우드 서비스에 할당된 VIP(가상 IP)의 위치를 차지하지 않습니다. VM 또는 역할 인스턴스에 직접 연결을 사용할 수 있는 추가 IP 주소입니다.

>[AZURE.NOTE]과거에는 ILPIP를 PIP라고 했으며, 공용 IP를 나타냅니다.

![ILPIP 및 VIP 간의 차이](./media/virtual-networks-instance-level-public-ip/Figure1.png)

그림 1에 표시된 것과 같이, 클라우드 서비스는 VIP를 사용하여 액세스하지만 개별 VM은 보통 VIP:&lt;포트 번호&gt;를 사용하여 액세스됩니다. 특정 VM에 ILPIP를 할당하면 해당 VM은 해당 IP 주소를 사용하여 직접 액세스할 수 있습니다.

Azure에서 클라우드 서비스를 만들면 해당 DNS A 레코드가 자동으로 만들어져, 실제 VIP를 사용하지 않고 정규화된 도메인 이름(FQDN)을 통해 서비스에 액세스할 수 있습니다. 동일한 프로세스가 ILPIP에 대해 발생하며 ILPIP 대신 FQDN으로 VM 또는 역할 인스턴스에 액세스할 수 있습니다.


>[AZURE.NOTE]각 VM 또는 역할 인스턴스에 대해 하나의 ILPIP를 할당할 수 있습니다. 구독 당 최대 5개의 ILPIP를 사용할 수 있습니다. 이때 ILPIP는 다중 NIC VM에 대해 지원되지 않습니다.

## ILPIP를 요청해야 하는 이유
클라우드 서비스 VIP:&lt;포트 번호&gt;를 사용하지 않고 직접 할당된 IP 주소로 VM 또는 역할 인스턴스에 연결할 수 있게 하려면, VM 또는 역할 인스턴스에 ILPIP를 요청합니다. **수동 FTP** - VM에 ILPIP를 지정하여, 모든 포트에서 트래픽을 받을 수 있으며, 트래픽을 수신할 끝점을 열지 않아도 됩니다. 이 포트를 동적으로 선택한 수동 FTP와 같은 시나리오를 통해 사용됩니다. **아웃 바운드 IP** - VM에서 발생하는 아웃바운드 트래픽은 소스로 ILPIP와 함께 보내지며 외부 엔터티에 대한 VM을 고유하게 식별합니다.

>[AZURE.NOTE]예약된 ILPIP를 사용하면 추가 Azure 구독에 대한 비용이 발생할 수 있습니다. ILPIP 가격에 대한 자세한 내용은 [IP 주소 가격](http://azure.microsoft.com/pricing/details/ip-addresses/)을 참조하세요.

## VM 생성 중 ILPIP를 요청하는 방법
아래의 PowerShell 스크립트는 *FTPService*라는 새 클라우드 서비스를 만든 다음, Azure에서 이미지를 검색 하고 검색된 이미지를 사용하여 *FTPInstance*라는 VM을 만들고, ILPIP를 사용하도록 VM을 설정하고 VM을 새 서비스에 추가합니다.

	New-AzureService -ServiceName FTPService -Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## VM에 대한 ILPIP 정보를 검색하는 방법
위의 스크립트를 사용하여 만든 VM에 대한 ILPIP 정보를 보려면, 다음 PowerShell 명령을 실행하고 *PublicIPAddress* 및 *PublicIPName*의 값을 확인합니다.

	Get-AzureVM -Name FTPInstance -ServiceName FTPService

	DeploymentName              : FTPService
	Name                        : FTPInstance
	Label                       : 
	VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
	InstanceStatus              : ReadyRole
	IpAddress                   : 100.74.118.91
	InstanceStateDetails        : 
	PowerState                  : Started
	InstanceErrorCode           : 
	InstanceFaultDomain         : 0
	InstanceName                : FTPInstance
	InstanceUpgradeDomain       : 0
	InstanceSize                : Small
	HostName                    : FTPInstance
	AvailabilitySetName         : 
	DNSName                     : http://ftpservice888.cloudapp.net/
	Status                      : ReadyRole
	GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
	ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
	PublicIPAddress             : 104.43.142.188
	PublicIPName                : ftpip
	NetworkInterfaces           : {}
	ServiceName                 : FTPService
	OperationDescription        : Get-AzureVM
	OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
	OperationStatus             : OK

## VM에서 ILPIP를 제거하는 방법
위의 스크립트에서 VM에 추가된 ILPIP를 제거하려면 다음 PowerShell 명령을 실행합니다.
	
	Get-AzureVM -ServiceName FTPService -Name FTPInstance `
	| Remove-AzurePublicIP `
	| Update-AzureVM

## 기존 VM에 ILPIP를 추가하는 방법
위의 스크립트를 사용하여 만든 VM에 ILPIP를 추가하려면, 다음 명령을 실행합니다.

	Get-AzureVM -ServiceName FTPService -Name FTPInstance `
	| Set-AzurePublicIP -PublicIPName ftpip2 `
	| Update-AzureVM

## 서비스 구성 파일을 사용하여 VM에 ILPIP를 연결하는 방법
서비스 구성(CSCFG) 파일을 사용하여 VM에 ILPIP를 연결할 수도 있습니다. 아래 샘플 xml에서는 *MyReservedIP*라는 예약된 IP를 역할 인스턴스에 대한 ILPIP로 사용하도록 클라우드 서비스를 구성하는 방법을 보여줍니다.
	
	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
	  <Role name="WebRole1">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
	    </ConfigurationSettings>
	  </Role>
      <NetworkConfiguration>
	    <VirtualNetworkSite name="VNet"/>
	    <AddressAssignments>
	      <InstanceAddress roleName="VMRolePersisted">
	        <Subnets>
	          <Subnet name="Subnet1"/>
	          <Subnet name="Subnet2"/>
	        </Subnets>
	        <PublicIPs>
	          <PublicIP name="MyReservedIP" domainNameLabel="MyReservedIP" />
	        </PublicIPs>
	      </InstanceAddress>
	    </AddressAssignments>
	  </NetworkConfiguration>
	</ServiceConfiguration>

## 참고 항목

[예약된 개인 IP(DIP)](../virtual-networks-reserved-private-ip)

[예약된 공용 IP](../virtual-networks-reserved-public-ip)

[가상 네트워크 개요](https://msdn.microsoft.com/library/azure/jj156007.aspx)

[예약된 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)

<!---HONumber=58--> 