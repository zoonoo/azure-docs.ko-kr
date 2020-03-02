---
title: Linux Vm에 대 한 문제 해결-Azure Disk Encryption
description: 이 문서에서는 Linux Vm 용 Microsoft Azure 디스크 암호화에 대 한 문제 해결 팁을 제공 합니다.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: dd21b6520dc68a6f7faa5500054b2865556e3dfb
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205911"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure Disk Encryption 문제 해결 가이드

이 가이드는 조직에서 Azure Disk Encryption을 사용하는 IT 전문가, 정보 보안 분석가 및 클라우드 관리자를 위한 것입니다. 이 문서는 디스크 암호화 관련 문제를 해결하는 데 도움을 드리기 위해 작성되었습니다.

아래 단계를 수행 하기 전에 먼저 암호화 하려는 vm이 지원 되는 [vm 크기 및 운영 체제](disk-encryption-overview.md#supported-vms-and-operating-systems)중 어느 것이 고 모든 필수 구성 요소를 충족 하는지 확인 합니다.

- [Vm에 대 한 추가 요구 사항](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [네트워킹 요구 사항](disk-encryption-overview.md#networking-requirements)
- [암호화 키 저장소 요구 사항](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Linux OS 디스크 암호화 문제 해결

Linux OS(운영 체제) 디스크 암호화에서는 전체 디스크 암호화 프로세스를 실행하기 전에 OS 드라이브를 분리해야 합니다. 드라이브를 분리할 수 없는 경우 "... 후 분리하지 못했습니다."라는 오류 메시지가 발생할 수 있습니다.

이 오류는 지원 되는 재고 갤러리 이미지에서 변경 된 환경을 사용 하 여 VM에서 OS 디스크 암호화를 시도 하는 경우에 발생할 수 있습니다. OS 드라이브를 분리하는 확장의 기능을 방해할 수 있는 지원되는 이미지로부터의 편차. 편차 예는 다음 항목을 포함할 수 있습니다.
- 지원되는 파일 시스템 또는 파티션 구성표와 더 이상 일치하지 않는 사용자 지정 이미지
- SAP, MongoDB, Apache Cassandra 및 Docker와 같은 대규모 애플리케이션은 암호화하기 전에 OS에 설치되고 실행되는 경우 지원되지 않습니다. Azure Disk Encryption은 디스크 암호화에서 OS 드라이브를 준비할 때 필요한 대로 이러한 프로세스를 안전하게 종료할 수 없습니다. OS 드라이브에 대한 오픈 파일 핸들을 보유하는 활성 프로세스가 있는 경우 OS 드라이브는 탑재될 수 없습니다. 따라서 OS 드라이브를 암호화하는 데 실패합니다. 
- 암호화가 활성화된 시간에 근접하여 실행하는 사용자 지정 스크립트 또는 암호화 프로세스 중에 다른 변경 작업이 VM에서 수행되고 있는 경우 이 충돌은 Azure Resource Manager 템플릿에서 동시에 실행하도록 여러 확장을 정의하거나 사용자 지정 스크립트 확장 또는 다른 작업을 디스크 암호화와 동시에 실행할 때 발생할 수 있습니다. 이러한 단계를 직렬화하고 격리하면 문제가 해결될 수 있습니다.
- 암호화를 활성화하기 전에 SELinux(Security Enhanced Linux)가 비활성화되지 않았으므로 분리 단계가 실패합니다. 암호화가 완료되면 SELinux를 다시 활성화할 수 있습니다.
- OS 디스크는 LVM(논리 볼륨 관리자) 구성표를 사용합니다. 제한된 LVM 데이터 디스크 지원은 사용할 수 있지만, LVM OS 디스크는 사용할 수 없습니다.
- 최소 메모리 요구 사항이 충족되지 않았습니다(OS 디스크 암호화에 권장되는 메모리 크기는 7GB임).
- 데이터 드라이브가 /mnt/ 디렉터리 또는 다른 디렉터리(예: /mnt/data1, /mnt/data2, /data3 + /data3/data4) 아래에 재귀적으로 탑재되었습니다.

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Ubuntu 14.04 LTS의 기본 커널을 업데이트 합니다.

Ubuntu 14.04 LTS 이미지는 기본 커널 버전 4.4와 함께 제공됩니다. 이 커널 버전에는 OS 암호화 프로세스 중에 메모리 부족 중단 프로그램이 dd 명령을 부적절하게 종료하는 알려진 문제가 있습니다. 이 버그는 최근 Azure 튜닝 Linux 커널에서 수정되었습니다. 이 오류를 방지하려면 이미지에서 암호화를 사용하도록 설정하기 전에 다음 명령을 사용하여 [Azure 튜닝 커널 4.15](https://packages.ubuntu.com/trusty/linux-azure) 이상으로 업데이트합니다.

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

VM이 새 커널로 다시 시작된 후 다음을 사용하여 새 커널 버전을 확인할 수 있습니다.

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Azure 가상 머신 에이전트 및 확장 버전 업데이트

지원 되지 않는 버전의 Azure 가상 컴퓨터 에이전트를 사용 하 여 가상 컴퓨터 이미지에서 Azure Disk Encryption 작업이 실패할 수 있습니다. 2\.2.38 이전 버전의 에이전트를 사용 하는 Linux 이미지는 암호화를 사용 하도록 설정 하기 전에 업데이트 해야 합니다. 자세한 내용은 [VM에서 Azure Linux 에이전트를 업데이트 하는 방법](../extensions/update-linux-agent.md) 및 [azure의 가상 머신 에이전트에 대 한 최소 버전 지원](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)을 참조 하세요.

올바른 버전의 Microsoft Azure. AzureDiskEncryption 또는 Microsoft azure. Azurediskdisforlinux 게스트 에이전트 확장도 필요 합니다. 확장 버전은 Azure Virtual Machine 에이전트 필수 구성 요소가 충족 되 고 지원 되는 버전의 가상 컴퓨터 에이전트가 사용 되는 경우 플랫폼에서 자동으로 유지 관리 되 고 업데이트 됩니다.

Microsoft. OSTCExtensions. AzureDiskEncryptionForLinux 확장은 더 이상 사용 되지 않으며 더 이상 지원 되지 않습니다.  

## <a name="unable-to-encrypt-linux-disks"></a>Linux 디스크를 암호화할 수 없음

경우에 따라 Linux 디스크 암호화가 "OS 디스크 암호화 시작됨" 상태에서 중단된 것으로 나타나고, SSH가 비활성화됩니다. 암호화 프로세스를 재고 갤러리 이미지에 대해 완료하는 데 3-16시간이 걸릴 수 있습니다. 다중 테라바이트 크기 데이터 디스크가 추가될 경우 이 프로세스는 며칠이 걸릴 수도 있습니다.

Linux OS 디스크 암호화 시퀀스는 OS 드라이브를 일시적으로 탑재 해제합니다. 그런 다음 암호화된 상태로 다시 탑재하기 전에 전체 OS 디스크의 블록 단위로 암호화를 수행합니다. Linux 디스크 암호화는 암호화를 진행 하는 동안 VM을 동시에 사용할 수 없습니다. VM의 성능 특성은 암호화를 완료하는 데 필요한 시간에 큰 차이를 만들 수 있습니다. 이러한 특성은 디스크 크기 및 스토리지 계정이 표준 또는 프리미엄(SSD) 스토리지인지 여부를 포함합니다.

암호화 상태를 확인 하려면 [AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 명령에서 반환 된 **ProgressMessage** 필드를 폴링합니다. OS 드라이브가 암호화되는 동안 VM은 서비스 상태가 되고 진행 중인 프로세스의 중단을 방지하기 위해 SSH를 비활성화합니다. **EncryptionInProgress** 메시지는 암호화가 진행 중인 동안 대부분의 시간에 대해 보고합니다. 몇 시간 후에 **VMRestartPending** 메시지는 VM을 다시 시작하라는 프롬프트를 표시합니다. 다음은 그 예입니다.


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

VM을 다시 부팅하라는 메시지가 표시된 후, VM이 다시 시작된 후 재부팅 및 대상에서 수행되어야 하는 마지막 단계를 위해 2~3분 정도 기다려야 합니다. 암호화가 마지막으로 완료되는 경우 상태 메시지가 변경됩니다. 이 메시지가 제공되면 암호화된 OS 드라이브가 사용할 준비가 되었으며 VM을 다시 사용할 수 있게 됩니다.

다음과 같은 경우에 스냅샷 또는 암호화 직전에 수행된 백업으로 VM을 다시 복원하는 것이 좋습니다.
   - 이전에 설명된 다시 부팅 시퀀스가 발생하지 않는 경우
   - 부팅 정보, 프로세스 메시지 또는 기타 오류 표시기가 이 프로세스 도중에 OS 암호화에 실패했다고 보고하는 경우 메시지의 예는 이 가이드에 설명되어 있는 "분리하지 못했습니다." 오류입니다.

다음 시도를 수행하기 전에 VM의 특성을 다시 평가하고 모든 필수 조건이 충족되는지 확인합니다.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>방화벽 뒤에 있는 Azure Disk Encryption 문제 해결

[격리 된 네트워크의 디스크 암호화를](disk-encryption-isolated-network.md) 참조 하세요.

## <a name="troubleshooting-encryption-status"></a>암호화 상태 문제 해결 

포털은 VM 내에서 암호화 되지 않은 경우에도 디스크를 암호화 된 상태로 표시할 수 있습니다.  낮은 수준의 명령을 사용 하 여 VM 내에서 디스크의 암호를 직접 해독 하는 데 더 높은 수준의 Azure Disk Encryption 관리 명령을 사용 하는 대신이 오류가 발생할 수 있습니다.  더 높은 수준의 명령은 VM 내에서 디스크의 암호를 해독 하는 것이 아니라 vm 외부의 중요 한 플랫폼 수준 암호화 설정 및 VM에 연결 된 확장 설정도 업데이트 합니다.  이러한 설정이 그대로 유지 되지 않으면 플랫폼이 암호화 상태를 보고 하거나 VM을 올바르게 프로 비전 할 수 없습니다.   

PowerShell을 사용 하 여 Azure Disk Encryption를 사용 하지 않도록 설정 하려면 [AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) 뒤에 [AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension)을 사용 합니다. 암호화를 사용 하지 않도록 설정 하기 전에 AzVMDiskEncryptionExtension를 실행 하면 오류가 발생 합니다.

CLI를 사용 하 여 Azure Disk Encryption를 사용 하지 않도록 설정 하려면 [az vm Encryption disable](/cli/azure/vm/encryption)을 사용 합니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 일반적인 Azure Disk Encryption 문제와 해당 문제 해결 방법에 대해 자세히 알아보았습니다. 이 서비스 및 기능에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Security Center에서 디스크 암호화 적용](../../security-center/security-center-apply-disk-encryption.md)
- [휴지 상태의 Azure 데이터 암호화](../../security/fundamentals/encryption-atrest.md)
