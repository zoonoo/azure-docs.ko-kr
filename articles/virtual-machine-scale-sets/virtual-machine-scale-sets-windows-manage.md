<properties
	pageTitle="가상 컴퓨터 크기 집합의 VM 관리 | Microsoft Azure"
	description="Azure PowerShell을 사용하여 가상 컴퓨터 크기 집합의 가상 컴퓨터를 관리합니다."
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# 가상 컴퓨터 규모 집합의 가상 컴퓨터 관리

Azure PowerShell은 Microsoft Azure에서 리소스를 관리할 때 많은 성능과 유연성을 제공합니다. 이 문서의 작업을 사용하여 가상 컴퓨터 크기 집합의 가상 컴퓨터 리소스를 관리합니다.

- [가상 컴퓨터 크기 집합에 대한 정보 표시](#displayvm)
- [크기 집합의 가상 컴퓨터 시작](#start)
- [크기 집합의 가상 컴퓨터 중지](#stop)
- [크기 집합의 가상 컴퓨터 다시 시작](#restart)
- [크기 집합에서 가상 컴퓨터 삭제](#delete)

크기 집합의 가상 컴퓨터 관리와 관련된 작업을 수행할 경우에는 관리하려는 컴퓨터의 인스턴스 ID를 알아야 합니다. [Azure 리소스 탐색기](https://resources.azure.com)를 사용하여 크기 집합에 있는 가상 컴퓨터의 인스턴스 ID를 찾을 수 있습니다. 리소스 탐색기를 사용하여 마친 작업의 상태를 확인할 수도 있습니다.

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## <a id="displayvm"></a>가상 컴퓨터 크기 집합에 대한 정보 표시

크기 집합에 대한 일반 정보를 가져올 수 있습니다(인스턴스 보기라고도 함). 또는 집합의 리소스에 대한 정보와 같이 더 구체적인 정보를 가져올 수 있습니다.

이 명령에서 *리소스 그룹 이름*을 가상 컴퓨터 크기 집합이 포함된 리소스 그룹의 이름으로 바꾸고 *크기 집합 이름*을 가상 컴퓨터 크기 집합의 이름으로 바꾼 다음 명령을 실행합니다.

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

이때 반환되는 내용은 다음과 같습니다.

    Sku                      :  {
                                  "name": "Standard_A0",
                                  "tier": "Standard",
                                  "capacity": 4
                                }
    UpgradePolicy            :  {
                                  "mode": "Manual"
                                }
    VirtualMachineProfile    :  {
                                  "osProfile": {
                                    "computerNamePrefix": "myvmss1",
                                    "adminUserName": "user1",
                                    "adminPassword": null,
                                    "customData": null,
                                    "windowsConfiguration": {
                                      "provisionVMAgent": true,
                                      "enableAutomaticUpdates": true,
                                      "timeZone": null,
                                      "additionalUnattendContent": null,
                                      "winRM": null
                                    }
                                    "linuxConfiguration": null,
                                    "secrets": []
                                  },
                                  "storageProfile": {
                                    "imageReference": {
                                      "publisher": "MicrosoftWindowsServer",
                                      "offer": "WindowsServer",
                                      "sku": "2012-R2-Datacenter",
                                      "version": "latest"
                                    },
                                    "osDisk": {
                                      "name": "vmssosdisk",
                                      "caching": "ReadOnly",
                                      "createOption": "FromImage",
                                      "osType": null,
                                      "image": null,
                                      "vhdContainers": [
                                        "https://amyst1.blob.core.windows.net/vmss",
                                        "https://gmyst1.blob.core.windows.net/vmss",
                                        "https://mmyst1.blob.core.windows.net/vmss",
                                        "https://smyst1.blob.core.windows.net/vmss",
                                        "https://ymyst1.blob.core.windows.net/vmss"
                                      ]
                                    }
                                  },
                                  "networkProfile": {
                                    "networkInterfaceConfigurations": [
                                      {
                                        "name": "myresnc2",
                                        "properties.primary": true,
                                        "properties.ipConfigurations": [
                                          {
                                            "name": "ip1",
                                            "properties.subnet": {
                                              "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/myresvn1/subnets/myressn1"
                                            },
                                            "properties.loadBalancerBackendAddressPools": [
                                              {
                                                "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/myreslb1/backendAddressPools/bepool1"
                                              }
                                            ],
                                            "properties.loadBalancerInboundNatPools": [
                                              {
                                                "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/myreslb1/inboundNatPools/natpool1"
                                              }
                                            ],
                                            "id": null
                                          }
                                        ],
                                        "id": null
                                      }
                                    ]
                                  },
                                  "extensionProfile": {
                                    "extensions": [
                                      {
                                        "name": "Microsoft.Insights.VMDiagnosticsSettings",
                                        "properties.publisher": "Microsoft.Azure.Diagnostics",
                                        "properties.type": "IaaSDiagnostics",
                                        "properties.typeHandlerVersion": "1.5",
                                        "properties.autoUpgradeMinorVersion": true,
                                        "properties.settings": {
                                          "xmlCfg": "{encoded configuration}",
                                          "storageAccount": "amyst1"
                                        },
                                        "properties.protectedSettings": null,
                                        "properties.provisioningState": null,
                                        "id": null
                                      }
                                    ]
                                  }
                                }
    ProvisioningState           : Succeeded
    Id                          : /subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                        : myvmss1
	Type                        : Microsoft.Compute/virtualMachineScaleSets
	Location                    : westus
	Tags.Count                  : 0
	Tags                        :

일반 정보를 가져오려면 *리소스 그룹 이름*을 가상 컴퓨터 크기 집합이 포함된 리소스 그룹의 이름으로 바꾸고 *크기 집합 이름*을 가상 컴퓨터 크기 집합의 이름으로 바꾼 다음 명령을 실행합니다.

	Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView

이때 반환되는 내용은 다음과 같습니다.

    VirtualMachine   :  {
                          "statusesSummary": [
                            {
                              "code": "ProvisioningState/succeeded",
                              "count": 4
                            }
                          ]
                        }
    Extensions.Count :  1
    Extensions       :  {
                          "name": "Microsoft.Insights.VMDiagnosticsSettings",
                          "statusesSummary": [
                            {
                              "code": "ProvisioningState/succeeded",
                              "count": 4
                            }
                          ]
                        }
	Statuses.Count   :  1
	Statuses         :  {
                          "code": "ProvisioningState/succeeded",
                          "level": "Info",
                          "displayStatus": "Provisioning succeeded",
                          "message": null,
                          "time": "2016-03-14T20:29:37.170809Z"
                        }

## <a id="start"></a>크기 집합의 가상 컴퓨터 시작

이 명령에서 *리소스 그룹 이름*을 가상 컴퓨터 크기 집합이 포함된 리소스 그룹의 이름으로 바꾸고 *VM 크기 집합 이름*을 크기 집합의 이름으로 바꾸며 *인스턴스 ID*를 다시 시작하려는 가상 컴퓨터의 식별자로 바꾼 다음 명령을 실행합니다.

    Start-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

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

## <a id="stop"></a>크기 집합의 가상 컴퓨터 중지

이 명령에서 *리소스 그룹 이름*을 가상 컴퓨터 크기 집합이 포함된 리소스 그룹의 이름으로 바꾸고 *크기 집합 이름*을 크기 집합의 이름으로 바꾸며 *인스턴스 ID*를 중지하려는 가상 컴퓨터의 식별자로 바꾼 다음 명령을 실행합니다.

	Stop-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

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

## <a id="restart"></a>크기 집합의 가상 컴퓨터 다시 시작

이 명령에서 *리소스 그룹 이름*을 가상 컴퓨터 크기 집합이 포함된 리소스 그룹의 이름으로 바꾸고 *크기 집합 이름*을 크기 집합의 이름으로 바꾸며 *인스턴스 ID*를 다시 시작하려는 가상 컴퓨터의 식별자로 바꾼 다음 명령을 실행합니다.

	Restart-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

## <a id="delete"></a>크기 집합에서 가상 컴퓨터 제거

이 명령에서 *리소스 그룹 이름*을 가상 컴퓨터 크기 집합이 포함된 리소스 그룹의 이름으로 바꾸고 *크기 집합 이름*을 크기 집합의 이름으로 바꾸며 *인스턴스 ID*를 크기 집합에서 제거하려는 가상 컴퓨터의 식별자로 바꾼 다음 명령을 실행합니다.

	Remove-AzureRmVmssVM -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId "instance id"

<!---HONumber=AcomDC_0427_2016-->