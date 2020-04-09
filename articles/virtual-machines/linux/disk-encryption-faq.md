---
title: 자주 묻는 질문 - 리눅스 VM에 대한 Azure 디스크 암호화
description: 이 문서에서는 Linux IaaS VM용 Microsoft Azure 디스크 암호화에 대한 자주 묻는 질문에 대한 답변을 제공합니다.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: ae3743530440c9df9094a0b9784922d2d6a3dfdf
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985408"
---
# <a name="azure-disk-encryption-for-linux-virtual-machines-faq"></a>리눅스 가상 머신 에 대한 Azure 디스크 암호화 FAQ

이 문서에서는 Linux 가상 시스템(VM)용 Azure 디스크 암호화에 대한 자주 묻는 질문(FAQ)에 대한 답변을 제공합니다. 이 서비스에 대한 자세한 내용은 [Azure 디스크 암호화 개요를](disk-encryption-overview.md)참조하십시오.

## <a name="what-is-azure-disk-encryption-for-linux-vms"></a>리눅스 VM에 대 한 Azure 디스크 암호화 는 무엇입니까?

Linux VM용 Azure 디스크 암호화는 Linux의 dm-crypt 기능을 사용하여 OS 디스크* 및 데이터 디스크의 전체 디스크 암호화를 제공합니다. 또한 [암호화FormatAll 기능을](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)사용할 때 임시 리소스 디스크의 암호화를 제공합니다. 콘텐츠는 VM에서 저장소 백 엔드로 암호화된 흐름입니다. 따라서 고객 관리 키로 종단 간 암호화를 제공합니다.
 
[지원되는 VM 및 운영 체제를](disk-encryption-overview.md#supported-vms-and-operating-systems)참조하십시오.

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>GA(일반 공급)에서 Azure Disk Encryption은 어디에 있나요?

Linux VM에 대한 Azure 디스크 암호화는 모든 Azure 공용 리전에서 일반 공급됩니다.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure Disk Encryption에서 사용할 수 있는 사용자 환경은 무엇인가요?

Azure Disk Encryption GA에서는 Azure Resource Manager 템플릿, Azure PowerShell 및 Azure CLI를 지원합니다. 다양한 사용자 환경이 유연성을 제공합니다. VM에 대해 디스크 암호화를 사용하도록 설정하는 세 가지 옵션이 있습니다. Azure 디스크 암호화에서 사용할 수 있는 사용자 환경 및 단계별 지침에 대한 자세한 내용은 [Linux에 대한 Azure 디스크 암호화 시나리오를](disk-encryption-linux.md)참조하십시오.

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure Disk Encryption 비용은 얼마인가요?

Azure 디스크 암호화를 사용하여 VM 디스크를 암호화하는 데는 요금이 부과되지 않지만 Azure Key Vault 사용과 관련된 요금이 있습니다. Azure Key Vault 요금에 대한 자세한 내용은 [Key Vault 가격 책정](https://azure.microsoft.com/pricing/details/key-vault/) 페이지를 참조하세요.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Azure Disk Encryption을 사용하기 시작하려면 어떻게 해야 하나요?

시작하려면 [Azure Disk Encryption 개요](disk-encryption-overview.md)를 읽어보세요.

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Azure 디스크 암호화를 지원하는 VM 크기와 운영 체제는 무엇입니까?

Azure 디스크 암호화 개요 문서에는 Azure 디스크 [암호화를](disk-encryption-overview.md) 지원하는 [VM 크기](disk-encryption-overview.md#supported-vms) 및 [VM 운영 체제가](disk-encryption-overview.md#supported-operating-systems) 나열됩니다.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Azure Disk Encryption을 사용하여 부팅 볼륨과 데이터 볼륨을 모두 암호화할 수 있나요?

예. 부팅 및 데이터 볼륨을 암호화하거나 OS 볼륨을 먼저 암호화하지 않고도 데이터 볼륨을 암호화할 수 있습니다. 

OS 볼륨을 암호화한 후에는 OS 볼륨에서 암호화를 사용하지 않도록 설정이 지원되지 않습니다. 규모 집합의 Linux VM의 경우 데이터 볼륨만 암호화할 수 있습니다.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Azure 디스크 암호화를 통해 마운트되지 않은 볼륨을 암호화할 수 있습니까?

아니요, Azure 디스크 암호화는 탑재된 볼륨만 암호화합니다.

## <a name="what-is-storage-server-side-encryption"></a>저장소 서버 측 암호화란 무엇입니까?

저장소 서버 측 암호화는 Azure 저장소에서 Azure 관리 디스크를 암호화합니다. 관리되는 디스크는 기본적으로 플랫폼 관리 키가 있는 서버 측 암호화(2017년 6월 10일 현재)로 암호화됩니다. 고객 관리 키를 지정하여 자체 키로 관리 디스크의 암호화를 관리할 수 있습니다. 자세한 내용은 [다음을](disk-encryption.md)참조하십시오.
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Azure Disk 암호화는 고객이 관리하는 키를 사용하는 저장소 서버 측 암호화와 어떻게 다른지, 각 솔루션을 언제 사용해야 합니까?

Azure Disk 암호화는 고객 관리 키가 있는 OS 디스크, 데이터 디스크 및 임시 리소스 디스크에 대한 종단 간 암호화를 제공합니다.
- 요구 사항에 위의 모든 암호화 및 종단 간 암호화가 포함된 경우 Azure 디스크 암호화를 사용합니다. 
- 요구 사항에 미사용 데이터만 암호화하는 경우 고객 관리 키로 미사용 데이터만 암호화한 다음 [고객 관리 키와 함께 서버 측 암호화를](disk-encryption.md)사용합니다. Azure 디스크 암호화 및 저장소 서버 측 암호화를 고객 관리 키로 암호화할 수 없습니다. 
- Linux 배포판이 [Azure Disk 암호화에 대한 지원되는 운영 체제](disk-encryption-overview.md#supported-operating-systems) 아래에 나열되지 않았거나 [Windows의 지원되지 않는 시나리오에서](disk-encryption-linux.md#unsupported-scenarios)호출된 시나리오를 사용하는 경우 [고객 관리 키가 있는 서버 측 암호화를 고려하십시오.](disk-encryption.md)
- 조직의 정책에 따라 Azure 관리 키로 미사용 콘텐츠를 암호화할 수 있는 경우 콘텐츠가 기본적으로 암호화됩니다. 관리 디스크의 경우 저장소 내부의 콘텐츠는 기본적으로 플랫폼 관리 키가 있는 서버 측 암호화를 통해 암호화됩니다. 키는 Azure 저장소 서비스에서 관리합니다. 



## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>비밀 또는 암호화 키를 회전하려면 어떻게 해야 합니까?

비밀을 회전하려면 원래 디스크 암호화를 활성화하기 위해 사용한 것과 동일한 명령을 호출하여 다른 Key Vault를 지정합니다. 키 암호화 키를 회전하려면 원래 디스크 암호화를 사용하도록 설정하되 새 키 암호화를 지정하는 데 사용한 것과 동일한 명령을 호출합니다. 

>[!WARNING]
> - 이 VM을 암호화하기 위해 Azure AD 자격 증명을 지정하여 [Azure AD 앱에서](disk-encryption-linux-aad.md) 이전에 Azure 디스크 암호화를 사용한 경우 이 옵션을 계속 사용하여 VM을 암호화해야 합니다. 이 암호화된 VM에서는 Azure Disk 암호화를 사용할 수 없으며, 이는 지원되는 시나리오가 아니므로 이 암호화된 VM에 대한 AAD 응용 프로그램에서 전환하는 것은 아직 지원되지 않습니다.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>원래 키 암호화 키를 사용하지 않은 경우 키 암호화 키를 추가하거나 제거하려면 어떻게 해야 합니까?

키 암호화 키를 추가하려면 키 암호화 키 매개 변수를 다시 전달하는 enable 명령을 호출합니다. 키 암호화 키를 제거하려면 키 암호화 키 매개 변수 없이 enable 명령을 다시 호출합니다.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure Disk Encryption에서 BYOK(Bring Your Own Key)를 가져올 수 있나요?

네, 사용자 고유의 키 암호화 키를 제공할 수 있습니다. 이러한 키는 Azure Disk Encryption에 대한 키 저장소인 Azure Key Vault에서 보호됩니다. 키 암호화 키 지원 시나리오에 대한 자세한 내용은 [Azure 디스크 암호화에 대한 키 자격 증명 모음 만들기 및 구성을](disk-encryption-key-vault.md)참조하십시오.

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Azure에서 만든 키 암호화 키를 사용할 수 있나요?

예, Azure Key Vault를 사용하여 Azure Disk Encryption을 사용하기 위한 키 암호화 키를 생성할 수 있습니다. 이러한 키는 Azure Disk Encryption에 대한 키 저장소인 Azure Key Vault에서 보호됩니다. 키 암호화 키에 대한 자세한 내용은 [Azure 디스크 암호화에 대한 키 자격 증명 모음 만들기 및 구성을](disk-encryption-key-vault.md)참조하십시오.

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>온-프레미스 키 관리 서비스 또는 HSM을 사용하여 암호화 키를 보호할 수 있나요?

온-프레미스 키 관리 서비스 또는 HSM은 Azure Disk Encryption을 통해 암호화 키를 보호하는 데 사용할 수 없습니다. Azure Key Vault 서비스만 사용하여 암호화 키를 보호할 수 있습니다. 키 암호화 키 지원 시나리오에 대한 자세한 내용은 [Azure 디스크 암호화에 대한 키 자격 증명 모음 만들기 및 구성을](disk-encryption-key-vault.md)참조하십시오.

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Azure Disk Encryption을 구성하기 위한 필수 구성 요소는 무엇인가요?

Azure Disk Encryption의 경우 필수 구성 요소가 있습니다. Azure [Disk 암호화에 대한 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md) 을 참조하여 새 키 자격 증명 모음을 만들거나 디스크 암호화 액세스에 대한 기존 키 자격 증명 모음을 설정하여 암호화를 활성화하고 비밀 및 키를 보호합니다. 키 암호화 키 지원 시나리오에 대한 자세한 내용은 [Azure 디스크 암호화에 대한 키 자격 증명 모음 만들기 및 구성을](disk-encryption-key-vault.md)참조하십시오.

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Azure AD 앱(이전 릴리스)을 사용하여 Azure Disk Encryption을 구성하기 위한 필수 구성 요소는 무엇인가요?

Azure Disk Encryption의 경우 필수 구성 요소가 있습니다. Azure Active Directory 응용 프로그램을 만들거나, 새 키 자격 증명 모음을 만들거나, 디스크 암호화 액세스를 위해 기존 키 자격 증명 모음을 설정하여 암호화를 활성화하고, 비밀 및 키를 보호하려면 [Azure AD 콘텐츠가 있는](disk-encryption-linux-aad.md) Azure 디스크 암호화를 참조하십시오. 키 암호화 키 지원 시나리오에 대한 자세한 내용은 [Azure AD를 사용 하 여 Azure 디스크 암호화에 대 한 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault-aad.md)을 참조 합니다.

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Azure AD 앱(이전 릴리스)을 사용한 Azure Disk Encryption은 여전히 지원되나요?
예. Azure AD 앱을 사용한 디스크 암호화는 계속 지원됩니다. 그러나 새 VM을 암호화하는 경우 Azure AD 앱을 사용하여 암호화하기보다 새 메서드를 사용하는 것이 좋습니다. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Azure AD 앱을 사용하여 암호화된 VM을 Azure AD 앱을 사용하지 않는 암호화로 마이그레이션할 수 있나요?
  현재 Azure AD 앱을 사용하여 암호화된 머신을 Azure AD 앱을 사용하지 않는 암호화로 직접 마이그레이션하는 경로는 없습니다. 또한 Azure AD 앱을 사용하지 않는 암호화에서 AD 앱을 사용한 암호화까지의 직접 경로는 없습니다. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure Disk Encryption에서 지원하는 Azure PowerShell 버전은 무엇인가요?

최신 버전의 Azure PowerShell SDK를 사용하여 Azure Disk Encryption을 구성합니다. 최신 버전의 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)을 다운로드합니다. Azure SDK 버전 1.1.0에서는 Azure Disk Encryption을 지원하지 *않습니다*.

> [!NOTE]
> 리눅스 Azure 디스크 암호화 미리보기 확장 "마이크로 소프트.OSTCExtension.AzureDisk암호화포리눅스"는 더 이상 사용되지 않습니다. 이 확장은 Azure 디스크 암호화 미리 보기 릴리스에 대해 게시되었습니다. 테스트 또는 프로덕션 배포에서 확장의 미리 보기 버전을 사용해서는 안 됩니다.

> Linux IaaS VM에서 암호화를 사용하도록 설정하기 위해 Linux VM용 Azure 디스크 암호화 확장을 배포해야 하는 ARM(Azure Resource Manager)과 같은 배포 시나리오의 경우 지원되는 Azure 디스크 암호화 프로덕션 "Microsoft.Azure.Security.AzureDiskEncryptionForLinux"를 사용해야 합니다.

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>사용자 지정 Linux 이미지에 Azure Disk Encryption을 적용할 수 있나요?

사용자 지정 Linux 이미지에는 Azure Disk Encryption을 적용할 수 없습니다. 이전에 언급한 지원 배포판에 대한 Linux 갤러리 이미지만 지원합니다. 사용자 지정 Linux 이미지는 현재 지원되지 않습니다.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>yum 업데이트를 사용하는 Linux Red Hat VM에 업데이트를 적용할 수 있나요?

예, 레드 햇 리눅스 VM에 yum 업데이트를 수행할 수 있습니다.  자세한 내용은 [격리된 네트워크의 Azure 디스크 암호화 를](disk-encryption-isolated-network.md)참조하십시오.

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>권장되는 Linux용 Azure Disk Encryption 워크플로는 무엇인가요?

Linux에서 최상의 결과를 얻기 위해 다음 워크플로가 권장됩니다.
* 필요한 OS 배포판 및 버전에 해당하는 수정되지 않은 주식 갤러리 이미지에서 시작
* 암호화되는 탑재된 드라이브를 백업합니다.  이 백업에서는 암호화가 완료되기 전에 VM이 다시 부팅되는 경우처럼 오류가 발생한 경우 복구가 허용됩니다.
* 암호화(VM 특성 및 연결된 데이터 디스크의 크기에 따라 여러 시간 또는 심지어 며칠이 걸릴 수 있음)
* 필요에 따라 소프트웨어를 사용자 지정하고 이미지에 추가합니다.

이 워크플로가 가능하지 않은 경우 플랫폼 스토리지 계정 계층에서 SSE([스토리지 서비스 암호화](../../storage/common/storage-service-encryption.md)) 사용은 dm-crypt를 사용하는 전체 디스크 암호화에 대한 대안이 될 수 있습니다.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>디스크 "Bek Volume" 또는 "/mnt/azure_bek_disk"란 무엇인가요?

"Bek 볼륨"은 암호화된 Azure VM에 대한 암호화 키를 안전하게 저장하는 로컬 데이터 볼륨입니다.
> [!NOTE]
> 이 디스크의 내용은 삭제하거나 편집하지 않아야 합니다. IaaS VM의 암호화 작업에 암호화 키가 필요하므로 이 디스크를 분리하지 않도록 합니다.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Azure Disk Encryption에는 어떤 암호화 방법이 사용되나요?

Azure 디스크 암호화는 256비트 볼륨 마스터 키를 사용하여 aes-xts-plain64의 암호 해독 기본값을 사용합니다.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>EncryptFormatAll을 사용하고 모든 볼륨 유형을 지정하면 이미 암호화한 데이터 드라이브에서 데이터가 지워지나요?
아니요. Azure Disk Encryption을 사용하여 이미 암호화된 데이터 드라이브에서는 데이터가 지워지지 않습니다. EncryptFormatAll은 OS 드라이브를 다시 암호화하지 않은 것과 유사하게 이미 암호화된 데이터 드라이브를 다시 암호화하지 않습니다. 자세한 내용은 [EncryptFormatAll 기준](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)을 참조하세요.        

## <a name="is-xfs-filesystem-supported"></a>XFS 파일 시스템이 지원되고 있습니까?
XFS 볼륨은 암호화FormatAll을 통해서만 데이터 디스크 암호화에 대해 지원됩니다. 이렇게 하면 볼륨을 다시 포자화하여 이전에 데이터를 지워집니다. 자세한 내용은 [EncryptFormatAll 기준](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)을 참조하세요.

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>암호화된 VM을 백업하고 복원할 수 있습니까? 

Azure Backup은 동일한 구독 및 리전 내에서 암호화된 VM을 백업하고 복원하는 메커니즘을 제공합니다.  지침은 Azure Backup 을 [통해 암호화된 가상 시스템 백업 및 복원을](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)참조하십시오.  암호화된 VM을 다른 지역으로 복원하는 것은 현재 지원되지 않습니다.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>질문을 하거나 의견을 제출할 수 있는 곳은 어디인가요?

[Azure Disk Encryption 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)에서 질문하거나 피드백을 제출할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Disk Encryption와 관련하여 가장 자주 묻는 질문에 대해 자세히 알아 보았습니다. 이 서비스에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 디스크 암호화 개요](disk-encryption-overview.md)
- [Azure Security Center에서 디스크 암호화 적용](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [휴지 상태의 Azure 데이터 암호화](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
