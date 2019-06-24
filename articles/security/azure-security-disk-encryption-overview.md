---
title: Azure 디스크 암호화 란?
description: 이 문서에서는 Azure 디스크 암호화의 개요를 제공합니다.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 58c5c8321e505fe2c1c7d19c58fe0d031b75b3e4
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67294873"
---
# <a name="azure-disk-encryption-overview"></a>Azure 디스크 암호화 개요

Azure Disk Encryption에는 보호 하 고 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호할 수 있습니다. 사용 합니다 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) Windows의 기능 및 [Dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) 기능의 Linux Azure virtual machines (Vm)의 OS 및 데이터 디스크를 위한 볼륨 암호화를 제공 합니다. 와 통합 되어 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) 디스크 암호화 키 및 비밀을 관리 및 제어할 수 있도록 및 VM 디스크에 있는 모든 데이터가 Azure storage에서 미사용 암호화 됩니다. Azure 디스크 암호화에 대 한 Windows 및 Linux Vm은 모든 Azure 공용 지역 및 Standard Vm 및 Azure Premium Storage를 사용 하 여 Vm에 대 한 Azure Government 지역 가용성 일반적입니다. 

Azure Security Center를 사용하는 경우 암호화되지 않은 VM이 있는 경우 경고 메시지가 표시됩니다. 이 경고는 심각도가 높다고 표시되며 이러한 VM을 암호화하도록 권장합니다.

![Azure Security Center 디스크 암호화 경고](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> 특정 권장 사항으로 인해 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 라이선스 또는 구독 비용이 발생할 수 있습니다.


## <a name="encryption-scenarios"></a>암호화 시나리오

Azure Disk Encryption을 사용 하 여 업계 표준 암호화 기술을 사용 하 여 Azure Vm을 보호 하 여 조직의 보안 및 규정 준수 요구 사항을 해결할 수 있습니다. 고객 제어 키 (BYOK) 정책으로 부팅 하는 Vm을 구성 하 고 key vault에서 이러한 키의 사용을 감사할 수도 있습니다.

Azure Disk Encryption에서는 다음과 같은 고객 시나리오를 지원합니다.

* 사용 하도록 설정 하 고 지원 되는 Azure 갤러리 이미지에서 만든 새 Vm에서 암호화를 사용 하지 않도록 설정 합니다.
* 사용 하도록 설정 하 고 Azure에서 실행 되는 기존 Vm에서 암호화를 사용 하지 않도록 설정 합니다.
* 사용 하도록 설정 하 고 사전에 암호화 된 VHD 및 암호화 키에서 만든 새 Windows Vm에서 암호화를 사용 하지 않도록 설정 합니다.
* 설정 및 Windows 가상 머신 확장에서 암호화 사용 안 함 설정 합니다.
* Linux 가상 머신 확장 집합에 대 한 데이터 암호화를 설정 하거나 해제 드라이브.
* 사용 하도록 설정 하 고 관리 디스크 Vm의 암호화를 사용 하지 않도록 설정 합니다.
* 기존의 암호화 된 Premium 및 비 Premium Storage VM의 암호화 설정을 업데이트 합니다.
* 암호화 된 Vm 백업 및 복원 합니다.
* 사용자 고유의 암호화 (BYOE) 전환한 사용자 고유의 키 (BYOK) 시나리오는 및 고객에 게 자신의 고유한 암호화 키를 사용 하 여 Azure key vault에 저장 합니다.

또한 Microsoft Azure에서 활성화 되어 있는 경우 Vm에 대 한 다음과 같은 시나리오를 지원 합니다.

* Azure Key Vault와 통합
* [표준 계층 Vm](https://azure.microsoft.com/pricing/details/virtual-machines/) 충족 하는 합니다 [최소 메모리 요구 사항과](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes)합니다. 
* Windows 및 Linux Vm, 관리 되는 디스크 및 확장에서 암호화를 사용 하도록 설정 하면 지원 되는 Azure 갤러리 이미지에서 Vm을 설정 합니다.
* Windows Vm 확장에 대 한 드라이브 Vm을 설정 하 고 관리 디스크 Vm에서 OS 및 데이터 암호화를 사용 하지 않도록 설정.
* Linux Vm의 경우 크기 조정에 대 한 데이터 드라이브에서 암호화를 사용 하지 않도록 설정 Vm을 설정 하 고 관리 디스크 Vm.
* Windows 클라이언트 OS를 실행 하는 Vm에서 암호화를 사용 합니다.
* 탑재 경로가 있는 볼륨에서 암호화를 사용 합니다.
* Mdadm을 사용 하 여 스트라이프 (RAID) 디스크를 사용 하 여 구성 된 Linux Vm에서 암호화를 사용 합니다.
* 데이터 디스크에 LVM을 사용 하는 Linux Vm에서 암호화를 사용 합니다.
* Linux VM OS 및 데이터 디스크에서 암호화를 사용 합니다.

   > [!NOTE]
   > 일부 Linux 배포에 대해서는 OS 드라이브 암호화가 지원되지 않습니다. 자세한 내용은 참조는 [Azure Disk Encryption 지원 되는 운영 체제: Linux](azure-security-disk-encryption-prerequisites.md#linux).
   
* Windows Server 2016에서 Windows 저장소 공간 시작 부분으로 구성 된 Vm에서 암호화를 사용 합니다.
* 백업 및 키 암호화 키 (KEK) 및 비 KEK 시나리오 모두에 대 한 암호화 된 Vm을 복원 합니다.

Azure Disk Encryption은 다음 시나리오, 기능 및 기술에 대 한 작동 하지 않습니다.

* 기본 계층 VM 또는 클래식 VM 만들기 메서드를 통해 만든 Vm을 암호화 합니다.
* OS 드라이브를 암호화 한 경우 OS 드라이브 또는 Linux VM의 데이터 드라이브에서 암호화를 사용 하지 않도록 설정 합니다.
* Linux 가상 머신 확장에 대 한 OS 드라이브 암호화를 설정 합니다.
* 소프트웨어 기반 RAID 시스템으로 구성 된 Windows Vm을 암호화 합니다.
* Linux Vm에서 사용자 지정 이미지를 암호화 합니다.
* 온-프레미스 키 관리 시스템과 통합 합니다.
* Azure 파일(공유 파일 시스템)
* NFS(네트워크 파일 시스템)
* 동적 볼륨

## <a name="encryption-features"></a>암호화 기능

를 사용 하도록 설정 및 Azure Vm에 대 한 Azure Disk Encryption 배포에 사용 하도록 설정 하는 다음 기능을 구성할 수 있습니다.

* 저장소에서 미사용 부팅 볼륨을 보호 하기 위해 OS 볼륨을 암호화 합니다.
* 저장소에서 미사용 데이터 볼륨을 보호 하기 위해 데이터 볼륨을 암호화 합니다.
* Windows Vm에 대 한 OS 및 데이터 드라이브에서 암호화를 사용 하지 않도록 설정 합니다.
* (경우에 OS 드라이브가 암호화 되지 않습니다) Linux Vm에 대 한 드라이브에서 데이터 암호화를 사용 하지 않도록 설정 합니다.
* 암호화 키 및 Azure Key Vault 구독의 암호를 보호 합니다.
* 암호화 된 VM의 암호화 상태를 보고 합니다.
* VM에서 디스크 암호화 구성 설정 제거합니다.
* 백업 및 Azure Backup 서비스를 사용 하 여 암호화 된 Vm을 복원 합니다.

Vm에 대 한 Windows 및 Linux 용 azure Disk Encryption에는 다음이 포함 됩니다.

* [Windows 용 디스크 암호화 확장](../virtual-machines/extensions/azure-disk-enc-windows.md)합니다.
* [Linux 용 디스크 암호화 확장](../virtual-machines/extensions/azure-disk-enc-linux.md)합니다.
* [디스크 암호화 powershell](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0)합니다.
* [Azure CLI 디스크 암호화 cmdlet](/cli/azure/vm/encryption?view=azure-cli-latest)합니다.
* [디스크 암호화 Azure Resource Manager 템플릿을](azure-security-disk-encryption-appendix.md#resource-manager-templates)합니다.

> [!NOTE]
> Azure Disk Encryption으로 VM 디스크를 암호화하는 작업에 대한 추가 요금은 없습니다. 표준 [Key Vault 가격](https://azure.microsoft.com/pricing/details/key-vault/)은 암호화 키 저장에 사용되는 키 자격 증명 모음에 적용됩니다. 

## <a name="encryption-workflow"></a>암호화 워크플로

Windows 및 Linux VM용 디스크 암호화를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. 고객은 Azure Disk Encryption Resource Manager 템플릿, PowerShell cmdlet 또는 Azure CLI를 통해 디스크 암호화를 사용하도록 선택하고 암호화 구성을 지정합니다.

   * 고객 암호화 VHD 시나리오의 경우, 암호화된 VHD를 저장소 계정에, 암호화 키 자료를 Key Vault에 업로드합니다. 그런 다음 새 VM에서 암호화를 사용 하도록 설정 하 고 암호화 구성을 제공 합니다.
   * 지원 되는 갤러리 이미지에서 생성 된 새 Vm을 Azure에서 이미 실행 되는 기존 Vm에 대 한 VM에서 암호화를 사용 하도록 설정 하 고 암호화 구성을 제공 합니다.

1. VM에서 암호화를 사용 하도록 설정 하려면 key vault에서 암호화 키 자료 (Windows 시스템에 대 한 BitLocker 암호화 키) 및 Linux에 대 한 암호를 읽을 Azure 플랫폼에 액세스 권한을 부여 합니다.

1. Azure에서는 암호화 및 Key Vault 구성으로 VM 서비스 모델을 업데이트하고 암호화된 VM을 설정합니다.

   ![Azure의 Microsoft 맬웨어 방지](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>암호 해독 워크플로
Vm 용 디스크 암호화를 사용 하지 않으려면 다음과 같은 개략적인 단계를 완료 합니다.

1. Azure에서 실행 중인 VM에서 암호화 (암호 해독)를 사용 하지 않도록 설정 선택 하 고 암호 해독 구성을 지정 합니다. Azure Disk Encryption Resource Manager 템플릿, PowerShell cmdlet 또는 Azure CLI를 통해 사용하지 않도록 설정할 수 있습니다.

   이 단계는 실행 중인 Windows VM에서 OS 또는 데이터 볼륨 암호화를 해제합니다. 이전 섹션에 나와 있는 것처럼 Linux에 대해 OS 디스크를 암호화하지 않도록 설정하는 것은 지원되지 않습니다. 암호 해독 단계는 OS 디스크가 암호화되지 않는 한 Linux VM의 데이터 드라이브에만 허용됩니다.

1. VM 서비스 모델 및 VM으로 표시 되어 azure 업데이트 암호 해독 합니다. VM의 콘텐츠는 더 이상 미사용 상태에서 암호화되지 않습니다.

   > [!NOTE]
   > 암호화 사용 안 함 작업은 Key Vault 및 암호화 키 자료(Windows 시스템의 경우 BitLocker 암호화 키, Linux의 경우 Passphrase)를 삭제하지 않습니다.
   >
   > Linux OS 디스크 암호화는 사용하지 않도록 설정할 수 없습니다. 암호 해독 단계는 Linux VM에서 데이터 드라이브에만 허용됩니다.
   >
   > OS 드라이브가 암호화되는 경우 Linux에 데이터 디스크 암호화를 사용하지 않도록 설정할 수 없습니다.


## <a name="encryption-workflow-previous-release"></a>암호화 워크플로(이전 릴리스)

Azure Disk Encryption의 새 릴리스는 Azure AD(Azure Active Directory) 애플리케이션 매개 변수 제공에 대한 요구 사항을 제거하여 VM 디스크 암호화를 사용하도록 설정합니다. 새 릴리스를 사용하면 암호화 단계를 사용하는 동안 더 이상 Azure AD 자격 증명을 제공할 필요가 없습니다. 새 릴리스를 사용하는 경우 모든 새 VM은 Azure AD 애플리케이션 매개 변수를 사용하지 않고 암호화되어야 합니다. Azure AD 애플리케이션 매개 변수를 사용하여 이미 암호화된 VM도 여전히 지원되며 Azure AD 구문을 사용하여 계속 유지 관리되어야 합니다. Windows 및 Linux VM(이전 릴리스)용 디스크 암호화를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [암호화 시나리오](#encryption-scenarios) 섹션에 나열된 시나리오에서 암호화 시나리오를 선택합니다.

1. 고객은 Azure Disk Encryption Resource Manager 템플릿, PowerShell cmdlet 또는 Azure CLI를 통해 디스크 암호화를 사용하도록 선택하고 암호화 구성을 지정합니다.

   * 고객 암호화 VHD 시나리오의 경우, 암호화된 VHD를 저장소 계정에, 암호화 키 자료를 Key Vault에 업로드합니다. 그런 다음 새 VM에서 암호화를 사용 하도록 설정 하 고 암호화 구성을 제공 합니다.
   * Marketplace에서 만든 새 Vm을 Azure에서 이미 실행 되는 기존 Vm에 대 한 VM에서 암호화를 사용 하도록 설정 하 고 암호화 구성을 제공 합니다.

1. VM에서 암호화를 사용 하도록 설정 하려면 key vault에서 암호화 키 자료 (Windows 시스템에 대 한 BitLocker 암호화 키) 및 Linux에 대 한 암호를 읽을 Azure 플랫폼에 액세스 권한을 부여 합니다.

1. 암호화 키 자료를 Key Vault에 기록하기 위한 Azure AD 애플리케이션 ID를 제공합니다. 이 단계에서는 2 단계에서 언급 한 시나리오에 대 한 VM에서 암호화 합니다.

1. Azure에서는 암호화 및 Key Vault 구성으로 VM 서비스 모델을 업데이트하고 암호화된 VM을 설정합니다.


## <a name="terminology"></a>용어
다음 표에서 Azure disk encryption 설명서에 사용 되는 일반적인 용어를 정의 합니다.

| 용어 | 정의 |
| --- | --- |
| Azure AD | [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) 계정은 키 자격 증명 모음에서 비밀을 인증, 저장 및 검색하는 데 사용됩니다. |
| Azure Key Vault | Key Vault는 FIPS(Federal Information Processing Standard) 검증 하드웨어 보안 모듈을 기반으로 하는 암호화 키 관리 서비스입니다. 이러한 표준은 암호화 키 및 중요한 비밀을 보호하는 데 도움이 됩니다. 자세한 내용은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 설명서를 참조하세요. |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) 업계에서 통용 되 Windows 볼륨 암호화 하는 기술로 Windows Vm에서 디스크 암호화를 사용 하도록 설정 하는 데 사용 됩니다. |
| BEK | BEK(BitLocker 암호화 키)는 OS 부팅 볼륨 및 데이터 볼륨을 암호화하는 데 사용됩니다. BEK는 Key Vault에서 비밀 보안 수단입니다. |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli)는 명령줄에서 Azure 리소스를 관리하고 관리하는 데 최적화되어 있습니다.|
| DM-Crypt |[Dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) 은 Linux Vm에서 디스크 암호화를 사용 하도록 설정 하는 데 사용 되는 Linux 기반, 투명 한 디스크 암호화 하위 시스템입니다. |
| 키 암호화 키 (KEK) | 비대칭 키 (RSA 2048)을 보호 하거나 래핑하는 암호를 사용할 수 있는 합니다. HSM(하드웨어 보안 모듈) 보호 키 또는 소프트웨어 보호 키를 제공할 수 있습니다. 자세한 내용은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 설명서를 참조하세요. |
| PowerShell cmdlet | 자세한 내용은 [Azure PowerShell cmdlet](/powershell/azure/overview)을 참조하세요. |

## <a name="next-steps"></a>다음 단계

시작 하려면 참조는 [Azure Disk Encryption 필수 구성 요소](azure-security-disk-encryption-prerequisites.md)합니다.

