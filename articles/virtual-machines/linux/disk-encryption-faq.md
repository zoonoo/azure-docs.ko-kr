---
title: FAQ-Linux Vm에 대 한 Azure Disk Encryption
description: 이 문서에서는 Linux IaaS Vm 용 Microsoft Azure 디스크 암호화에 대 한 질문과 대답을 제공 합니다.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 7c49467451963ceb52f114430343fafb955ec4f7
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82786991"
---
# <a name="azure-disk-encryption-for-linux-virtual-machines-faq"></a>Linux 가상 머신에 대 한 Azure Disk Encryption FAQ

이 문서에서는 Linux Vm (가상 머신)의 Azure Disk Encryption에 대 한 FAQ (질문과 대답)를 제공 합니다. 이 서비스에 대 한 자세한 내용은 [Azure Disk Encryption 개요](disk-encryption-overview.md)를 참조 하세요.

## <a name="what-is-azure-disk-encryption-for-linux-vms"></a>Linux Vm의 Azure Disk Encryption은 무엇 인가요?

Linux Vm에 대 한 Azure Disk Encryption Linux의 dm 기능을 사용 하 여 OS 디스크 * 및 데이터 디스크에 대 한 전체 디스크 암호화를 제공 합니다. 또한 [EncryptFormatAll 기능](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)을 사용 하는 경우 임시 디스크의 암호화를 제공 합니다. VM에서 저장소 백 엔드로 암호화 된 콘텐츠 흐름입니다. 따라서 고객이 관리 하는 키를 사용 하 여 종단 간 암호화를 제공 합니다.
 
[지원 되는 vm 및 운영 체제를](disk-encryption-overview.md#supported-vms-and-operating-systems)참조 하세요.

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>GA(일반 공급)에서 Azure Disk Encryption은 어디에 있나요?

Linux Vm에 대 한 Azure Disk Encryption은 모든 Azure 공용 지역에서 일반 공급으로 제공 됩니다.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure Disk Encryption에서 사용할 수 있는 사용자 환경은 무엇인가요?

Azure Disk Encryption GA에서는 Azure Resource Manager 템플릿, Azure PowerShell 및 Azure CLI를 지원합니다. 다양한 사용자 환경이 유연성을 제공합니다. Vm에 대해 디스크 암호화를 사용 하도록 설정 하는 세 가지 옵션이 있습니다. Azure Disk Encryption에서 사용할 수 있는 사용자 환경 및 단계별 지침에 대 한 자세한 내용은 [Azure Disk Encryption 시나리오에서 Linux](disk-encryption-linux.md)를 참조 하세요.

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure Disk Encryption 비용은 얼마인가요?

Azure Disk Encryption를 사용 하 여 VM 디스크를 암호화 하는 데는 요금이 부과 되지 않지만 Azure Key Vault 사용과 관련 된 요금은 청구 됩니다. Azure Key Vault 요금에 대한 자세한 내용은 [Key Vault 가격 책정](https://azure.microsoft.com/pricing/details/key-vault/) 페이지를 참조하세요.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Azure Disk Encryption을 사용하기 시작하려면 어떻게 해야 하나요?

시작하려면 [Azure Disk Encryption 개요](disk-encryption-overview.md)를 읽어보세요.

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Azure Disk Encryption 지원 되는 VM 크기 및 운영 체제는 무엇 인가요?

[Azure Disk Encryption 개요](disk-encryption-overview.md) 문서에는 Azure Disk Encryption를 지 원하는 [Vm 크기](disk-encryption-overview.md#supported-vms) 및 [vm 운영 체제가](disk-encryption-overview.md#supported-operating-systems) 나열 되어 있습니다.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Azure Disk Encryption을 사용하여 부팅 볼륨과 데이터 볼륨을 모두 암호화할 수 있나요?

예, 부팅 볼륨과 데이터 볼륨을 모두 암호화 하거나 먼저 OS 볼륨을 암호화 하지 않고도 데이터 볼륨을 암호화할 수 있습니다. 

OS 볼륨을 암호화 한 후에는 OS 볼륨에서 암호화를 사용 하지 않도록 설정할 수 없습니다. 확장 집합에 있는 Linux Vm의 경우 데이터 볼륨만 암호화할 수 있습니다.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Azure Disk Encryption에서 분리 된 볼륨을 암호화할 수 있나요?

아니요, Azure Disk Encryption는 탑재 된 볼륨만 암호화 합니다.

## <a name="what-is-storage-server-side-encryption"></a>Storage 서버 쪽 암호화 란?

저장소 서버 쪽 암호화는 Azure Storage에서 Azure managed disks를 암호화 합니다. 관리 디스크는 플랫폼 관리 키를 사용 하는 서버 쪽 암호화를 사용 하 여 기본적으로 암호화 됩니다 (2017 년 6 월 10 일 기준). 고객이 관리 하는 키를 지정 하 여 고유한 키로 관리 디스크의 암호화를 관리할 수 있습니다. 자세한 내용은 [Azure managed disks의 서버 쪽 암호화](disk-encryption.md)를 참조 하세요.
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>는 고객이 관리 하는 키를 사용 하 여 저장소 서버 쪽 암호화와 Azure Disk Encryption 어떻게 다르며 언제 각 솔루션을 사용 해야 하나요?

Azure Disk Encryption는 고객 관리 키를 사용 하 여 OS 디스크, 데이터 디스크 및 임시 디스크에 대 한 종단 간 암호화를 제공 합니다.
- 요구 사항에 위의 암호화와 종단 간 암호화를 모두 암호화 하는 작업이 포함 된 경우 Azure Disk Encryption를 사용 합니다. 
- 요구 사항에 따라 고객이 관리 하는 키를 사용 하 여 미사용 데이터만 암호화 해야 하는 경우에 [는 고객 관리 키를 사용 하 여 서버 쪽 암호화](disk-encryption.md)를 사용 합니다. 고객 관리 키를 사용 하 여 Azure Disk Encryption 및 저장소 서버 쪽 암호화를 모두 사용 하 여 디스크를 암호화할 수 없습니다. 
- [Azure Disk Encryption에 대해 지원 되는 운영 체제](disk-encryption-overview.md#supported-operating-systems) 에 Linux 배포판이 나열 되어 있지 않거나 [지원 되지 않는 Windows 시나리오](disk-encryption-linux.md#unsupported-scenarios)에서 호출 된 시나리오를 사용 하는 경우 [고객 관리 키를 사용 하 여 서버 쪽 암호화](disk-encryption.md)를 고려 합니다.
- 조직의 정책에서 Azure 관리 키를 사용 하 여 미사용 콘텐츠를 암호화할 수 있는 경우 아무 작업도 필요 하지 않습니다. 콘텐츠는 기본적으로 암호화 됩니다. 관리 디스크의 경우 저장소 내의 콘텐츠는 플랫폼 관리 키를 사용 하는 서버 쪽 암호화를 사용 하 여 기본적으로 암호화 됩니다. 키는 Azure Storage 서비스에서 관리 됩니다. 



## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>비밀 또는 암호화 키를 회전 어떻게 할까요??

비밀을 회전 하려면 원래 사용 했던 것과 동일한 명령을 호출 하 여 디스크 암호화를 사용 하도록 설정 하 고 다른 Key Vault를 지정 합니다. 키 암호화 키를 회전 하려면 원래 사용 했던 것과 동일한 명령을 호출 하 여 디스크 암호화를 사용 하도록 설정 하 고 새 키 암호화를 지정 합니다. 

>[!WARNING]
> - 이 VM을 암호화 하는 데 Azure AD 자격 증명을 지정 하 여 이전에 [AZURE ad 앱과 Azure Disk Encryption](disk-encryption-linux-aad.md) 를 사용한 경우에는이 옵션을 계속 사용 하 여 vm을 암호화 해야 합니다. 이 암호화 된 vm에 대 한 Azure Disk Encryption를 사용할 수 없습니다 .이 시나리오는 지원 되지 않습니다. 즉,이 암호화 된 VM에 대 한 AAD 응용 프로그램에서 외부로의 전환은 아직 지원 되지 않습니다.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>키 암호화 키를 처음 사용 하는 경우 키 암호화 키를 추가 하거나 제거 하는 어떻게 할까요?

키 암호화 키를 추가 하려면 사용 명령을 다시 호출 하 여 키 암호화 키 매개 변수를 전달 합니다. 키 암호화 키를 제거 하려면 키 암호화 키 매개 변수 없이 사용 명령을 다시 호출 합니다.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure Disk Encryption에서 BYOK(Bring Your Own Key)를 가져올 수 있나요?

네, 사용자 고유의 키 암호화 키를 제공할 수 있습니다. 이러한 키는 Azure Disk Encryption에 대한 키 저장소인 Azure Key Vault에서 보호됩니다. 키 암호화 키 지원 시나리오에 대 한 자세한 내용은 [Azure Disk Encryption 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md)을 참조 하세요.

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Azure에서 만든 키 암호화 키를 사용할 수 있나요?

예, Azure Key Vault를 사용하여 Azure Disk Encryption을 사용하기 위한 키 암호화 키를 생성할 수 있습니다. 이러한 키는 Azure Disk Encryption에 대한 키 저장소인 Azure Key Vault에서 보호됩니다. 키 암호화 키에 대 한 자세한 내용은 [Azure Disk Encryption 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md)을 참조 하세요.

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>온-프레미스 키 관리 서비스 또는 HSM을 사용하여 암호화 키를 보호할 수 있나요?

온-프레미스 키 관리 서비스 또는 HSM은 Azure Disk Encryption을 통해 암호화 키를 보호하는 데 사용할 수 없습니다. Azure Key Vault 서비스만 사용하여 암호화 키를 보호할 수 있습니다. 키 암호화 키 지원 시나리오에 대 한 자세한 내용은 [Azure Disk Encryption 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md)을 참조 하세요.

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Azure Disk Encryption을 구성하기 위한 필수 구성 요소는 무엇인가요?

Azure Disk Encryption의 경우 필수 구성 요소가 있습니다. 새 주요 자격 증명 모음을 만들거나 암호화를 사용 하도록 설정 하 고 암호 및 키를 보호 하기 위해 디스크 암호화 액세스용 기존 key vault를 설정 하려면 [Azure Disk Encryption에 대 한 key Vault 만들기 및 구성](disk-encryption-key-vault.md) 문서를 참조 하세요. 키 암호화 키 지원 시나리오에 대 한 자세한 내용은 [Azure Disk Encryption 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md)을 참조 하세요.

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Azure AD 앱(이전 릴리스)을 사용하여 Azure Disk Encryption을 구성하기 위한 필수 구성 요소는 무엇인가요?

Azure Disk Encryption의 경우 필수 구성 요소가 있습니다. Azure Active Directory 응용 프로그램을 만들거나, 새 주요 자격 증명 모음을 만들거나, 암호화를 사용 하도록 설정 하 고 암호 및 키를 보호 하기 위해 디스크 암호화 액세스용으로 기존 key vault를 설정 하려면 [AZURE AD 콘텐츠를 사용](disk-encryption-linux-aad.md) 하 여 Azure Disk Encryption를 참조 하세요. 키 암호화 키 지원 시나리오에 대 한 자세한 내용은 [AZURE AD를 사용 하 여 Azure Disk Encryption에 대 한 key Vault 만들기 및 구성](disk-encryption-key-vault-aad.md)을 참조 하세요.

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Azure AD 앱(이전 릴리스)을 사용한 Azure Disk Encryption은 여전히 지원되나요?
예. Azure AD 앱을 사용한 디스크 암호화는 계속 지원됩니다. 그러나 새 VM을 암호화하는 경우 Azure AD 앱을 사용하여 암호화하기보다 새 메서드를 사용하는 것이 좋습니다. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Azure AD 앱을 사용하여 암호화된 VM을 Azure AD 앱을 사용하지 않는 암호화로 마이그레이션할 수 있나요?
  현재 Azure AD 앱을 사용하여 암호화된 머신을 Azure AD 앱을 사용하지 않는 암호화로 직접 마이그레이션하는 경로는 없습니다. 또한 Azure AD 앱을 사용하지 않는 암호화에서 AD 앱을 사용한 암호화까지의 직접 경로는 없습니다. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure Disk Encryption에서 지원하는 Azure PowerShell 버전은 무엇인가요?

최신 버전의 Azure PowerShell SDK를 사용하여 Azure Disk Encryption을 구성합니다. 최신 버전의 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)을 다운로드합니다. Azure SDK 버전 1.1.0에서는 Azure Disk Encryption을 지원하지 *않습니다*.

> [!NOTE]
> Linux Azure disk encryption preview 확장 "Microsoft. OSTCExtension. AzureDiskEncryptionForLinux"는 더 이상 사용 되지 않습니다. 이 확장은 Azure disk encryption preview 릴리스에 대해 게시 되었습니다. 테스트 또는 프로덕션 배포에는 확장의 미리 보기 버전을 사용해 서는 안 됩니다.

> Linux IaaS VM에서 암호화를 사용 하도록 설정 하기 위해 Linux VM 용 Azure disk encryption 확장을 배포 해야 하는 ARM (Azure Resource Manager)과 같은 배포 시나리오의 경우 Azure disk encryption 프로덕션 지원 확장 "Microsoft. Azure"를 사용 해야 합니다.

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>사용자 지정 Linux 이미지에 Azure Disk Encryption을 적용할 수 있나요?

사용자 지정 Linux 이미지에는 Azure Disk Encryption을 적용할 수 없습니다. 이전에 언급한 지원 배포판에 대한 Linux 갤러리 이미지만 지원합니다. 사용자 지정 Linux 이미지는 현재 지원되지 않습니다.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>yum 업데이트를 사용하는 Linux Red Hat VM에 업데이트를 적용할 수 있나요?

예, Red Hat Linux VM에서 yum 업데이트를 수행할 수 있습니다.  자세한 내용은 [격리 된 네트워크에 대 한 Azure Disk Encryption](disk-encryption-isolated-network.md)를 참조 하세요.

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>권장되는 Linux용 Azure Disk Encryption 워크플로는 무엇인가요?

Linux에서 최상의 결과를 얻기 위해 다음 워크플로가 권장됩니다.
* 필요한 OS 배포판 및 버전에 해당하는 수정되지 않은 주식 갤러리 이미지에서 시작
* 암호화되는 탑재된 드라이브를 백업합니다.  이 백업에서는 암호화가 완료되기 전에 VM이 다시 부팅되는 경우처럼 오류가 발생한 경우 복구가 허용됩니다.
* 암호화(VM 특성 및 연결된 데이터 디스크의 크기에 따라 여러 시간 또는 심지어 며칠이 걸릴 수 있음)
* 필요에 따라 소프트웨어를 사용자 지정하고 이미지에 추가합니다.

이 워크플로가 가능하지 않은 경우 플랫폼 스토리지 계정 계층에서 SSE([스토리지 서비스 암호화](../../storage/common/storage-service-encryption.md)) 사용은 dm-crypt를 사용하는 전체 디스크 암호화에 대한 대안이 될 수 있습니다.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>디스크 "Bek Volume" 또는 "/mnt/azure_bek_disk"란 무엇인가요?

"Bek volume"은 암호화 된 Azure Vm에 대 한 암호화 키를 안전 하 게 저장 하는 로컬 데이터 볼륨입니다.
> [!NOTE]
> 이 디스크의 내용은 삭제하거나 편집하지 않아야 합니다. IaaS VM의 암호화 작업에 암호화 키가 필요하므로 이 디스크를 분리하지 않도록 합니다.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Azure Disk Encryption에는 어떤 암호화 방법이 사용되나요?

Azure Disk Encryption는 256 비트 볼륨 마스터 키가 있는 xts-plain64의 암호 해독 기본값을 사용 합니다.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>EncryptFormatAll을 사용하고 모든 볼륨 유형을 지정하면 이미 암호화한 데이터 드라이브에서 데이터가 지워지나요?
아니요. Azure Disk Encryption을 사용하여 이미 암호화된 데이터 드라이브에서는 데이터가 지워지지 않습니다. EncryptFormatAll은 OS 드라이브를 다시 암호화하지 않은 것과 유사하게 이미 암호화된 데이터 드라이브를 다시 암호화하지 않습니다. 자세한 내용은 [EncryptFormatAll 기준](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)을 참조하세요.        

## <a name="is-xfs-filesystem-supported"></a>XFS 파일 시스템은 지원 되나요?
XFS OS 디스크의 암호화가 지원 됩니다.

XFS 데이터 디스크의 암호화는 EncryptFormatAll 매개 변수를 사용 하는 경우에만 지원 됩니다. 볼륨을 다시 포맷 하 여 이전에 있던 모든 데이터를 지웁니다. 자세한 내용은 [EncryptFormatAll 기준](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)을 참조하세요.

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>암호화 된 VM을 백업 및 복원할 수 있나요? 

Azure Backup는 동일한 구독 및 지역 내에서 암호화 된 VM을 백업 및 복원 하는 메커니즘을 제공 합니다.  자세한 내용은 [Azure Backup를 사용 하 여 암호화 된 가상 컴퓨터 백업 및 복원](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)을 참조 하세요.  암호화 된 VM을 다른 지역으로 복원 하는 것은 현재 지원 되지 않습니다.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>질문을 하거나 의견을 제출할 수 있는 곳은 어디인가요?

[Azure Disk Encryption 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)에서 질문하거나 피드백을 제출할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Disk Encryption와 관련하여 가장 자주 묻는 질문에 대해 자세히 알아 보았습니다. 이 서비스에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Disk Encryption 개요](disk-encryption-overview.md)
- [Azure Security Center에서 디스크 암호화 적용](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [휴지 상태의 Azure 데이터 암호화](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
