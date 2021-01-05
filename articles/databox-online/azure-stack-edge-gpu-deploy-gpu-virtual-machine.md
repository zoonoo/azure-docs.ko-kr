---
title: Azure Stack Edge Pro 장치에서 GPU Vm의 개요 및 배포
description: 템플릿을 사용 하 여 Azure Stack Edge Pro 장치에서 GPU Vm (가상 머신)을 만들고 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: 6fa8705221e1f1f9a9143f274a53db21b657988f
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763926"
---
# <a name="gpu-vms-for-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro 장치에 대 한 GPU Vm

이 문서에서는 Azure Stack Edge Pro 장치에서 GPU Vm (가상 머신)에 대 한 개요를 제공 합니다. 이 문서에서는 GPU VM을 만든 다음 적절 한 Nvidia 드라이버를 설치 하는 GPU 드라이버 확장을 설치 하는 방법을 설명 합니다. Azure Resource Manager 템플릿을 사용 하 여 GPU VM을 만들고 GPU 드라이버 확장을 설치 합니다. 

이 문서는 Azure Stack Edge Pro GPU 및 Azure Stack Edge Pro R 장치에 적용 됩니다.

## <a name="about-gpu-vms"></a>GPU Vm 정보

Azure Stack Edge Pro 장치에는 Nvidia Tesla T4 GPU의 1 ~ 2가 장착 되어 있습니다. 이러한 장치에 GPU 가속 VM 워크 로드를 배포 하려면 GPU 최적화 VM 크기를 사용 합니다. 예를 들어, T4 Gpu를 사용 하는 유추 워크 로드를 배포 하려면 NC T4 v3 시리즈를 사용 해야 합니다. 

자세한 내용은 [NC T4 v3 시리즈 vm](../virtual-machines/nct4-v3-series.md)을 참조 하세요.

## <a name="supported-os-and-gpu-drivers"></a>지원 되는 OS 및 GPU 드라이버 

Azure N 시리즈 Vm의 GPU 기능을 활용 하려면 Nvidia GPU 드라이버를 설치 해야 합니다. 

Nvidia GPU 드라이버 확장은 적절 한 Nvidia 이상 DA 또는 그리드 드라이버를 설치 합니다. Azure Resource Manager 템플릿을 사용 하 여 확장을 설치 하거나 관리할 수 있습니다.

### <a name="supported-os-for-gpu-extension-for-windows"></a>Windows 용 GPU 확장에 대해 지원 되는 OS

이 확장은 다음 운영 체제 (OSs)를 지원 합니다. 다른 버전은 작동 하지만 Azure Stack Edge Pro 장치에서 실행 되는 GPU Vm에서 내부 테스트 되지 않았습니다.

| 배포 | 버전 |
|---|---|
| Windows Server 2019 | 핵심 |
| Windows Server 2016 | 핵심 |

### <a name="supported-os-for-gpu-extension-for-linux"></a>Linux 용 GPU 확장에 대해 지원 되는 OS

이 확장은 특정 OS 버전에 대 한 드라이버 지원에 따라 다음과 같은 OS 배포판을 지원 합니다. 다른 버전은 작동 하지만 Azure Stack Edge Pro 장치에서 실행 되는 GPU Vm에서 내부 테스트 되지 않았습니다.


| 배포 | 버전 |
|---|---|
| Ubuntu | 18.04 LTS |
| Red Hat Enterprise Linux | 7.4 |


## <a name="gpu-vms-and-kubernetes"></a>GPU Vm 및 Kubernetes

장치에 GPU Vm을 배포 하기 전에 장치에 Kubernetes가 구성 된 경우 다음 고려 사항을 검토 하세요.

#### <a name="for-1-gpu-device"></a>1-GPU 장치: 

- **장치에서 GPU vm을 만든 다음 Kubernetes 구성을** 수행 합니다 .이 시나리오에서는 gpu vm 만들기 및 Kubernetes 구성이 모두 성공 합니다. 이 경우 Kubernetes는 GPU에 액세스할 수 없습니다.

- **장치에서 Kubernetes을 구성 하 고 GPU VM을 만든 후** 다음을 수행 합니다 .이 시나리오에서는 Kubernetes가 장치의 gpu를 클레임 하 고 사용할 수 있는 gpu 리소스가 없으므로 VM 만들기가 실패 합니다.

#### <a name="for-2-gpu-device"></a>2-GPU 장치

- **장치에서 GPU vm을 만든 다음 Kubernetes 구성을** 수행 합니다 .이 시나리오에서는 사용자가 만드는 gpu vm이 장치에서 한 gpu를 요청 하 고 Kubernetes 구성도 성공 하 고 나머지 하나의 gpu를 클레임 합니다. 

- **장치에 두 개의 Gpu vm (Kubernetes 구성** 포함)을 만듭니다 .이 시나리오에서 두 gpu vm은 장치에서 두 gpu를 요청 하 고 Kubernetes는 gpu 없이 구성 됩니다. 

- **GPU VM을 만든 후 장치에서 Kubernetes를 구성** 합니다 .이 시나리오에서 Kubernetes는 장치에 있는 gpu를 모두 클레임 하 고, 사용할 수 있는 gpu 리소스가 없으므로 VM 만들기가 실패 합니다.

장치에서 GPU Vm이 실행 중이 고 Kubernetes도 구성 되어 있는 경우 언제 든 지 VM의 할당이 취소 될 때 (Stop-AzureRmVM 또는 New-azurermvm를 사용 하 여 VM을 중지 하거나 제거 하는 경우) Kubernetes 클러스터가 장치에서 사용 가능한 모든 Gpu를 클레임 할 위험이 있습니다. 이러한 인스턴스에서는 장치에 배포 된 GPU Vm을 다시 시작 하거나 GPU Vm을 만들 수 없습니다.


## <a name="create-gpu-vms"></a>GPU Vm 만들기

장치에 GPU Vm을 배포 하는 경우 다음 단계를 수행 합니다.

1. 장치에서 Kubernetes도 실행 중인지 확인 합니다. 장치에서 Kubernetes를 실행 하는 경우 먼저 GPU VM을 만든 다음 Kubernetes를 구성 해야 합니다. Kubernetes가 먼저 구성 된 경우 사용 가능한 모든 GPU 리소스를 요청 하 고 GPU VM 만들기가 실패 합니다.

1. 클라이언트 컴퓨터에 [VM 템플릿 및 매개 변수 파일을 다운로드](https://aka.ms/ase-vm-templates) 합니다. 작업 디렉터리로 사용할 디렉터리에 압축을 풉니다.

1. GPU Vm을 만들려면 다음과 같은 차이점을 제외 하 고 [템플릿을 사용 하 여 Azure Stack Edge Pro에서 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) 의 모든 단계를 수행 합니다. 

    1. Compute 네트워크를 구성 하는 동안 계산을 위해 인터넷에 연결 된 포트를 사용 하도록 설정 합니다. 이를 통해 gpu Vm에 대 한 GPU 확장에 필요한 GPU 드라이버를 다운로드할 수 있습니다.

        포트 2가 인터넷에 연결 되어 있고 계산 네트워크를 사용 하도록 설정 하는 데 사용 된 예제는 다음과 같습니다. 이전 단계에서 Kubernetes가 필요 하지 않은 것으로 확인 된 경우 Kubernetes 노드 IP 및 외부 서비스 IP 할당을 건너뛸 수 있습니다.

        ![인터넷에 연결 된 포트에서 계산 설정 사용](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

            
    1. 템플릿을 사용 하 여 VM을 만듭니다. GPU VM 크기를 지정할 때 `CreateVM.parameters.json` Gpu vm에 대해 지원 되는 NCasT4-v3 시리즈를 사용 해야 합니다. 자세한 내용은 [GPU vm에 대해 지원 되는 VM 크기](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)를 참조 하세요.

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```

    1. GPU VM이 성공적으로 만들어지면 Azure Portal에서 Azure Stack Edge 리소스의 가상 머신 목록에서이 VM을 볼 수 있습니다.

        ![Azure Portal의 가상 컴퓨터 목록에 있는 GPU VM](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. VM을 선택 하 고 세부 정보로 드릴 다운 합니다. VM에 할당 된 IP를 복사 합니다.

    ![Azure Portal에서 GPU VM에 할당 된 IP](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. VM을 만든 후 확장 템플릿을 사용 하 여 GPU 확장을 배포 합니다. Linux Vm의 경우 [linux 용 gpu 확장 설치](#gpu-extension-for-linux) 및 windows vm의 경우 [windows 용 gpu 확장 설치](#gpu-extension-for-windows)를 참조 하세요.

1. GPU 확장 설치를 확인 하려면 GPU VM에 연결 합니다.
    1. Windows VM을 사용 하는 경우 [WINDOWS vm에 연결](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-windows-vm)의 단계를 따릅니다. [설치를 확인](#verify-windows-driver-installation)합니다.
    1. Linux VM을 사용 하는 경우 [LINUX vm에 연결](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-linux-vm)의 단계를 따릅니다. [설치를 확인](#verify-linux-driver-installation)합니다.

1. 필요한 경우 계산 네트워크를 다시 필요한 모든 항목으로 전환할 수 있습니다. 


> [!NOTE]
> 장치 소프트웨어 버전을 2012에서 이후 버전으로 업데이트 하는 경우 GPU Vm을 수동으로 중지 해야 합니다.


## <a name="install-gpu-extension"></a>GPU 확장 설치

VM의 운영 체제에 따라 Windows 또는 Linux 용 GPU 확장을 설치할 수 있습니다.

> [!NOTE]
> GPU 확장을 설치 하기 전에 장치의 compute 네트워크에 대해 사용 하도록 설정 된 포트가 인터넷에 연결 되어 있고 액세스 권한이 있는지 확인 합니다. 인터넷 액세스를 통해 GPU 드라이버가 다운로드 됩니다.

### <a name="gpu-extension-for-windows"></a>Windows 용 GPU 확장

기존 VM에 대 한 Nvidia GPU 드라이버를 배포 하려면 `addGPUExtWindowsVM.parameters.json` 매개 변수 파일을 편집한 다음 템플릿을 배포 `addGPUextensiontoVM.json` 합니다.

#### <a name="edit-parameters-file"></a>매개 변수 파일 편집

이 파일은 `addGPUExtWindowsVM.parameters.json` 다음 매개 변수를 사용 합니다.

```json
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: windowsGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverWindows" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DriverURL" : "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
    "DriverCertificateUrl" : "https://go.microsoft.com/fwlink/?linkid=871664",
    "DriverType":"CUDA"
    }
    }
    }
```

다음은이 문서에서 사용 된 샘플 매개 변수 파일입니다.

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment2"

DeploymentName          : deployment2
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:02:56 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          adminUsername    String                     Administrator
                          password         String                     Password1
                          imageName        String                     myasewindowsimg
                          vmSize           String                     Standard_NC4as_T4_v3
                          vnetName         String                     ASEVNET
                          vnetRG           String                     aserg
                          subnetName       String                     ASEVNETsubNet
                          nicName          String                     nic6
                          ipConfigName     String                     ipconfig6
                          privateIPAddress  String

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="deploy-template"></a>템플릿 배포

템플릿을 배포 `addGPUextensiontoVM.json` 합니다. 이 템플릿은 기존 VM에 확장을 배포 합니다. 다음 명령을 실행합니다.

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> 확장 배포는 장기 실행 작업 이므로 완료 하는 데 약 10 분이 걸립니다.

샘플 출력은 다음과 같습니다.

```powershell
PS C:\WINDOWS\system32> "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment3"

DeploymentName          : deployment3
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:18:50 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          extensionName    String                     windowsgpuext
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverWindows
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>배포 추적

지정 된 VM에 대 한 확장의 배포 상태를 확인 하려면 다음 명령을 실행 합니다. 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
샘플 출력은 다음과 같습니다.

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM2 -Name windowsgpuext

ResourceGroupName       : myasegpuvm1
VMName                  : VM2
Name                    : windowsgpuext
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverWindows
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM2/extensions/windowsgpuext
PublicSettings          : {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

확장 실행 출력은 다음 파일에 기록 됩니다. 설치 상태를 추적 하려면이 파일을 참조 `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status` 하세요. 


성공적인 설치는로 표시 됩니다 `message` `Enable Extension` `status` `success` .

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

#### <a name="verify-windows-driver-installation"></a>Windows 드라이버 설치 확인

VM에 로그인 하 고 드라이버와 함께 설치 된 nvidia-smi-s 명령줄 유틸리티를 실행 합니다. 는 `nvidia-smi.exe` 에  `C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe` 있습니다. 파일이 표시 되지 않는 경우 드라이버 설치는 백그라운드에서 계속 실행 될 수 있습니다. 10 분 동안 기다린 후 다시 확인 합니다.

드라이버가 설치 된 경우 다음 샘플과 유사한 출력이 표시 됩니다. 

```powershell
PS C:\Users\Administrator> cd "C:\Program Files\NVIDIA Corporation\NVSMI"
PS C:\Program Files\NVIDIA Corporation\NVSMI> ls

    Directory: C:\Program Files\NVIDIA Corporation\NVSMI

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/26/2020  12:00 PM         849640 MCU.exe
-a----        2/26/2020  12:00 PM         443104 nvdebugdump.exe
-a----        2/25/2020   2:06 AM          81823 nvidia-smi.1.pdf
-a----        2/26/2020  12:01 PM         566880 nvidia-smi.exe
-a----        2/26/2020  12:01 PM         991344 nvml.dll

PS C:\Program Files\NVIDIA Corporation\NVSMI> .\nvidia-smi.exe
Wed Dec 16 00:35:51 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 442.50       Driver Version: 442.50       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            TCC  | 0000503C:00:00.0 Off |                    0 |
| N/A   35C    P8    11W /  70W |      8MiB / 15205MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
PS C:\Program Files\NVIDIA Corporation\NVSMI>
```

자세한 내용은 [Windows 용 NVIDIA GPU 드라이버 확장](../virtual-machines/extensions/hpccompute-gpu-windows.md)(영문)을 참조 하세요.

### <a name="gpu-extension-for-linux"></a>Linux 용 GPU 확장

기존 VM에 대 한 Nvidia GPU 드라이버를 배포 하려면 `addGPUExtLinuxVM.parameters.json` 매개 변수 파일을 편집한 다음 템플릿을 배포 `addGPUextensiontoVM.json` 합니다.

#### <a name="edit-parameters-file"></a>매개 변수 파일 편집

Ubuntu를 사용 하는 경우 `addGPUExtLinuxVM.parameters.json` 파일은 다음 매개 변수를 사용 합니다.

```powershell
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: linuxGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverLinux" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
    "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
    "CUDA_ver": "10.0.130",
    "InstallCUDA": "true"
    }
    }
    }
```
Red Hat Enterprise Linux (RHEL)를 사용 하는 경우 파일은 다음 매개 변수를 사용 합니다.


```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<name of the VM>" 
        },
        "extensionName": {
            "value": "<name for the extension. Example: linuxGpu>" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
                    "isCustomInstall":true,
                    "DRIVER_URL":"https://go.microsoft.com/fwlink/?linkid=874273",
                    "CUDA_ver":"10.0.130",
                    "PUBKEY_URL":"http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                    "DKMS_URL":"https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm",
                    "LIS_URL":"https://aka.ms/lis",
                    "LIS_RHEL_ver":"3.10.0-1062.9.1.el7"
            }
        }
    }
}
```


다음은이 문서에서 사용 된 샘플 Ubuntu 매개 변수 파일입니다.

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM1" 
        },
        "extensionName": {
            "value": "gpuLinux" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
            "CUDA_ver": "10.0.130",
            "InstallCUDA": "true"
            }
        }
    }
}
```


#### <a name="deploy-template"></a>템플릿 배포

템플릿을 배포 `addGPUextensiontoVM.json` 합니다. 이 템플릿은 기존 VM에 확장을 배포 합니다. 다음 명령을 실행합니다.

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> 확장 배포는 장기 실행 작업 이므로 완료 하는 데 약 10 분이 걸립니다.

샘플 출력은 다음과 같습니다.

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "rg2"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "delpoyment7"

DeploymentName          : delpoyment7
ResourceGroupName       : rg2
ProvisioningState       : Succeeded
Timestamp               : 12/10/2020 10:43:23 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM1
                          extensionName    String                     gpuLinux
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverLinux
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL":
                          "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```

#### <a name="track-deployment-status"></a>배포 상태 추적    
    
템플릿 배포는 장기 실행 작업입니다. 지정 된 VM에 대 한 확장의 배포 상태를 확인 하려면 다른 PowerShell 세션 (관리자 권한으로 실행)을 엽니다. 다음 명령을 실행합니다. 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
샘플 출력은 다음과 같습니다. 

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName rg2 -VMName VM1 -Name gpulinux

ResourceGroupName       : rg2
VMName                  : VM1
Name                    : gpuLinux
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverLinux
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg2/providers/Microsoft.Compute/virtualMachines/VM1/extensions/gpuLinux
PublicSettings          : {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> 배포가 완료 되 면이 `ProvisioningState` 로 변경 `Succeeded` 됩니다.

확장 실행 출력은 다음 파일에 기록 됩니다 `/var/log/azure/nvidia-vmext-status` .

#### <a name="verify-linux-driver-installation"></a>Linux 드라이버 설치 확인

드라이버 설치를 확인 하려면 다음 단계를 수행 합니다.

1. GPU VM에 연결 합니다. [LINUX VM에 연결](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-linux-vm)의 지침을 따릅니다. 

    샘플 출력은 다음과 같습니다.

    ```powershell
    PS C:\WINDOWS\system32> ssh -l Administrator 10.57.50.60
    Administrator@10.57.50.60's password:
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
      System information as of Thu Dec 10 22:57:01 UTC 2020
    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
    
    249 packages can be updated.
    140 updates are security updates.
    
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage    
      System information as of Thu Dec 10 22:57:01 UTC 2020    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
        
    249 packages can be updated.
    140 updates are security updates.
    
    New release '20.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
        
    *** System restart required ***
    Last login: Thu Dec 10 21:49:29 2020 from 10.90.24.23
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@VM1:~$
    ```
2. 드라이버와 함께 설치 된 nvidia-smi-s 명령줄 유틸리티를 실행 합니다. 드라이버가 성공적으로 설치 되 면 유틸리티를 실행 하 여 다음 출력을 볼 수 있습니다.

    ```powershell
    Administrator@VM1:~$ nvidia-smi
    Thu Dec 10 22:58:46 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 455.45.01    Driver Version: 455.45.01    CUDA Version: 11.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            Off  | 0000941F:00:00.0 Off |                    0 |
    | N/A   48C    P0    27W /  70W |      0MiB / 15109MiB |      5%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    Administrator@VM1:~$
    ```

자세한 내용은 [Linux 용 NVIDIA GPU 드라이버 확장](../virtual-machines/extensions/hpccompute-gpu-linux.md)(영문)을 참조 하세요.

## <a name="remove-gpu-extension"></a>GPU 확장 제거

GPU 확장을 제거 하려면 다음 명령을 사용 합니다.

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

샘플 출력은 다음과 같습니다.

```powershell
PS C:\azure-stack-edge-deploy-vms> Remove-AzureRmVMExtension -ResourceGroupName rgl -VMName WindowsVM -Name windowsgpuext
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue? [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
Requestld IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------    
          True                OK         OK
```




## <a name="next-steps"></a>다음 단계

[Azure Resource Manager cmdlet](/powershell/module/azurerm.resources/?view=azurermps-6.13.0)