---
title: Azure Site Recovery 서비스를 사용하여 다른 Azure 지역으로 Azure IaaS VM 이동 | Microsoft Docs
description: Azure Site Recovery를 사용하여 Azure 지역 간에 Azure IaaS VM을 이동합니다.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: f6713326045ebd84f1cd484803fbc725ad798d7b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882270"
---
# <a name="move-azure-vms-to-another-region"></a>다른 지역으로 Azure VM 이동

기존 VM의 신뢰성 및 가용성 개선, 관리 효율성 개선, 거버넌스 등 [여기](azure-to-azure-move-overview.md)에 설명된 다양한 이유로 기존 Azure IaaS 가상 머신을 한 Azure 지역에서 다른 Azure 지역으로 이동하려는 다양한 시나리오가 있습니다. BCDR(비즈니스 지속성 및 재해 복구)을 위해 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 머신 및 Azure VM의 재해 복구를 관리하고 오케스트레이션하는 것은 물론, Site Recovery를 사용하여 Azure VM을 보조 지역으로 이동하는 작업을 관리할 수도 있습니다.       

이 자습서에서는 Azure Site Recovery를 사용하여 Azure VM을 다른 Azure 지역으로 이동하는 방법을 보여줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * [필수 조건 확인](#verify-prerequisites)
> * [원본 VM 준비](#prepare-the-source-vms)
> * [대상 Azure 지역 준비](#prepare-the-target-region)
> * [대상 Azure 지역에 데이터 복사](#copy-data-to-the-target-region)
> * [구성 테스트](#test-the-configuration) 
> * [이동 수행](#perform-the-move-to-the-target-region-and-confirm) 
> * [원본 Azure 지역의 리소스 삭제](#discard-the-resource-in-the-source-region) 

> [!IMPORTANT]
> 이 문서에서는 Azure VM을 한 Azure 지역에서 다른 Azure 지역으로 이동하는 방법을 안내하며, 가용성 집합의 VM을 다른 Azure 지역의 영역 고정 VM으로 이동하여 가용성을 높여야 하는 요구 사항이 있는 경우 [여기](move-azure-VMs-AVset-Azone.md) 자습서를 참조하세요.

## <a name="verify-prerequisites"></a>필수 조건 확인

- 이동하려는 Azure 지역에서 Azure VM이 있는지 확인합니다.
- 원하는 [원본 Azure 지역 - 대상 Azure 지역 조합이 지원되는지](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) 확인하여 대상 Azure 지역을 결정합니다.
- [시나리오 아키텍처 및 구성 요소](azure-to-azure-architecture.md)를 이해해야 합니다.
- [제한 사항 및 요구 사항 지원](azure-to-azure-support-matrix.md)을 검토합니다.
- 계정 권한 확인: Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 관리자가 됩니다. 구독 관리자가 아닌 경우에는 필요한 권한을 할당해 줄 것을 관리자에게 요청합니다. VM 복제를 사용하도록 설정하고 기본적으로 Azure Site Recovery를 사용하여 데이터를 복사하려면 다음 권한이 필요합니다.

    1. Azure 리소스에서 VM을 만들 수 있는 권한. 기본 제공되는 ‘Virtual Machine 참가자’ 역할에는 다음을 포함하여 이러한 권한이 있습니다.
        - 선택한 리소스 그룹에서 VM을 만들 수 있는 권한
        - 선택한 가상 네트워크에서 VM을 만들 수 있는 권한
        - 선택한 저장소 계정에 대한 쓰기 권한

    2. Azure Site Recovery 작업을 관리할 수 있는 권한도 있어야 합니다. ‘Site Recovery 참가자’ 역할에는 Recovery Services 자격 증명 모음에서 Site Recovery 작업을 관리하는 데 필요한 모든 사용 권한이 있습니다.

## <a name="prepare-the-source-vms"></a>원본 VM 준비

1. 이동할 Azure VM에 모든 최신 루트 인증서가 있는지 확인합니다. 최신 루트 인증서가 없는 경우 보안 제약 조건으로 인해 대상 Azure 지역에 데이터를 복사하도록 설정할 수 없습니다.

    - Windows VM의 경우 VM에 최신 Windows 업데이트를 모두 설치하여 신뢰할 수 있는 모든 루트 인증서가 컴퓨터에 있도록 합니다. 연결이 끊어진 환경에서 조직의 표준 Windows 업데이트 및 인증서 업데이트 프로세스를 따릅니다.
    - Linux VM의 경우 Linux 배포자가 제공한 지침에 따라 신뢰할 수 있는 최신 루트 인증서 및 인증서 해지 목록을 VM에 가져옵니다.
2. 이동할 VM의 네트워크 연결을 제어하기 위해 인증 프록시를 사용하지 않도록 합니다.
3. 이동하려는 VM이 인터넷에 액세스할 수 없고 방화벽 프록시를 사용하여 아웃바운드 액세스를 제어하는 경우 [여기](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity)서 요구 사항을 확인합니다.
4. 원본 네트워킹 레이아웃 및 현재 사용하는 모든 리소스(부하 분산 장치, NSG, 공용 IP 등)를 식별합니다.

## <a name="prepare-the-target-region"></a>대상 Azure 지역 준비

1. Azure 구독에서 재해 복구에 사용되는 대상 지역에 VM을 만들 수 있도록 허용하는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

2. 구독에 원본 VM과 동일한 크기의 VM을 지원할 수 있을 만큼 충분한 리소스가 있는지 확인합니다. Site Recovery를 사용하여 대상에 데이터를 복사하는 경우 Site Recovery는 동일한 크기 또는 가장 비슷한 크기를 대상 VM에 선택합니다.

3. 원본 네트워킹 레이아웃에서 식별된 모든 구성 요소의 대상 리소스를 만듭니다. 그래야만 대상 Azure 지역으로 전환 후 원본에서 사용하던 모든 기능을 VM에서 그대로 사용할 수 있습니다.

    > [!NOTE]
    > 원본 VM을 복제하도록 설정하면 Azure Site Recovery가 자동으로 가상 네트워크를 검색하고 만들지만, 네트워크를 미리 만들어 두었다가 복제 설정을 위한 사용자 흐름에서 VM에 할당할 수도 있습니다. 하지만 그 외의 리소스는 아래 설명처럼 대상 Azure 지역에 수동으로 만들어야 합니다.

     다음 문서를 참조하여 원본 VM 구성에 따라 가장 일반적으로 사용되는 네트워크 리소스를 만드세요.

    - [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [부하 분산 장치](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [공용 IP](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    그 외의 네트워킹 구성 요소는 네트워킹 [설명서](https://docs.microsoft.com/azure/#pivot=products&panel=network)를 참조하세요. 

4. 대상 Azure 지역으로 최종 전환하기 전에 구성을 테스트하려면 수동으로 대상 Azure 지역에 [비-프로덕션 네트워크를 만듭니다](https://docs.microsoft.com/azure/virtual-network/quick-create-portal). 이렇게 하면 프로덕션과의 간섭을 최소화할 수 있으므로 권장하는 방법입니다.
    
## <a name="copy-data-to-the-target-region"></a>대상 Azure 지역에 데이터 복사
아래 단계는 Azure Site Recovery를 사용하여 대상 Azure 지역에 데이터를 복사하는 방법을 안내합니다.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>원본 지역을 제외한 모든 지역에 자격 증명 모음을 만듭니다.

1. [Azure Portal](https://portal.azure.com) > **Recovery Services**에 로그인합니다.
2. **리소스 만들기** > **관리 도구** > **Backup 및 Site Recovery**를 클릭합니다.
3. **이름**에서 **ContosoVMVault**라는 이름을 지정합니다. 구독이 여러 개인 경우 적절한 구독을 선택합니다.
4. **ContosoRG** 리소스 그룹을 만듭니다.
5. Azure 지역을 지정합니다. 지원되는 지역을 확인하려면 [Azure Site Recovery 가격 정보](https://azure.microsoft.com/pricing/details/site-recovery/)에서 지리적 가용성을 참조하세요.
6. Recovery Services 자격 증명 모음에서 **개요** > **ConsotoVMVault** > **+복제**를 클릭합니다.
7. **원본**에서 **Azure**를 선택합니다.
8. **원본 위치**에서 VM이 현재 실행 중인 원본 Azure 지역을 선택합니다.
9. 리소스 관리자 배포 모델을 선택합니다. 그런 다음, **원본 구독** 및 **원본 리소스 그룹**을 선택합니다.
10. **확인**을 클릭하여 설정을 저장합니다.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Azure VM에 대한 복제를 설정하고 데이터 복사를 시작합니다.

Site Recovery는 구독 및 리소스 그룹과 연관된 VM 목록을 검색합니다.

1. 다음 단계에서 이동할 VM을 선택합니다. 그런 후 **OK**를 클릭합니다.
3. **설정**에서 **재해 복구**를 클릭합니다.
4. **재해 복구 구성** > **대상 지역**에서 복제할 대상 지역을 선택합니다.
5. 이 자습서에서는 다른 기본 설정을 적용합니다.
6. **복제 활성화**를 클릭합니다. VM에 대해 복제를 활성화하는 작업이 시작됩니다.

    ![복제 활성화](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>구성 테스트


1. 자격 증명 모음으로 이동하여 **설정** > **복제된 항목**에서 대상 Azure 지역으로 이동할 가상 머신을 클릭하고, **+테스트 장애 조치(failover)** 아이콘을 클릭합니다.
2. **테스트 장애 조치**에서 장애 조치에 사용할 복구 지점을 선택합니다.

   - **가장 최근에 처리됨**: VM을 Site Recovery 서비스에서 처리된 최신 복구 지점으로 장애 조치합니다. 타임스탬프가 표시됩니다. 이 옵션을 사용하면 데이터를 처리하는 데 시간을 소비하지 않으므로 낮은 RTO(복구 시간 목표)가 제공됩니다.
   - **최신 앱 일치**: 모든 VM을 최신 앱 일치 복구 지점으로 장애 조치합니다. 타임스탬프가 표시됩니다.
   - **사용자 지정**: 복구 지점을 선택합니다.

3. 구성을 테스트하기 위해 Azure VM을 이동할 대상 Azure 가상 네트워크를 선택합니다. 

> [!IMPORTANT]
> VM을 최종적으로 이동할 대상 VM의 프로덕션 네트워크가 아닌 별도의 Azure VM 네트워크를 오류 테스트에 사용하는 것이 좋습니다. 복제를 설정할 때 이미 설정되었습니다.

4. 이동 테스트를 시작하려면 **확인**을 클릭합니다. 진행률을 추적하려면 VM을 클릭하여 해당 속성을 엽니다. 또는 자격 증명 모음 이름 > **설정** > **작업** > **Site Recovery 작업**에서 **테스트 장애 조치** 작업을 클릭하면 됩니다.
5. 장애 조치가 완료되면 Azure Portal > **Virtual Machines**에서 Azure VM 복제본이 표시됩니다. VM이 실행 중이고, 규모가 적절히 조정되었으며, 적절한 네트워크에 연결되어 있는지 확인하세요.
6. 이동 테스트 과정에서 만들어진 VM을 삭제하려면 복제된 항목에서 **테스트 장애 조치(failover) 정리**를 클릭합니다. 테스트와 연관된 모든 관측 내용을 **참고**에 기록하고 저장합니다.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>대상 Azure 지역으로 이동하고 확인합니다.

1.  자격 증명 모음으로 이동하여 **설정** > **복제된 항목**에서 가상 머신을 클릭한 다음, **장애 조치(failover)** 를 클릭합니다.
2. **장애 조치(failover)** 에서 **최신**을 선택합니다. 
3. **장애 조치(failover)를 시작하기 전에 컴퓨터 종료**를 선택합니다. Site Recovery는 장애 조치(failover)를 트리거하기 전에 원본 VM을 종료하려고 합니다. 종료가 실패하더라도 장애 조치는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다. 
4. 작업이 완료되면 VM이 예상대로 대상 Azure 지역에 표시되는지 확인합니다.
5. **복제된 항목**에서 VM을 마우스 오른쪽 단추로 클릭하고 **커밋**을 클릭합니다. 대상 Azure 지역으로 이동하는 프로세스가 끝났습니다. 커밋 작업이 완료될 때까지 기다립니다.

## <a name="discard-the-resource-in-the-source-region"></a>원본 Azure 지역의 리소스 삭제 

1. VM으로 이동합니다.  **복제 사용 안 함**을 클릭합니다.  그러면 VM에 대한 데이터 복사 프로세스가 중지됩니다.  

> [!IMPORTANT]
> ASR 복제 요금이 청구되지 않도록 이 단계를 수행해야 합니다.

원본 리소스를 다시 사용할 계획이 없으면 다음 단계를 진행합니다.

1. [원본 VM 준비](#prepare-the-source-vms)의 4단계에서 나열한 원본 Azure 지역의 모든 관련 네트워크 리소스를 삭제합니다. 
2. 원본 Azure 지역에서 해당 스토리지 계정을 삭제합니다.



## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure VM을 다른 Azure 지역으로 이동했습니다. 이제 이동한 VM에 대해 재해 복구를 구성할 수 있습니다.

> [!div class="nextstepaction"]
> [마이그레이션 후 재해 복구 설정](azure-to-azure-quickstart.md)

