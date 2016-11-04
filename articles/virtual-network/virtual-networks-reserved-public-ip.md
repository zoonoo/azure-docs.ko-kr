---
title: 예약된 IP | Microsoft Docs
description: 예약된 IP 및 관리 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial

---
# 예약된 IP 개요
Azure의 IP 주소는 두 범주 즉, 동적 IP 및 예약된 IP로 나뉩니다. Azure에서 관리하는 공용 IP 주소는 기본적으로 동적입니다. 즉, 지정된 클라우드 서비스에 사용되는 IP 주소(VIP) 또는 VM이나 역할 인스턴스에 직접 액세스하는 데 사용되는 IP 주소(ILPIP)는 리소스가 종료 또는 할당 취소된 경우 때때로 변경될 수 있습니다.

IP 주소의 변경을 방지하기 위해 IP 주소를 예약할 수 있습니다. 예약된 IP는 VIP로만 사용할 수 있으므로 리소스가 종료 또는 할당 취소된 때에도 클라우드 서비스의 IP 주소가 동일하게 유지됩니다. 또한 VIP로 사용하는 기존의 동적 IP를 예약된 IP 주소로 변환할 수 있습니다.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[Resource Manager 배포 모델](virtual-network-ip-addresses-overview-arm.md)을 사용하여 고정 공용 IP 주소를 예약하는 방법을 알아봅니다.

Azure에서 [IP 주소](virtual-network-ip-addresses-overview-classic.md)가 어떻게 작동하는지 이해해야 합니다.

## 예약된 IP는 언제 필요한가요?
* **IP가 구독에서 예약되어 있는지 확인하려고 합니다**. 어떤 상황에서도 구독에서 해제되지 않을 IP 주소를 예약하려는 경우 예약된 공용 IP를 사용해야 합니다.
* **중지 상태 또는 할당 취소 상태(VM)에서도 IP를 클라우드 서비스에서 유지하려고 합니다**. 클라우드 서비스의 VM이 중지 또는 할당 취소된 경우에도 변경되지 않는 IP 주소를 사용하여 서비스에 액세스할 수 있도록 설정하기를 원합니다.
* **Azure의 아웃바운드 트래픽이 예측 가능한 IP 주소를 사용하는지 확인하려고 합니다**. 특정 IP 주소의 트래픽만 허용하도록 온-프레미스 방화벽을 구성할 수 있습니다. IP를 예약하면 원본 IP 주소를 알고 있으므로 IP 변경 때문에 방화벽 규칙을 업데이트할 필요가 없습니다.

## FAQ
1. 모든 Azure 서비스에 예약된 IP를 사용할 수 있나요?
   * 예약된 IP는 VM 및 VIP를 통해 노출되는 클라우드 서비스 인스턴스 역할에만 사용할 수 있습니다.
2. 예약된 IP를 몇 개까지 사용할 수 있나요?
   * 현재 모든 Azure 구독은 20개의 예약된 IP를 사용할 권한이 있습니다. 그러나 예약된 IP를 추가로 요청할 수 있습니다. 자세한 내용은 [구독 및 서비스 제한](../azure-subscription-service-limits.md) 페이지를 참조하세요.
3. 예약된 IP는 사용 요금이 있나요?
   * 가격 정보는 [예약된 IP 주소 가격 정보](http://go.microsoft.com/fwlink/?LinkID=398482)를 참조하세요.
4. IP 주소를 어떻게 예약하나요?
   * PowerShell 또는 [Azure 관리 REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)를 사용하여 특정 지역에서 IP 주소를 예약할 수 있습니다. 이 예약된 IP 주소는 구독에 연결됩니다. 관리 포털을 사용하여 IP 주소를 예약할 수는 없습니다.
5. 선호도 그룹 기반 VNet에서 예약된 IP를 사용할 수 있나요?
   * 예약된 IP는 지역 VNet에서만 지원됩니다. 선호도 그룹과 연결된 VNet에 대해서는 지원되지 않습니다. VNet을 지역 또는 선호도 그룹과 연결하는 방법에 대한 자세한 내용은 [지역 VNet 및 선호도 그룹 정보](virtual-networks-migrate-to-regional-vnet.md)를 참조하세요.

## 예약된 VIP를 관리하는 방법
예약된 IP를 사용하려면 먼저 구독에 예약된 IP를 추가해야 합니다. *미국 중부* 위치에서 사용할 수 있는 공용 IP 주소 풀에서 예약된 IP를 만들려면 다음 PowerShell 명령을 실행합니다.

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

그러나 예약할 IP를 지정할 수 없습니다. 구독에서 예약된 IP 주소를 보려면 다음 PowerShell 명령을 실행하고 *ReservedIPName* 및 *Address*의 값을 확인합니다.

    Get-AzureReservedIP

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

IP를 예약하면 예약된 IP는 삭제될 때까지 계속 구독에 연결됩니다. 위에 나온 예약된 IP를 삭제하려면 다음 PowerShell 명령을 실행합니다.

    Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## 기존 클라우드 서비스의 IP 주소를 예약하는 방법
*-ServiceName* 매개 변수를 추가하여 기존 클라우드 서비스의 IP 주소를 예약할 수 있습니다. *미국 중부* 위치에서 클라우드 서비스 *TestService*의 IP 주소를 예약하려면 다음 PowerShell 명령을 실행합니다.

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService


## 예약된 IP를 새 클라우드 서비스에 연결하는 방법
아래 스크립트는 예약된 IP를 새로 만든 후 *TestService*라는 이름의 새 클라우드 서비스에 연결합니다.

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

> [!NOTE]
> 클라우드 서비스에서 사용할 예약된 IP를 만들 때 여전히 인바운드 통신에 *VIP:&lt;포트 번호>*를 사용하여 VM을 참조해야 합니다. IP를 예약했다고 VM에 직접 연결할 수 있다는 의미는 아닙니다. 예약된 IP는 VM이 배포된 클라우드 서비스에 할당됩니다. IP 통해 VM에 직접 연결하려는 경우 인스턴스 수준 공용 IP를 구성해야 합니다. 인스턴스 수준 공용 IP(ILPIP라고 함)는 VM에 직접 할당된 공용 IP의 한 유형입니다. ILPIP는 예약할 수 없습니다. 자세한 내용은 [인스턴스 수준 공용 IP(ILPIP)](virtual-networks-instance-level-public-ip.md)를 참조하세요.
> 
> 

## 실행 중인 배포에서 예약된 IP를 제거하는 방법
위의 스크립트에서 만든 새 서비스에 추가된 예약된 IP를 제거하려면 다음 PowerShell 명령을 실행합니다.

    Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

> [!NOTE]
> 실행 중인 배포에서 예약된 IP를 제거해도 구독에서 예약이 제거되지는 않습니다. 단지 구독의 다른 리소스에서 사용할 수 있도록 IP가 해제됩니다.
> 
> 

## 실행 중인 배포에 예약된 IP를 연결하는 방법
아래 스크립트는 *TestVM2*라는 이름의 새 VM이 있는 *TestService2*라는 새 클라우드 서비스를 만든 후 *MyReservedIP*라는 기존의 예약된 IP를 클라우드 서비스에 연결합니다.

    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | New-AzureVM -ServiceName TestService2 -Location "Central US"
    Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## 서비스 구성 파일을 사용하여 클라우드 서비스에 예약된 IP를 연결하는 방법
서비스 구성(CSCFG) 파일을 사용하여 클라우드 서비스에 예약된 IP를 연결할 수도 있습니다. 아래 샘플 xml에서는 *MyReservedIP*라는 예약된 VIP를 사용하도록 클라우드 서비스를 구성하는 방법을 보여 줍니다.

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## 다음 단계
* 클래식 배포 모델에서 [IP 주소 지정](virtual-network-ip-addresses-overview-classic.md)이 어떻게 작동하는지 이해합니다.
* [예약된 개인 IP 주소](virtual-networks-reserved-private-ip.md)에 대해 알아봅니다.
* [ILPIP(인스턴스 수준 공용 IP) 주소](virtual-networks-instance-level-public-ip.md)에 대해 알아봅니다.

<!---HONumber=AcomDC_0824_2016-->