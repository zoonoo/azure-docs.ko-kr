---
title: Azure 부팅 진단
description: Azure 부팅 진단 및 관리 되는 부팅 진단 개요
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 1dcefefe02d91506c494cdf91e75ca951ccf43bb
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365473"
---
# <a name="azure-boot-diagnostics"></a>Azure 부팅 진단

부팅 진단은 VM 부팅 실패를 진단할 수 있도록 하는 Azure VM (가상 머신)에 대 한 디버깅 기능입니다. 부팅 진단을 통해 사용자는 직렬 로그 정보와 스크린샷을 수집 하 여 부팅 하는 VM의 상태를 관찰할 수 있습니다.

## <a name="boot-diagnostics-storage-account"></a>부트 진단 저장소 계정
Azure Portal에서 VM을 만들 때 부트 진단은 기본적으로 사용 하도록 설정 됩니다. 권장 되는 부팅 진단 환경은 Azure VM을 만들 때 상당한 성능 향상을 제공 하므로 관리 되는 저장소 계정을 사용 하는 것입니다. 이는 Azure 관리 저장소 계정이 사용 되므로 부팅 진단 데이터를 저장 하는 새 사용자 저장소 계정을 만드는 데 걸리는 시간이 제거 되기 때문입니다.

대체 부팅 진단 환경은 사용자 관리 저장소 계정을 사용 하는 것입니다. 사용자는 새 저장소 계정을 만들거나 기존 저장소 계정을 사용할 수 있습니다. 

> [!IMPORTANT]
> 로그와 스냅숏 이미지를 구성 하는 부팅 진단 데이터 blob은 관리 저장소 계정에 저장 됩니다. 고객은 디스크의 프로 비전 된 크기가 아닌 blob에서 사용 된 Gid에 대해서만 요금이 부과 됩니다. 스냅숏 미터는 관리 되는 저장소 계정의 요금 청구에 사용 됩니다. 관리 되는 계정은 표준 LRS 또는 표준 ZRS에 만들어지므로, 고객은 진단 데이터 blob의 크기에 대해서만 매월 $0.05/GB로 요금이 청구 됩니다. 이 가격에 대 한 자세한 내용은 [Managed disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조 하세요. 고객은 VM 리소스 URI에 연결 되는이 요금을 볼 수 있습니다. 

## <a name="boot-diagnostics-view"></a>부트 진단 보기
가상 컴퓨터 블레이드에 있는 부팅 진단 옵션은 Azure Portal의 *지원 및 문제 해결* 섹션 아래에 있습니다. 부트 진단을 선택 하면 스크린샷 및 일련 로그 정보가 표시 됩니다. 직렬 로그는 커널 메시징을 포함 하 고 스크린샷은 Vm의 현재 상태에 대 한 스냅숏입니다. VM에서 Windows 또는 Linux를 실행 하 고 있는지 여부에 따라 예상 되는 스크린샷은 어떻게 나타나는지 결정 합니다. Windows의 경우 사용자에 게 데스크톱 배경 및 Linux가 표시 됩니다. 사용자에 게 로그인 프롬프트가 표시 됩니다.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux 부팅 진단 스크린샷":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Windows 부팅 진단 스크린샷":::

## <a name="enable-managed-boot-diagnostics"></a>관리 되는 부팅 진단 사용 
Azure Portal, CLI 및 ARM 템플릿을 통해 관리 되는 부트 진단을 사용 하도록 설정할 수 있습니다. PowerShell을 통한 사용은 아직 지원 되지 않습니다. 

### <a name="enable-managed-boot-diagnostics-using-the-azure-portal"></a>Azure Portal를 사용 하 여 관리 되는 부팅 진단 사용
Azure Portal에서 VM을 만들 때 기본 설정은 관리 저장소 계정을 사용 하 여 부팅 진단을 사용 하도록 설정 하는 것입니다. 이를 보려면 VM을 만드는 동안 *관리* 탭으로 이동 합니다. 

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-enable-portal.png" alt-text="VM을 만드는 동안 관리 되는 부트 진단을 사용 하도록 설정 하는 스크린샷":::

### <a name="enable-managed-boot-diagnostics-using-cli"></a>CLI를 사용 하 여 관리 되는 부팅 진단 사용
관리 저장소 계정을 사용한 부트 진단은 Azure CLI 2.12.0 이상에서 지원 됩니다. 저장소 계정에 대 한 이름 또는 URI를 입력 하지 않으면 관리 계정이 사용 됩니다. 자세한 내용 및 코드 샘플은 [부트 진단에 대 한 CLI 설명서](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest&preserve-view=true)를 참조 하세요.

### <a name="enable-managed-boot-diagnostics-using-azure-resource-manager-arm-templates"></a>ARM (Azure Resource Manager) 템플릿을 사용 하 여 관리 되는 부트 진단 사용
API 버전 2020-06-01 이후의 모든 항목은 관리 부트 진단을 지원 합니다. 자세한 내용은 [부팅 진단 인스턴스 보기](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#bootdiagnostics)를 참조 하세요.

```ARM Template
            "name": "[parameters('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2020-06-01",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[parameters('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[parameters('virtualMachineComputerName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "linuxConfiguration": {
                        "disablePasswordAuthentication": true
                    }
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true
                    }
                }
            }
        }
    ],

```

## <a name="limitations"></a>제한 사항
- 부트 진단은 Azure Resource Manager Vm에만 사용할 수 있습니다.
- 관리 되는 부팅 진단은 관리 되지 않는 OS 디스크를 사용 하는 Vm을 지원 하지 않습니다.
- 부팅 진단은 premium storage 계정을 지원 하지 않습니다. premium storage 계정이 부팅 진단에 사용 되 면 `StorageAccountTypeNotSupported` VM을 시작할 때 오류가 발생 합니다. 
- 관리 되는 저장소 계정은 리소스 관리자 API 버전 "2020-06-01" 이상에서 지원 됩니다.
- 현재 Azure 직렬 콘솔은 부트 진단을 위해 관리 되는 저장소 계정과 호환 되지 않습니다. [Azure 직렬 콘솔](./troubleshooting/serial-console-overview.md)에 대해 자세히 알아보세요.
- 포털은 단일 인스턴스 Vm에 대 한 관리 되는 저장소 계정으로 부팅 진단을 사용 하도록 지원 합니다.

## <a name="next-steps"></a>다음 단계

[Azure 직렬 콘솔](./troubleshooting/serial-console-overview.md) 에 대 한 자세한 내용과 부팅 진단을 사용 하 여 [azure의 가상 컴퓨터 문제를 해결](./troubleshooting/boot-diagnostics.md)하는 방법을 알아보세요.