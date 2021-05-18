---
title: Azure Site Recovery에서 암호화된 Azure VM에 대한 복제를 사용하도록 설정
description: 이 문서에서는 Site Recovery를 사용하여 한 Azure 지역에서 다른 Azure 지역으로 Azure Disk Encryption을 사용하는 VM에 대한 복제를 구성하는 방법을 설명합니다.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: fa4d61599e102f9a2580e704ee7a02486067daa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135788"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>다른 Azure 지역으로 Azure Disk Encryption 사용 가능 가상 머신 복제

이 문서에서는 Azure ADE(디스크 암호화)를 사용하도록 설정한 Azure VM을 한 Azure 지역에서 다른 지역으로 복제하는 방법을 설명합니다.

>[!NOTE]
> 현재 Site Recovery는 Windows 운영 체제를 실행하는 VM에 대해 AAD(Azure Active Directory) 유무에 관계없이 ADE를 지원합니다. Linux 운영 체제의 경우 AAD가 없는 ADE만 지원합니다. 또한 AAD 없이 ADE 1.1을 실행하는 머신의 경우 VM은 관리 디스크를 사용해야 합니다. 비관리 디스크가 있는 VM은 지원되지 않습니다. ADE 0.1(AAD 포함)에서 1.1로 전환하는 경우에는 1.1을 사용하도록 설정한 후 복제를 비활성화하고 VM에 대한 복제를 활성화해야 합니다.


## <a name="required-user-permissions"></a><a id="required-user-permissions"></a> 필요한 사용자 권한
Site Recovery를 사용하려면 대상 지역에 키 자격 증명 모음을 만들고 소스 지역 키 자격 증명 모음에서 대상 지역 키 자격 증명 모음으로 키를 복사할 수 있는 권한이 사용자에게 있어야 합니다.

Azure Portal에서 디스크 암호화를 사용하는 VM의 복제를 사용하도록 설정하려면 사용자에게 **원본 지역과 대상 지역** 키 자격 증명 모음에 대한 다음 권한이 있어야 합니다.

- 키 자격 증명 모음 권한
    - 나열, 만들기 및 가져오기
    
- 키 자격 증명 모음 비밀 권한
    - 비밀 관리 작업
        - 가져오기, 나열 및 설정
    
- 키 자격 증명 모음 키 권한(VM이 키 암호화 키를 사용하여 Disk Encryption 키를 암호화하는 경우에만 필요함)
    - 키 관리 작업
        - 가져오기, 나열 및 만들기
    - 암호화 작업
        - 암호 해독 및 암호화

권한을 관리하려면 포털의 키 자격 증명 모음 리소스로 이동합니다. 사용자에게 필요한 권한을 추가합니다. 다음 예에서는 원본 영역에 있는 키 자격 증명 모음 *ContosoWeb2Keyvault* 에 대한 권한을 설정하는 방법을 보여 줍니다.

1. **홈** > **Keyvaults** > **ContosoWeb2KeyVault> 액세스 정책** 으로 이동합니다.

   ![키 자격 증명 모음 권한 창](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. 사용자 권한이 없다고 표시될 수 있습니다. **새로 추가** 를 선택합니다. 사용자 및 권한 정보를 입력합니다.

   ![keyvault 권한](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

DR(재해 복구)을 사용하도록 설정하는 사용자에게 키를 복사할 권한이 없는 경우 적절한 권한이 있는 보안 관리자는 다음 스크립트를 사용하여 암호화 비밀과 키를 대상 지역에 복사할 수 있습니다.

권한 문제를 해결하려면 이 문서의 뒷부분에 있는 [키 자격 증명 모음 권한 문제](#trusted-root-certificates-error-code-151066)를 참조하세요.

>[!NOTE]
>포털에서 디스크 암호화를 사용하는 VM의 복제를 사용하도록 설정하려면 키 자격 증명 모음, 비밀 및 키에 대한 "목록"권한 이상이 필요합니다.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>PowerShell 스크립트를 사용하여 DR 지역에 디스크 암호화 키 복사

1. ["CopyKeys" 원시 스크립트 코드를 엽니다](https://aka.ms/ade-asr-copy-keys-code).
2. 파일에 스크립트를 복사하고 파일 이름을 **Copy-keys.ps1** 로 지정합니다.
3. Windows PowerShell 애플리케이션을 열고 파일을 저장한 폴더로 이동합니다.
4. Copy-keys.ps1을 실행합니다.
5. Azure 자격 증명을 제공하여 로그인합니다.
6. VM의 **Azure 구독** 을 선택합니다.
7. 리소스 그룹이 로드될 때까지 기다렸다가 VM의 **리소스 그룹** 을 선택합니다.
8. 표시된 목록에서 VM을 선택합니다. 디스크 암호화에 대해 사용하도록 설정된 VM만 목록에 있습니다.
9. **대상 위치** 를 선택합니다.

    - **디스크 암호화 키 자격 증명 모음**
    - **키 암호화 키 자격 증명 모음**

   기본적으로 Site Recovery는 대상 지역에 새 Key Vault를 만듭니다. 자격 증명 모음의 이름은 "asr" 접미사를 포함하며, 원본 VM 디스크 암호화 키를 기반으로 합니다. Site Recovery에서 만든 키 자격 증명 모음이 이미 있는 경우 다시 사용됩니다. 필요한 경우 목록에서 다른 키 자격 증명 모음을 선택합니다.

## <a name="enable-replication"></a>복제 사용

예를 들어 주 Azure 지역은 동아시아, 보조 지역은 동남 아시아입니다.

1. 자격 증명 모음에서 **+복제** 를 선택합니다.
2. 다음 필드를 확인합니다.
    - **원본**: VM의 원점이며 이 경우 **Azure** 입니다.
    - **원본 위치**: 가상 머신을 보호할 Azure 지역입니다. 이 예에서 VM 원본 위치는 "동아시아"입니다.
    - **배포 모델**: 원본 컴퓨터의 Azure 배포 모델입니다.
    - **원본 구독**: 원본 가상 머신이 속한 구독입니다. Recovery Services 자격 증명 모음과 동일한 Azure Active Directory 테넌트에 있는 모든 구독일 수 있습니다.
    - **리소스 그룹**: 원본 가상 머신이 속해 있는 리소스 그룹입니다. 선택한 리소스 그룹의 모든 VM은 다음 단계에서 보호를 위해 나열됩니다.

3. **Virtual Machines** > **Virtual Machines 선택** 에서 복제하려는 각 VM을 선택합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 다음 **확인** 을 선택합니다.

4. **설정** 에서 다음과 같은 대상 사이트 설정을 구성할 수 있습니다.

    - **대상 위치**: 원본 가상 머신 데이터가 복제될 위치입니다. Site Recovery는 선택한 컴퓨터의 위치에 따라 적절한 대상 지역의 목록을 제공합니다. Recovery Services 자격 증명 모음의 위치와 동일한 위치를 사용하는 것이 좋습니다.
    - **대상 구독**: 재해 복구에 사용되는 대상 구독입니다. 기본적으로 대상 구독은 원본 구독과 동일합니다.
    - **대상 리소스 그룹**: 모든 복제된 가상 머신이 속하게 될 리소스 그룹입니다. Site Recovery는 기본적으로 새 리소스 그룹을 대상 지역에 만듭니다. 이름에 "asr" 접미사가 붙습니다. Azure Site Recovery에서 만든 리소스 그룹이 이미 있는 경우 다시 사용됩니다. 다음 섹션처럼 리소스 그룹을 사용자 지정할 수도 있습니다. 원본 가상 머신이 호스트되는 지역을 제외한 모든 Azure 지역이 대상 리소스 그룹의 위치가 될 수 있습니다.
    - **대상 가상 네트워크**: 기본적으로 Site Recovery는 대상 지역에 새 가상 네트워크를 만듭니다. 이름에 "asr" 접미사가 붙습니다. 이는 원본 네트워크에 매핑되고 이후의 모든 보호를 위해 사용됩니다. [자세히 알아봅니다](./azure-to-azure-network-mapping.md) 를 확인해 보세요.
    - **대상 스토리지 계정(원본 VM이 관리 디스크를 사용하는 경우)** : 기본적으로 Site Recovery는 원본 VM 스토리지 구성을 모방하는 새 대상 스토리지 계정을 만듭니다. 스토리지 계정이 이미 있는 경우 다시 사용됩니다.
    - **복제본 관리 디스크(원본 VM이 관리 디스크를 사용하는 경우)** : Site Recovery는 대상 지역에 새로운 복제본 관리 디스크를 만들어서 원본 VM의 관리 디스크와 동일한 스토리지 유형(표준 또는 프리미엄)을 원본 VM의 관리 디스크로 미러링합니다.
    - **캐시 스토리지 계정**: Site Recovery는 원본 지역에 *캐시 스토리지* 로 불리는 추가 스토리지 계정이 필요합니다. 원본 VM의 모든 변경 내용이 추적되어 캐시 스토리지 계정으로 전송됩니다. 그런 다음 대상 위치로 복제됩니다.
    - **가용성 집합**: 기본적으로 Site Recovery는 대상 지역에 새 가용성 집합을 만듭니다. 이름에 "asr" 접미사가 있습니다. Site Recovery에서 만든 가용성 집합이 이미 있는 경우 다시 사용됩니다.
    - **디스크 암호화 키 자격 증명 모음**: 기본적으로 Site Recovery는 대상 지역에 새 키 자격 증명 모음을 만듭니다. 원본 VM 디스크 암호화 키를 기반으로 하는 "asr" 접미사가 있습니다. Azure Site Recovery에서 만든 키 자격 증명 모음이 이미 있는 경우 다시 사용됩니다.
    - **키 암호화 Key Vault**: 기본적으로 Site Recovery는 대상 지역에 새 Key Vault를 만듭니다. 이름은 원본 VM 키 암호화 키를 기준으로 하는 "asr" 접미사를 포함합니다. Azure Site Recovery에서 만든 키 자격 증명 모음이 이미 있으면 재사용됩니다.
    - **복제 정책** 은 복구 지점 보존 기록 및 앱 일치 스냅샷 빈도에 대한 설정을 정의합니다. 기본적으로 Site Recovery는 복구 지점 보존의 경우 기본 *24시간* 설정으로, 앱 일치 스냅샷 빈도의 경우 *60분* 설정으로 새 복제 정책을 만듭니다.

## <a name="customize-target-resources"></a>대상 리소스 사용자 지정

Site Recovery 기본 대상 설정을 수정하려면 다음 단계를 수행합니다.

1. "대상 구독" 옆에 있는 **사용자 지정** 을 선택하여 기본 대상 구독을 수정합니다. Azure AD 테넌트에서 사용할 수 있는 구독 목록에서 구독을 선택합니다.

2. 다음의 기본 설정을 수정하려면 "리소스 그룹, 네트워크, 스토리지 및 가용성 집합" 옆의 **사용자 지정** 을 선택합니다.
    - **대상 리소스 그룹** 의 경우 구독 내 대상 위치에 있는 리소스 그룹의 목록에서 리소스 그룹을 선택할 수 있습니다.
    - **대상 가상 네트워크** 의 경우 대상 위치의 가상 네트워크 목록에서 네트워크를 선택합니다.
    - **가용성 집합** 의 경우 가용성 집합 설정이 원본 지역에서 가용성 집합의 일부인 경우 VM에 추가할 수 있습니다.
    - **대상 스토리지 계정** 의 경우 사용할 계정을 선택합니다.

2. 다음 기본 설정을 수정하려면 ‘암호화 설정’ 옆의 **사용자 지정** 을 선택합니다.
   - **대상 디스크 암호화 키 자격 증명 모음** 의 경우 구독의 대상 위치에 있는 키 자격 증명 모음 목록에서 대상 디스크 암호화 키 자격 증명 모음을 선택합니다.
   - **대상 키 암호화 키 자격 증명 모음** 의 경우 구독의 대상 위치에 있는 키 자격 증명 모음 목록에서 대상 키 암호화 키 자격 증명 모음을 선택합니다.

3. **대상 리소스 만들기** > **복제 사용** 을 선택합니다.
4. VM이 복제에 대해 사용하도록 설정된 후 **복제된 항목** 에서 VM 상태를 확인할 수 있습니다.

>[!NOTE]
>초기 복제 중 상태가 새로 고쳐질 때까지 시간이 걸릴 수 있습니다(진행률이 나타나지 않음). 최신 상태를 가져오려면 **새로 고침** 을 클릭합니다.

## <a name="update-target-vm-encryption-settings"></a>대상 VM 암호화 설정 업데이트
다음 시나리오에서는 대상 VM 암호화 설정을 업데이트해야 합니다.
  - VM에서 Site Recovery 복제를 사용하도록 설정했습니다. 나중에 원본 VM에서 디스크 암호화를 사용하도록 설정했습니다.
  - VM에서 Site Recovery 복제를 사용하도록 설정했습니다. 나중에 원본 VM에서 디스크 암호화 키 또는 키 암호화 키를 변경했습니다.

[스크립트](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script)를 사용하여 암호화 키를 대상 지역에 복사한 다음 **Recovery Services 자격 증명 모음** > *복제된 항목* > **속성** > **컴퓨팅 및 네트워크** 에서 대상 암호화 설정을 업데이트할 수 있습니다.

![ADE 설정 업데이트 대화 상자 창](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="troubleshoot-key-vault-permission-issues-during--azure-to-azure-vm-replication&quot;></a><a id=&quot;trusted-root-certificates-error-code-151066&quot;></a>Azure 간에 VM을 복제하는 중 발생한 키 자격 증명 모음 권한 문제 해결

Azure Site Recovery는 적어도 원본 지역 키 자격 증명 모음에 대한 읽기 권한과 대상 지역 키 자격 증명 모음에 대한 쓰기 권한이 있어야 비밀을 읽어 대상 지역 키 자격 증명 모음에 복사할 수 있습니다. 

**원인 1:** **원본 지역 키 자격 증명 모음** 에서 키를 읽을 수 있는 &quot;GET&quot; 권한이 없습니다. </br>
**해결 방법:**  구독 관리자인지 여부에 관계없이 키 자격 증명 모음에 대한 권한이 있어야 합니다.

1. 이 예에서 &quot;ContososourceKeyvault&quot; > **액세스 정책** 인 원본 지역 키 자격 증명 모음으로 이동합니다. 
2. **주체 선택** 에서 사용자 이름을 추가합니다 (예: &quot;dradmin@contoso.com").
3. **키 권한** 에서 가져오기(GET)를 선택합니다. 
4. **비밀 권한** 에서 가져오기(GET)를 선택합니다. 
5. 액세스 정책 저장

**원인 2:** **대상 지역 키 자격 증명 모음** 에서 키를 쓸 수 있는 권한이 없습니다. </br>

*예*: 원본 지역에서 키 자격 증명 모음 *ContososourceKeyvault* 가 있는 VM을 복제하려고 합니다.
원본 지역 키 자격 증명 모음에 대한 모든 권한이 있습니다. 그러나 보호하는 동안 권한이 없는 이미 생성된 키 자격 증명 모음 ContosotargetKeyvault를 선택합니다. 오류가 발생합니다.

[대상 키 자격 증명 모음](#required-user-permissions)에 필요한 권한

**해결 방법:** **홈** > **Keyvaults** > **ContosotargetKeyvault** > **액세스 정책** 으로 이동하고 적절한 권한을 추가합니다.

## <a name="next-steps"></a>다음 단계

테스트 장애 조치(failover) 실행에 대해 [자세히 알아보세요](site-recovery-test-failover-to-azure.md).
