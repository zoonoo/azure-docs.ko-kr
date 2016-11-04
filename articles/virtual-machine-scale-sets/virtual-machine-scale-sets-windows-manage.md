---
title: 가상 컴퓨터 크기 집합의 VM 관리 | Microsoft Docs
description: Azure PowerShell을 사용하여 가상 컴퓨터 크기 집합의 가상 컴퓨터를 관리합니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2016
ms.author: davidmu

---
# 가상 컴퓨터 규모 집합의 가상 컴퓨터 관리
이 문서의 작업을 사용하여 가상 컴퓨터 크기 집합의 가상 컴퓨터 리소스를 관리합니다.

크기 집합의 가상 컴퓨터 관리와 관련된 작업을 수행할 경우에는 관리하려는 컴퓨터의 인스턴스 ID를 알아야 합니다. [Azure 리소스 탐색기](https://resources.azure.com)를 사용하여 크기 집합에 있는 가상 컴퓨터의 인스턴스 ID를 찾을 수 있습니다. 리소스 탐색기를 사용하여 마친 작업의 상태를 확인할 수도 있습니다.

최신 버전의 Azure PowerShell을 설치하는 방법, 사용할 구독을 선택하는 방법, Azure 계정에 로그인하는 방법은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

## 가상 컴퓨터 크기 집합에 대한 정보 표시
크기 집합에 대한 일반 정보를 가져올 수 있습니다(인스턴스 보기라고도 함). 또는 집합의 리소스에 대한 정보와 같이 더 구체적인 정보를 가져올 수 있습니다.

이 명령에서 *리소스 그룹 이름*을 가상 컴퓨터 크기 집합이 포함된 리소스 그룹의 이름으로 바꾸고 *크기 집합 이름*을 가상 컴퓨터 크기 집합의 이름으로 바꾼 다음 명령을 실행합니다.

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

이때 반환되는 내용은 다음과 같습니다.

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded

이 명령에서 *리소스 그룹 이름*을 가상 컴퓨터 크기 집합이 포함된 리소스 그룹 이름으로 바꾸고 *크기 집합 이름*을 가상 컴퓨터 크기 집합 이름으로 바꾼 다음 *#*을 정보를 가져오려는 가상 컴퓨터의 인스턴스 식별자로 바꾼 다음 명령을 실행합니다.

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

이때 반환되는 내용은 다음과 같습니다.

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded

## 크기 집합의 가상 컴퓨터 시작
이 명령에서 *리소스 그룹 이름*을 가상 컴퓨터 크기 집합이 포함된 리소스 그룹의 이름으로 바꾸고 *크기 집합 이름*을 크기 집합의 이름으로 바꾼 다음 *#*을 시작하려는 가상 컴퓨터의 식별자로 바꾼 다음 명령을 실행합니다.

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

리소스 탐색기에서 인스턴스 상태가 **실행 중**으로 표시됩니다.

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

-InstanceId 매개 변수를 사용하지 않으면 집합의 모든 가상 컴퓨터를 시작할 수 있습니다.

## 크기 집합의 가상 컴퓨터 중지
이 명령에서 *리소스 그룹 이름*을 가상 컴퓨터 크기 집합이 포함된 리소스 그룹의 이름으로 바꾸고 *크기 집합 이름*을 크기 집합의 이름으로 바꾼 다음 *#*을 중지하려는 가상 컴퓨터의 식별자로 바꾼 다음 명령을 실행합니다.

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

리소스 탐색기에서 인스턴스 상태가 **할당 취소됨**으로 표시됩니다.

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]

가상 컴퓨터를 중지하고 할당을 취소하지 않으려면 StayProvisioned 매개 변수를 사용합니다. -InstanceId 매개 변수를 사용하지 않으면 집합의 모든 가상 컴퓨터를 중지할 수 있습니다.

## 크기 집합의 가상 컴퓨터 다시 시작
이 명령에서 *리소스 그룹 이름*을 가상 컴퓨터 크기 집합이 포함된 리소스 그룹의 이름으로 바꾸고 *크기 집합 이름*을 크기 집합의 이름으로 바꾼 다음 *#*을 다시 시작하려는 가상 컴퓨터의 식별자로 바꾼 다음 명령을 실행합니다.

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

-InstanceId 매개 변수를 사용하지 않으면 집합의 모든 가상 컴퓨터를 다시 시작할 수 있습니다.

## 크기 집합에서 가상 컴퓨터 제거
이 명령에서 *리소스 그룹 이름*을 가상 컴퓨터 크기 집합이 포함된 리소스 그룹의 이름으로 바꾸고 *크기 집합 이름*을 크기 집합의 이름으로 바꾼 다음 *#*을 크기 집합에서 제거하려는 가상 컴퓨터의 식별자로 바꾼 다음 명령을 실행합니다.

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

-InstanceId 매개 변수를 사용하지 않으면 가상 컴퓨터 크기 집합을 한 번에 제거할 수 있습니다.

<!---HONumber=AcomDC_0720_2016-->