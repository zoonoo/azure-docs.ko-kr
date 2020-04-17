---
title: Linux VM용 Azure Disk Encryption 사용
description: 이 문서에서는 Linux VM에 대한 Microsoft Azure 디스크 암호화를 사용하도록 설정하는 방법에 대한 지침을 제공합니다.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: fa7e085f723d4f4c411f52e045c9437d5cb293b3
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459783"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>리눅스 VM에 대 한 Azure 디스크 암호화 

Azure Disk Encryption은 조직의 보안 및 규정 준수 약정에 따라 데이터를 안전하게 보호하는 데 도움이 됩니다. Linux의 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 기능을 사용하여 Azure 가상 시스템(VM)의 OS 및 데이터 디스크에 대한 볼륨 암호화를 제공하며 [Azure Key Vault와](../../key-vault/index.yml) 통합되어 디스크 암호화 키 및 암호를 제어하고 관리할 수 있습니다. 

[Azure 보안 센터를](../../security-center/index.yml)사용하는 경우 암호화되지 않은 VM이 있는 경우 경고가 표시됩니다. 이 경고는 심각도가 높다고 표시되며 이러한 VM을 암호화하도록 권장합니다.

![Azure Security Center 디스크 암호화 경고](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - 이전에 Azure AD와 함께 Azure 디스크 암호화를 사용하여 VM을 암호화한 경우 이 옵션을 계속 사용하여 VM을 암호화해야 합니다. 자세한 내용은 [Azure AD(이전 릴리스)가 있는 Azure 디스크 암호화를](disk-encryption-overview-aad.md) 참조하십시오. 
> - 특정 권장 사항으로 인해 데이터, 네트워크 또는 컴퓨팅 리소스 사용량이 증가할 수 있으며 이로 인해 라이선스 또는 구독 비용이 발생합니다. 사용자는 유효한 활성 Azure 구독을 포함하여 지원되는 지역에서 Azure에 리소스를 만들어야 합니다.
> - 현재 2세대 VM은 Azure 디스크 암호화를 지원하지 않습니다. 자세한 내용은 [Azure의 2세대 VM 지원을](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) 참조하십시오.

만들기를 사용하여 단 몇 분 만에 Linux용 Azure 디스크 암호화의 기본 을 배우고 [Azure CLI 빠른 시작](disk-encryption-cli-quickstart.md) 또는 Azure [Powershell 빠른 시작을 사용하여 Linux VM을 만들고 암호화할](disk-encryption-powershell-quickstart.md)수 있습니다.

## <a name="supported-vms-and-operating-systems"></a>지원되는 VM 및 운영 체제

### <a name="supported-vms"></a>지원되는 VM

Linux VM은 [다양한 크기로](sizes.md)제공됩니다. [기본, A 시리즈 VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/)또는 이러한 최소 메모리 요구 사항을 충족하지 않는 가상 컴퓨터에서는 Azure 디스크 암호화를 사용할 수 없습니다.

| 가상 머신 | 최소 메모리 요구 사항 |
|--|--|
| 데이터 볼륨만 암호화할 때 Linux VM| 2GB |
| 리눅스 VM 데이터 및 OS 볼륨을 암호화 할 때, 루트 (/) 파일 시스템 사용량이 4GB 이하인 경우 | 8GB |
| 리눅스 VM 데이터 및 OS 볼륨을 암호화 할 때, 루트 (/) 파일 시스템 사용량이 4GB보다 큰 곳 | 루트 파일 시스템 사용 * 2. 예를 들어, 16GB의 루트 파일 시스템 사용량에는 최소 32GB의 RAM이 필요합니다. |

Linux 가상 시스템에서 OS 디스크 암호화 프로세스가 완료되면 메모리를 적게 사용하여 실행되도록 VM을 구성할 수 있습니다. 

Azure 디스크 암호화는 프리미엄 저장소가 있는 VM에서도 사용할 수 있습니다.

Azure 디스크 암호화는 [세대 2 VM)](generation-2.md#generation-1-vs-generation-2-capabilities)및 [Lsv2 시리즈 VM에서](../lsv2-series.md)사용할 수 없습니다. 추가 예외는 [Azure 디스크 암호화: 지원되지 않는 시나리오를](disk-encryption-linux.md#unsupported-scenarios)참조하십시오.

### <a name="supported-operating-systems"></a>지원되는 운영 체제

Azure 디스크 암호화는 Azure 승인 [Linux 배포판의](endorsed-distros.md)하위 집합에서 지원되며, 이는 그 자체가 가능한 모든 Linux 서버 의 하위 집합입니다.

![Azure 디스크 암호화를 지원하는 Linux 서버 배포판의 벤 다이어그램](./media/disk-encryption/ade-supported-distros.png)

Azure에서 승인하지 않는 Linux 서버 배포판은 Azure 디스크 암호화를 지원하지 않습니다. 승인된 배포 및 버전만 Azure 디스크 암호화를 지원합니다.

| Linux 배포 | 버전 | 암호화에 지원되는 볼륨 유형|
| --- | --- |--- |
| Ubuntu | 18.04| OS 및 데이터 디스크 |
| Ubuntu | 16.04| OS 및 데이터 디스크 |
| Ubuntu | 14.04.5</br>[4.15 이상으로 업데이트된 Azure 튜닝 커널 포함](disk-encryption-troubleshooting.md) | OS 및 데이터 디스크 |
| RHEL | 7.7 | OS 및 데이터 디스크(아래 참고 참조) |
| RHEL | 7.6 | OS 및 데이터 디스크(아래 참고 참조) |
| RHEL | 7.5 | OS 및 데이터 디스크(아래 참고 참조) |
| RHEL | 7.4 | OS 및 데이터 디스크(아래 참고 참조) |
| RHEL | 7.3 | OS 및 데이터 디스크(아래 참고 참조) |
| RHEL | 7.2 | OS 및 데이터 디스크(아래 참고 참조) |
| RHEL | 6.8 | 데이터 디스크(아래 참고 사항 참조) |
| RHEL | 6.7 | 데이터 디스크(아래 참고 사항 참조) |
| CentOS | 7.7 | OS 및 데이터 디스크 |
| CentOS | 7.6 | OS 및 데이터 디스크 |
| CentOS | 7.5 | OS 및 데이터 디스크 |
| CentOS | 7.4 | OS 및 데이터 디스크 |
| CentOS | 7.3 | OS 및 데이터 디스크 |
| CentOS | 7.2n | OS 및 데이터 디스크 |
| CentOS | 6.8 | 데이터 디스크 |
| openSUSE | 42.3 | 데이터 디스크 |
| SLES | 12-SP4 | 데이터 디스크 |
| SLES | 12-SP3 | 데이터 디스크 |

> [!NOTE]
> 새로운 Azure 디스크 암호화 구현은 RHELOS 및 RHEL7 종량제 이미지에 대한 데이터 디스크에 대해 지원됩니다.  
>
> ADE는 RHEL Bring-Your-Subscription 골드 이미지에도 지원되지만 구독이 등록된 **후에만** 지원됩니다. 자세한 내용은 [Azure에서 Red Hat 엔터프라이즈 Linux 가져오기 사용자 전용 구독 골드 이미지를](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) 참조하십시오.

## <a name="additional-vm-requirements"></a>추가 VM 요구 사항

Azure 디스크 암호화를 사용하려면 dm-crypt 및 vfat 모듈이 시스템에 있어야 합니다. 기본 이미지에서 vfat를 제거하거나 사용하지 않도록 설정하면 시스템이 키 볼륨을 읽고 후속 재부팅 시 디스크 잠금 해제에 필요한 키를 얻지 못하게 됩니다. 시스템에서 vfat 모듈을 제거하는 시스템 강화 단계는 Azure 디스크 암호화와 호환되지 않습니다. 

암호화를 활성화하기 전에 암호화할 데이터 디스크가 /etc/fstab에 올바르게 나열되어야 합니다. "/dev/sdX" 형식의 디바이스 이름은 특히 암호화가 적용된 후 다시 부팅할 때 동일한 디스크에 연결될 수 없으므로 이 항목에 영구 블록 디바이스 이름을 사용합니다. 이 동작에 대한 자세한 내용은 [Linux VM 디바이스 이름 변경 문제 해결](troubleshoot-device-names-problems.md)을 참조하세요.

/etc/fstab 설정이 탑재에 대해 올바르게 구성되었는지 확인합니다. 이러한 설정을 구성하려면 mount -a 명령을 실행하거나 VM을 다시 부팅하고 다시 탑재를 트리거합니다. 이러한 작업이 완료되면 lsblk 명령의 출력을 확인하여 드라이브가 여전히 탑재되어 있는지 확인합니다. 
- 암호화를 사용하도록 설정하기 전에 /etc/fstab 파일에서 드라이브를 올바르게 탑재하지 않으면 Azure Disk Encryption에서 해당 드라이브를 올바르게 탑재할 수 없습니다.
- Azure Disk Encryption 프로세스는 암호화 프로세스의 일환으로 탑재 정보를 /etc/fstab에서 자체의 구성 파일로 이동합니다. 데이터 드라이브 암호화가 완료된 후 /etc/fstab에서 누락된 항목을 보고 놀라지 마세요.
- 암호화를 시작하기 전에 마운트된 데이터 디스크에 기록할 수 있는 모든 서비스와 프로세스를 중지하고 비활성화하여 재부팅 후 자동으로 다시 시작되지 않도록 해야 합니다. 이러한 파티션에서 파일을 열어 두면 암호화 절차가 다시 마운트되지 않도록 하여 암호화에 실패할 수 있습니다. 
- 다시 부팅한 후 Azure Disk Encryption 프로세스에서 새로 암호화된 디스크를 탑재하는 데 시간이 걸립니다. 이러한 디스크는 다시 부팅한 후에 즉시 사용할 수 없습니다. 다른 프로세스가 액세스할 수 있게 되기 전에 이 프로세스에서 암호화된 드라이브를 시작, 잠금 해제 및 탑재하는 데 시간이 필요합니다. 이 프로세스는 시스템 특성에 따라 다시 부팅 후 2분 이상 걸릴 수 있습니다.

데이터 디스크를 탑재하고 필요한 /etc/fstab 항목을 만드는 데 사용할 수 있는 명령의 예는 [Azure 디스크 암호화 필수 구성 조건 CLI 스크립트(줄](https://github.com/ejarvi/ade-cli-getting-started) 244-248) 및 Azure 디스크 암호화 필수 [구성조건 PowerShell 스크립트에서](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)찾을 수 있습니다. 

## <a name="networking-requirements"></a>네트워킹 요구 사항

Azure 디스크 암호화 기능을 사용하려면 Linux VM이 다음 네트워크 끝점 구성 요구 사항을 충족해야 합니다.
  - 키 자격 증명 모음에 연결하는 토큰을 얻으려면 Linux VM이 Azure Active Directory \[끝점인 login.microsoftonline.com\]연결할 수 있어야 합니다.
  - 키 자격 증명 모음에 암호화 키를 쓰려면 Linux VM이 키 자격 증명 모음 끝점에 연결할 수 있어야 합니다.
  - Linux VM은 Azure 확장 리포지토리를 호스팅하는 Azure 저장소 끝점과 VHD 파일을 호스트하는 Azure 저장소 계정에 연결할 수 있어야 합니다.
  -  보안 정책이 Azure VM에서 인터넷으로 액세스를 제한하는 경우 이전 URI를 확인하고 IP에 대한 아웃바운드 연결을 허용하도록 특정 규칙을 구성할 수 있습니다. 자세한 내용은 [방화벽 뒤에 있는 Azure Key Vault](../../key-vault/general/access-behind-firewall.md)를 참조하세요.  

## <a name="encryption-key-storage-requirements"></a>암호화 키 스토리지 요구 사항  

Azure 디스크 암호화에는 디스크 암호화 키 및 비밀을 제어하고 관리하기 위해 Azure 키 자격 증명 모음이 필요합니다. 키 자격 증명 모음 및 VM은 동일한 Azure 리전 및 구독에 있어야 합니다.

자세한 내용은 [Azure 디스크 암호화에 대한 키 자격 증명 모음 만들기 및 구성을](disk-encryption-key-vault.md)참조하십시오.

## <a name="terminology"></a>용어
다음 표에서는 Azure 디스크 암호화 설명서에 사용되는 몇 가지 일반적인 용어를 정의합니다.

| 용어 | 정의 |
| --- | --- |
| Azure Key Vault | Key Vault는 FIPS(Federal Information Processing Standard) 검증 하드웨어 보안 모듈을 기반으로 하는 암호화 키 관리 서비스입니다. 이러한 표준은 암호화 키 및 중요한 비밀을 보호하는 데 도움이 됩니다. 자세한 내용은 Azure [Key Vault](https://azure.microsoft.com/services/key-vault/) 설명서 및 [Azure 디스크 암호화에 대한 키 자격 증명 모음 만들기 및 구성을](disk-encryption-key-vault.md)참조하십시오. |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli)는 명령줄에서 Azure 리소스를 관리하고 관리하는 데 최적화되어 있습니다.|
| DM-Crypt |[DM-Crypt는](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) Linux VM에서 디스크 암호화를 활성화하는 데 사용되는 Linux 기반의 투명한 디스크 암호화 하위 시스템입니다. |
| 키 암호화 키(KEK) | 비밀을 보호하거나 래핑하는 데 사용할 수 있는 비대칭 키(RSA 2048)입니다. HSM(하드웨어 보안 모듈) 보호 키 또는 소프트웨어 보호 키를 제공할 수 있습니다. 자세한 내용은 Azure [Key Vault](https://azure.microsoft.com/services/key-vault/) 설명서 및 [Azure 디스크 암호화에 대한 키 자격 증명 모음 만들기 및 구성을](disk-encryption-key-vault.md)참조하십시오. |
| PowerShell cmdlet | 자세한 내용은 [Azure PowerShell cmdlet](/powershell/azure/overview)을 참조하세요. |


## <a name="next-steps"></a>다음 단계

- [빠른 시작 - Azure CLI를 사용하여 Linux VM 생성 및 암호화](disk-encryption-cli-quickstart.md)
- [빠른 시작 - Azure Powershell을 사용하여 Linux VM 생성 및 암호화](disk-encryption-powershell-quickstart.md)
- [Linux VM에 대한 Azure Disk Encryption 시나리오](disk-encryption-linux.md)
- [Azure 디스크 암호화 필수 구성 조건 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 디스크 암호화 필수 구성 조건 PowerShell 스크립트](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md)


