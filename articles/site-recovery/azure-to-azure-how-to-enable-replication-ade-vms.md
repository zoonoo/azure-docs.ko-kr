---
title: Azure Site Recovery에서 ADE(Azure Disk Encryption) 사용 가능 VM에 대한 복제 구성 | Microsoft Docs
description: 이 문서에서는 Site Recovery를 사용하여 Azure 지역 간의 ADE VM 복제를 구성하는 방법을 설명합니다.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: b3e997a37bb5d030d559b6771b2c0e2f74cc62ab
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277695"
---
# <a name="replicate-azure-disk-encryption-ade-enabled-virtual-machines-to-another-azure-region"></a>다른 Azure 지역으로 ADE(Azure Disk Encryption) 사용 가능 가상 머신 복제

이 문서에서는 Azure 지역 간에 ADE(Azure Disk Encryption) 사용 가능 VM을 복제할 수 있도록 설정하는 방법을 설명합니다.

>[!NOTE]
>Azure Site Recovery에서는 Windows OS에서 실행 중인 Azure VM과 [Azure AD 앱을 통한 암호화가 가능](https://aka.ms/ade-aad-app)한 Azure VM만 지원됩니다.
>

## <a name="required-user-permissions"></a>필요한 사용자 권한
Azure Site Recovery에서는 대상 영역에 키 자격 증명 모음을 만들고 해당 영역에 키를 복사할 수 있는 권한이 사용자에게 있어야 합니다.

포털에서 ADE VM 복제를 사용하도록 설정하려는 사용자에게는 아래 권한이 있어야 합니다.
- 키 자격 증명 모음 권한
    - list
    - 생성
    - 가져오기

-   키 자격 증명 모음 비밀 권한
    - 나열
    - 생성
    - 가져오기

- 키 자격 증명 모음 키 권한(VM이 키 암호화 키를 사용하여 Disk Encryption 키를 암호화하는 경우에만 필요함)
    - 나열
    - 가져오기
    - 생성
    - 암호화
    - 암호 해독

포털에서 키 자격 증명 모음 리소스로 이동한 다음 사용자에게 필요한 권한을 추가하는 방식으로 권한을 관리할 수 있습니다. 예를 들어 아래 단계별 가이드에서는 원본 영역에 있는 키 자격 증명 모음 “ContosoWeb2Keyvault”에 대해 사용하는 방법을 보여 줍니다.


-  “홈> Keyvaults> ContosoWeb2KeyVault> 액세스 정책”으로 이동

![keyvault 권한](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)



- 사용자 권한이 없음을 확인할 수 있으므로, “새로 추가”와 사용자 및 권한을 클릭하여 위에서 언급한 권한을 추가합니다.

![keyvault 권한](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

DR(재해 복구)을 사용하도록 설정하는 사용자에게 키를 복사하는 데 필요한 권한이 없으면 암호화 비밀과 키를 대상 지역에 복사하는 데 필요한 적절한 권한이 있는 보안 관리자에게 아래에 링크로 제공된 스크립트를 제공할 수 있습니다.

권한 문제를 해결하려면 [이 문서](#trusted-root-certificates-error-code-151066)를 참조하세요.
>[!NOTE]
>포털에서 ADE VM 복제를 사용하도록 설정하려면 키 자격 증명 모음, 비밀 및 키에 대한 “나열” 이상의 권한이 필요합니다.
>

## <a name="copy-ade-keys-to-dr-region-using-powershell-script"></a>PowerShell 스크립트를 사용하여 DR 지역에 ADE 키 복사

1. [이 링크](https://aka.ms/ade-asr-copy-keys-code)를 클릭하여 브라우저 창에서 ‘CopyKeys’ 원시 스크립트 코드를 엽니다.
2. 파일에 스크립트를 복사하고 파일 이름을 ‘Copy-keys.ps1’로 지정합니다.
2. Windows PowerShell 애플리케이션을 열고 파일이 있는 폴더 위치로 이동합니다.
3. 'Copy-keys.ps1'을 시작합니다.
4. Azure 로그인 자격 증명을 입력합니다.
5. VM의 **Azure 구독**을 선택합니다.
6. 리소스 그룹이 로드될 때까지 기다렸다가 VM의 **리소스 그룹**을 선택합니다.
7. 표시된 VM 목록에서 VM을 선택합니다. Azure Disk Encryption이 사용하도록 설정된 VM만 목록에 표시됩니다.
8. **대상 위치**를 선택합니다.
9. **디스크 암호화 Key Vault**: 기본적으로 Azure Site Recovery는 원본 VM 디스크 암호화 키를 기반으로 대상 지역에 접미사 "asr"이 포함된 이름으로 새로운 Key Vault를 만듭니다. Azure Site Recovery에서 만든 키 자격 증명 모음이 이미 있으면 재사용 됩니다. 필요한 경우 목록에서 다른 키 자격 증명 모음을 선택할 수 있습니다.
10. **키 암호화 Key Vault**: 기본적으로 Azure Site Recovery는 원본 VM 키 암호화 키를 기반으로 대상 지역에 접미사 "asr"이 포함된 이름으로 새로운 Key Vault를 만듭니다. Azure Site Recovery에서 만든 키 자격 증명 모음이 이미 있으면 재사용 됩니다. 필요한 경우 목록에서 다른 키 자격 증명 모음을 선택할 수 있습니다.

## <a name="enable-replication"></a>복제 사용

이 절차에서는 주 Azure 지역을 동아시아, 보조 지역을 동남 아시아라고 가정합니다.

1. 자격 증명 모음에서 **+복제**를 클릭합니다.
2. 다음 필드를 확인합니다.
    - **원본**: VM의 원점이며 이 경우 **Azure**입니다.
    - **원본 위치**: 가상 머신을 보호할 Azure 지역입니다. 이 그림에서는 원본 위치가 '동아시아'입니다.
    - **배포 모델**: 원본 머신의 Azure 배포 모델입니다.
    - **원본 구독**: 원본 가상 머신이 속한 구독입니다. 복구 서비스 자격 증명 모음이 있는 동일한 Azure Active Directory 테넌트 내에 있는 구독일 수 있습니다.
    - **리소스 그룹**: 원본 가상 머신이 속한 리소스 그룹입니다. 선택한 리소스 그룹 아래의 모든 VM은 다음 단계에서 보호를 위해 나열됩니다.

3. **Virtual Machines > 가상 머신 선택**에서 복제하려는 각 VM을 클릭하여 선택합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 다음 **확인**을 클릭합니다.

4. **설정**에서 대상 사이트 설정을 선택적으로 구성할 수 있습니다.

    - **대상 위치**: 원본 가상 머신 데이터가 복제될 위치입니다. 선택한 컴퓨터 위치에 따라 Site Recovery에서 적합한 대상 지역 목록을 제공합니다. 대상 위치를 Recovery Services 자격 증명 모음 위치와 동일하게 유지하는 것이 좋습니다.
    - **대상 구독**: 재해 복구에 사용되는 대상 구독입니다. 기본적으로 대상 구독은 원본 구독과 동일합니다.
    - **대상 리소스 그룹**: 모든 복제된 가상 머신이 속하는 리소스 그룹입니다. 기본적으로 Azure Site Recovery는 "asr" 접미사를 사용하여 대상 지역에 새 리소스 그룹을 만듭니다. Azure Site Recovery에서 만든 리소스 그룹이 이미 있는 경우 해당 리소스 그룹이 재사용 됩니다. 아래 섹션처럼 리소스 그룹을 사용자 지정할 수도 있습니다. 원본 가상 머신이 호스트되는 지역을 제외한 모든 Azure 지역이 대상 리소스 그룹의 위치가 될 수 있습니다.
    - **대상 가상 네트워크**: 기본적으로 Site Recovery는 "asr" 접미사를 사용하여 대상 지역에 새 가상 네트워크를 만듭니다. 이 가상 네트워크는 원본 네트워크에 매핑되고 이후의 모든 보호를 위해 사용됩니다. [자세히 알아봅니다](site-recovery-network-mapping-azure-to-azure.md) 를 확인해 보세요.
    - **대상 스토리지 계정(원본 VM이 관리 디스크를 사용하지 않는 경우)**: 기본적으로 Site Recovery는 원본 VM 스토리지 구성을 모방하는 새 대상 스토리지 계정을 만듭니다. 저장소 계정이 이미 있는 경우 다시 사용됩니다.
    - **복제본 관리 디스크(원본 VM이 관리 디스크를 사용하는 경우)**: Site Recovery는 대상 지역에 새로운 복제본 관리 디스크를 만들어서 원본 VM의 관리 디스크와 동일한 스토리지 유형(표준 또는 프리미엄)을 원본 VM의 관리 디스크로 미러링합니다.
    - **캐시 스토리지 계정**: Site Recovery는 원본 지역에 캐시 스토리지로 불리는 추가 스토리지 계정이 필요합니다. 원본 VM에서 발생하는 모든 변경 내용이 대상 위치로 복제되기 전에 추적되고 캐시 저장소 계정으로 전송됩니다.
    - **가용성 집합**: 기본적으로 Azure Site Recovery는 이름에 "asr" 접미사가 있는 대상 지역에 새 가용성 집합을 만듭니다. Azure Site Recovery에서 만든 가용성 집합이 이미 있는 경우 해당 가용성 집합이 재사용 됩니다.
    - **디스크 암호화 Key Vault**: 기본적으로 Azure Site Recovery는 원본 VM 디스크 암호화 키를 기반으로 대상 지역에 접미사 "asr"이 포함된 이름으로 새로운 Key Vault를 만듭니다. Azure Site Recovery에서 만든 키 자격 증명 모음이 이미 있으면 재사용 됩니다.
    - **키 암호화 Key Vault**: 기본적으로 Azure Site Recovery는 원본 VM 키 암호화 키를 기반으로 대상 지역에 접미사 "asr"이 포함된 이름으로 새로운 Key Vault를 만듭니다. Azure Site Recovery에서 만든 키 자격 증명 모음이 이미 있으면 재사용 됩니다.
    - **복제 정책**: 복구 지점 보존 기록 및 앱 일치 스냅숏 빈도에 대한 설정을 정의합니다. 기본적으로 Azure Site Recovery는 복구 지점 보존의 경우 '24시간', 앱 일치 스냅숏 빈도의 경우 '60분'인 기본 설정으로 새 복제 정책을 만듭니다.



## <a name="customize-target-resources"></a>대상 리소스 사용자 지정

Site Recovery에서 사용되는 기본 대상 설정을 수정할 수 있습니다.


1. ‘대상 구독’ 옆에 있는 **사용자 지정:** 을 클릭하여 기본 대상 구독을 수정합니다. 동일한 AAD(Azure Active Directory) 테넌트에서 사용할 수 있는 모든 구독 목록에서 구독을 선택합니다.

2. 아래의 기본 설정을 수정하려면 ‘리소스 그룹, 네트워크, 스토리지 및 가용성 집합’ 옆의 **사용자 지정:** 을 클릭합니다.
    - **대상 리소스 그룹**에서 ,구독 내 대상 위치에 있는 모든 리소스 그룹의 목록에서 리소스 그룹을 선택할 수 있습니다.
    - **대상 가상 네트워크**에서 대상 위치의 모든 가상 네트워크 목록에서 네트워크를 선택합니다.
    - **가용성 집합**에서 가용성 집합 설정이 원본 지역 가용성 집합의 일부인 경우 VM에 가용성 집합 설정을 추가할 수 있습니다.
    - **대상 저장소 계정**에서 사용할 계정을 선택합니다.


2. 아래의 기본 설정을 수정하려면 ‘암호화 설정’ 옆의 **사용자 지정:** 을 클릭합니다.
   - **대상 디스크 암호화 키 자격 증명 모음**에서 구독의 대상 위치에 있는 모든 키 자격 증명 모음 목록에서 대상 디스크 암호화 키 자격 증명 모음을 선택합니다.
     - **대상 키 암호화 키 자격 증명 모음**에서 구독의 대상 위치에 있는 모든 키 자격 증명 모음 목록에서 대상 키 암호화 키 자격 증명 모음을 선택합니다.

3. **대상 리소스 만들기** > **복제 사용**을 클릭합니다.
4. VM이 복제에 대해 사용하도록 설정된 후 **복제된 항목**에서 VM 상태를 확인할 수 있습니다.

>[!NOTE]
>초기 복제 중 상태가 새로 고쳐질 때까지 시간이 걸릴 수 있습니다(진행률 없음). 최신 작업 상태를 가져오려면 **새로 고침** 단추를 클릭합니다.
>

## <a name="update-target-vm-encryption-settings"></a>대상 VM 암호화 설정 업데이트
아래 시나리오에서는 대상 VM 암호화 설정을 업데이트해야 합니다.
  - VM에서 Site Recovery 복제를 사용하고 나중에 원본 VM에서 ADE(Azure Disk Encryption)를 사용하도록 설정한 경우
  - VM에서 Site Recovery 복제를 사용하고 나중에 원본 VM에서 디스크 암호화 키 및/또는 키 암호화 키를 변경한 경우

[스크립트](#copy-ade-keys-to-dr-region-using-powershell-script)를 사용하여 암호화 키를 대상 지역에 복사한 다음, **Recovery Services 자격 증명 모음 -&gt; 복제된 항목 -&gt; 속성 -&gt; 계산 및 네트워크**에서 대상 암호화 설정을 업데이트할 수 있습니다.

![update-ade-settings](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="trusted-root-certificates-error-code-151066"></a>Azure 간에 VM을 복제하는 중 발생한 키 자격 증명 모음 권한 문제 해결

**원인 1:** 필요한 권한이 없는 대상 영역에서 이미 생성된 Keyvault를 선택했을 수 있습니다.
Azure Site Recovery에서 만들도록 하지 않고 대상 영역에 이미 생성된 Keyvault를 선택하는 경우, 키 자격 증명 모음에 위에서 언급한 필요한 권한이 있는지 확인합니다.</br>
*예*: 사용자가 원본 영역에 “ContososourceKeyvault”라는 키 자격 증명 모음이 있는 VM을 복제하려고 합니다.
원본 영역 키 자격 증명 모음에 대한 모든 권한이 사용자에게 있지만, 보호 중에 이미 생성된 키 자격 증명 모음 “ContosotargetKeyvault”를 선택합니다. 이 키 자격 증명 모음에는 권한이 없으므로 보호에서 오류가 발생합니다.</br>
**해결 방법:** “홈> Keyvaults> ContososourceKeyvault> 액세스 정책”으로 이동하여 위에 표시된 대로 권한을 추가합니다.

**원인 2:** 암호 해독-암호화 권한이 없는 대상 영역에서 이미 생성된 Keyvault를 선택했을 수 있습니다.
Azure Site Recovery에서 만들도록 하지 않고 대상 영역에 이미 생성된 Keyvault를 선택하는 경우, 원본 영역에 있는 키도 암호화하는 경우 사용자에게 암호 해독-암호화 권한이 있는지 확인합니다.</br>
*예*: 사용자가 원본 영역에 “ContososourceKeyvault”라는 키 자격 증명 모음이 있는 VM을 복제하려고 합니다.
원본 영역 키 자격 증명 모음에 대한 모든 권한이 사용자에게 있지만, 보호 중에 이미 생성된 키 자격 증명 모음 “ContosotargetKeyvault”를 선택합니다. 이 키 자격 증명 모음에는 암호 해독 및 암호화 권한이 없습니다.</br>
**해결 방법:** “홈> Keyvaults> ContososourceKeyvault> 액세스 정책”으로 이동하여 키 권한> 암호화 작업 아래에 권한을 추가합니다.

## <a name="next-steps"></a>다음 단계

테스트 장애 조치(failover) 실행에 대해 [자세히 알아보세요](site-recovery-test-failover-to-azure.md).
