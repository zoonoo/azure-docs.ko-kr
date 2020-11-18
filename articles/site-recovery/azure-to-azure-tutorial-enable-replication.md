---
title: Azure Site Recovery를 사용하여 Azure VM 재해 복구를 설정하는 자습서
description: 이 자습서에서는 Azure Site Recovery를 사용하여 다른 Azure 지역에 Azure VM에 대한 재해 복구를 설정합니다.
ms.topic: tutorial
ms.date: 11/03/2020
ms.custom: mvc
ms.openlocfilehash: 90527ad39055e438e4970ad4686f204f72d20cd2
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394103"
---
# <a name="tutorial-set-up-disaster-recovery-for-azure-vms"></a>자습서: Azure VM에 대한 재해 복구 설정

이 자습서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 Azure VM에 대한 재해 복구를 설정하는 방법을 보여줍니다. 이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Azure 설정 및 사용 권한 확인
> * 복제할 VM 준비
> * Recovery Services 자격 증명 모음 만들기
> * VM 복제를 사용하도록 설정

VM에 복제를 사용하도록 설정하여 재해 복구를 설정하면 Site Recovery Mobility Service 확장이 VM에 설치되고 Azure Site Recovery에 등록됩니다. 복제하는 동안 VM 디스크 쓰기는 원본 지역의 캐시 스토리지 계정으로 전송됩니다. 여기에서 대상 지역으로 데이터가 전송되고 데이터에서 복구 지점이 생성됩니다. 재해 복구 중에 VM을 장애 조치(failover)하면 복구 지점이 대상 지역에서 VM을 복원하는 데 사용됩니다.

> [!NOTE]
> 자습서는 가장 간단한 기본 설정을 사용하여 지침을 제공합니다. 사용자 지정된 설정을 사용하여 Azure VM 재해 복구를 설정하려면 [이 문서](azure-to-azure-how-to-enable-replication.md)를 검토하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 시작하기 전에 먼저 다음을 수행합니다.

- [지원되는 지역을 검토합니다](azure-to-azure-support-matrix.md#region-support). 동일한 지리적 위치에 있는 두 지역 간에 Azure VM에 대한 재해 복구를 설정할 수 있습니다.
- 하나 이상의 Azure VM이 필요합니다. [Windows](azure-to-azure-support-matrix.md#windows) 또는 [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) VM이 지원되는지 확인합니다.
- VM [컴퓨팅](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [스토리지](azure-to-azure-support-matrix.md#replicated-machines---storage) 및 [네트워킹](azure-to-azure-support-matrix.md#replicated-machines---networking)요구 사항을 검토합니다.
- 이 자습서에서는 VM이 암호화되지 않았다고 가정합니다. 암호화된 VM에 대한 재해 복구를 설정하려면 [이 문서를 참조하세요](azure-to-azure-how-to-enable-replication-ade-vms.md).

## <a name="check-azure-settings"></a>Azure 설정 확인

대상 지역의 사용 권한 및 설정을 확인합니다.

### <a name="check-permissions"></a>사용 권한 확인

Azure 계정에는 Recovery Services 자격 증명 모음을 만들고 대상 지역에 VM을 만들 수 있는 권한이 필요합니다.

- 무료 Azure 구독을 방금 만든 사용자는 계정 관리자이므로 추가 작업이 필요하지 않습니다.
- 관리자가 아닌 경우, 관리자와 협력하여 필요한 권한을 얻으십시오.
    - **자격 증명 모음 만들기**: 구독에 대한 관리자 또는 소유자 권한 
    - **자격 증명 모음에서 Site Recovery 작업 관리**: *Site Recovery* 기여자 기본 제공 Azure 역할
    - **대상 지역에서 Azure VM 만들기**: 기본 제공되는 Virtual Machine 기여자 역할 또는 다음을 수행할 수 있는 특정 권한:
        - 선택한 가상 네트워크에 VM 만들기
        - Azure Storage 계정에 쓰기
        - Azure 관리 디스크에 쓰기

### <a name="verify-target-settings"></a>대상 설정 확인

검색 복구 중에 원본 지역에서 장애 조치(failover)를 수행하면 대상 지역에 VM이 만들어집니다. 

구독이 대상 지역에 충분한 리소스가 있는지 확인합니다. 원본 지역의 VM과 일치하는 크기로 VM을 만들 수 있어야 합니다. 재해 복구를 설정할 때 Site Recovery는 대상 VM에 대해 동일한 크기(또는 가장 가까운 크기)를 선택합니다.


## <a name="prepare-vms"></a>VM 준비

VM에 아웃바운드 연결이 있고 최신 루트 인증서가 있는지 확인합니다. 


### <a name="set-up-vm-connectivity"></a>VM 연결 설정

복제하려는 VM에는 아웃바운드 네트워크 연결이 필요합니다.

> [!NOTE]
> Site Recovery는 인증 프록시를 사용하여 네트워크 연결을 제어하도록 지원하지 않습니다.

#### <a name="outbound-connectivity-for-urls"></a>URL에 대한 아웃바운드 연결

URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 다음 URL에 대한 액세스를 허용합니다.

| **이름**                  | **상업용**                               | **정부**                                 | **설명** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| 스토리지                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | VM에서 원본 지역의 캐시 스토리지 계정에 데이터를 쓸 수 있도록 합니다. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Site Recovery 서비스 URL에 대한 권한 부여 및 인증을 제공합니다. |
| 복제               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | VM이 Site Recovery 서비스와 통신할 수 있도록 합니다. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | VM이 Site Recovery 모니터링 및 진단 데이터를 쓸 수 있도록 합니다. |

#### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 주소 범위에 대한 아웃바운드 연결

NSG(네트워크 보안 그룹)를 사용하여 연결을 제어하는 경우에는 다음 [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags)(IP 주소 그룹)에 대해 포트 443으로 HTTPS 아웃바운드를 허용하는 서비스 태그 기반 NSG 규칙을 만듭니다.

**Tag** | **허용** 
--- | ---
스토리지 태그  |VM에서 캐시 스토리지 계정에 데이터를 쓸 수 있도록 합니다.   
Azure AD 태그 | Azure AD에 해당하는 모든 IP 주소에 대한 액세스를 허용합니다.   
EventsHub 태그 | Site Recovery 모니터링에 대한 액세스를 허용합니다.  
AzureSiteRecovery 태그 | 모든 지역에서 Site Recovery 서비스에 대한 액세스를 허용합니다.   
GuestAndHybridManagement 태그 | 복제를 사용하도록 설정된 VM에서 실행 중인 Site Recovery Mobility 에이전트를 자동으로 업그레이드하려는 경우에 사용합니다.

필요한 태그 및 태그 지정 예제에 대해 [자세히 알아보세요](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

### <a name="verify-vm-certificates"></a>VM 인증서 확인

VM에 최신 루트 인증서가 있는지 확인합니다. 그렇지 않으면 보안 제약 조건으로 인해 VM을 Site Recovery에 등록할 수 없습니다.

- **Windows VM**: VM에 최신 Windows 업데이트를 모두 설치하여 모든 신뢰할 수 있는 루트 인증서가 머신에 있도록 합니다. 연결이 끊긴 환경에서는 Windows 업데이트 및 인증서 업데이트에 대한 표준 프로세스를 수행합니다.
- **Linux VM**: Linux 배포자가 제공한 지침에 따라 신뢰할 수 있는 최신 루트 인증서 및 CRL(인증서 해지 목록)을 가져옵니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

VM을 복제할 원본 지역을 제외한 지역에서 Recovery Services 자격 증명 모음을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 검색 상자에 *recovery* 를 입력합니다. **서비스** 에서 **Recovery Services 자격 증명 모음** 을 선택합니다.

    ![Recovery Services 자격 증명 모음 검색](./media/azure-to-azure-tutorial-enable-replication/search.png)

3. **Recovery Services 자격 증명 모음** 에서 **추가** 를 선택합니다.
4. **Recovery Services 자격 증명 모음 만들기** > **기본** 에서 자격 증명 모음을 만들 구독을 선택합니다.
5. **리소스 그룹** 에서 자격 증명 모음에 대한 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다.
6. **자격 증명 모음 이름** 에 자격 증명 모음을 식별하기 위한 이름을 지정합니다.
7. **지역** 에서 자격 증명 모음을 저장할 Azure 지역을 선택합니다. [지원되는 지역을 확인합니다](https://azure.microsoft.com/pricing/details/site-recovery/).
8. **검토 + 만들기** 를 선택합니다.

   ![새 자격 증명 모음을 만들기 위한 페이지의 자격 증명 모음 설정](./media/azure-to-azure-tutorial-enable-replication/vault-basics.png)

9. **검토 + 만들기** 에서 **만들기** 를 선택합니다.

10. 자격 증명 모음 배포가 시작됩니다. 알림에서 진행률을 확인합니다.
11. 자격 증명 모음이 배포된 후에는 **대시보드에 고정** 을 선택하여 빠른 참조를 위해 저장합니다. **리소스로 이동** 을 선택하여 새 자격 증명 모음을 엽니다. 
    
    ![배포 후 자격 증명 모음을 열고 대시보드에 고정하는 단추](./media/azure-to-azure-tutorial-enable-replication/vault-deploy.png)

### <a name="enable-site-recovery"></a>Site Recovery 사용

자격 증명 모음 설정에서 **Site Recovery 사용** 을 선택합니다.

![자격 증명 모음에서 Site Recovery를 사용하도록 설정하기 위한 선택](./media/azure-to-azure-tutorial-enable-replication/enable-site-recovery.png)

## <a name="enable-replication"></a>복제 사용

원본 설정을 선택하고 VM 복제를 사용하도록 설정합니다. 

### <a name="select-source-settings"></a>원본 설정 선택

1. 자격 증명 모음 > **Site Recovery** 페이지의 **Azure Virtual Machines** 에서 **복제 사용** 을 선택합니다.

    ![Azure VM에 대한 복제를 사용하도록 설정하는 선택](./media/azure-to-azure-tutorial-enable-replication/enable-replication.png)

2. **원본**> **원본 위치** 에서 VM을 현재 실행 중인 원본 Azure 지역을 선택합니다.
3. **Azure 가상 머신 배포 모델** 에서 기본 **Resource Manager** 설정을 그대로 둡니다.
4. **원본 구독** 에서 VM을 실행 중인 구독을 선택합니다. 자격 증명 모음과 동일한 Azure AD(Active Directory) 테넌트에 있는 구독을 선택할 수 있습니다.
5. **원본 리소스 그룹** 에서 VM이 포함된 리소스 그룹을 선택합니다.
6. **가용성 영역 간 재해 복구** 에서 기본 **아니요** 설정을 그대로 둡니다.

     ![원본 설정](./media/azure-to-azure-tutorial-enable-replication/source.png)

7. **다음** 을 선택합니다.

### <a name="select-the-vms"></a>VM 선택

Site Recovery는 선택한 구독/리소스 그룹과 연결된 VM을 검색합니다.

1. **Virtual Machines** 에서 재해 복구를 사용하도록 설정할 VM을 선택합니다.

     ![복제용 VM을 선택하는 페이지](./media/azure-to-azure-tutorial-enable-replication/select-vm.png)

2. **다음** 을 선택합니다.

### <a name="review-replication-settings"></a>복제 설정 검토

1. **복제 설정** 에서 설정을 검토합니다. Site Recovery는 대상 지역에 대한 기본 설정/정책을 만듭니다. 이 자습서에서는 기본 설정을 사용합니다.
2. **복제 사용** 을 선택합니다.

    ![설정을 사용자 지정하고 복제를 사용하도록 설정하는 페이지](./media/azure-to-azure-tutorial-enable-replication/enable-vm-replication.png)   

3. 알림에서 복제 진행률을 추적합니다.

     ![알림에서 진행 상태 추적](./media/azure-to-azure-tutorial-enable-replication/notification.png) ![성공적인 복제 추적 알림](./media/azure-to-azure-tutorial-enable-replication/notification-success.png)

4. 사용하도록 설정한 VM이 자격 증명 모음 > **복제된 항목** 페이지에 나타납니다.

    ![복제된 항목 페이지의 VM](./media/azure-to-azure-tutorial-enable-replication/replicated-items.png)


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure VM의 재해 복구를 사용하도록 설정했습니다. 이제 훈련을 실행하여 장애 조치(failover)가 예상대로 작동하는지 확인합니다.

> [!div class="nextstepaction"]
> [재해 복구 훈련 실행](azure-to-azure-tutorial-dr-drill.md)
