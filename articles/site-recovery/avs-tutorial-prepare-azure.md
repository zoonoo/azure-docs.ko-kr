---
title: Azure VMware Solution VM의 재해 복구를 위한 Azure Site Recovery 리소스 준비
description: Azure Site Recovery를 사용하여 Azure VMware Solution 머신의 재해 복구를 위한 Azure 리소스를 준비하는 방법을 알아봅니다.
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 2bd305e3760a8c3d743037e7d90b71f5e9579eda
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395481"
---
# <a name="prepare-azure-site-recovery-resources-for-disaster-recovery-of-azure-vmware-solution-vms"></a>Azure VMware Solution VM의 재해 복구를 위한 Azure Site Recovery 리소스 준비

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 Azure VMware Solution VM의 재해 복구를 설정할 수 있도록 Azure 리소스 및 구성 요소를 준비하는 방법을 설명합니다. [Azure VMware Solution](../azure-vmware/introduction.md)은 Azure에서 프라이빗 클라우드를 제공합니다. 이러한 프라이빗 클라우드에는 전용 운영 체제 미설치 Azure 인프라에서 구축된 vSphere 클러스터가 포함됩니다.

이 문서는 Azure VMware Solution VM의 재해 복구를 설정하는 방법을 보여주는 자습서 시리즈 중 첫 번째 자습서입니다. 


이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure 계정에 복제 권한이 있는지 확인합니다.
> * Recovery Services 자격 증명 모음을 만듭니다. 자격 증명 모음은 VM 및 기타 복제 구성 요소에 대한 메타데이터 및 구성 정보를 유지합니다.
> * Azure VNet(Virtual Network)을 설정합니다. 장애 조치(failover) 후 만들어지는 Azure VM이 이 네트워크에 연결됩니다.

> [!NOTE]
> 자습서는 시나리오에 맞는 가장 간단한 배포 경로를 보여줍니다. 가능한 경우 기본 옵션을 사용하고 가능한 모든 설정과 경로를 보여주지 않습니다. 자세한 내용은 Site Recovery 목차의 방법 섹션에 있는 문서를 참조하세요.

> [!NOTE]
> Azure VMware Solution에 Azure Site Recovery를 사용하는 몇 가지 개념은 온-프레미스 VMware VM의 재해 복구와 중복되므로 그에 따라 설명서가 상호 참조됩니다.

## <a name="before-you-start"></a>시작하기 전에

- Azure에서 Azure VMware Solution 프라이빗 클라우드 [배포](../azure-vmware/tutorial-create-private-cloud.md)
- [VMware](vmware-azure-architecture.md) 재해 복구용 아키텍처 검토
- [VMware](vmware-azure-common-questions.md)에 대한 일반적인 질문 읽기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다. 그런 다음 [Azure Portal](https://portal.azure.com)에 로그인합니다.


## <a name="verify-account-permissions"></a>계정 권한 확인

Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 관리자이며 필요한 권한을 보유하고 있습니다. 구독 관리자가 아닌 경우에는 관리자와 협력하여 필요한 권한을 할당받습니다. 새 가상 머신에 대한 복제를 사용하려면 다음 작업을 수행할 권한이 있어야 합니다.

- 선택한 리소스 그룹에 VM 만들기
- 선택한 가상 네트워크에 VM 만들기
- Azure Storage 계정에 씁니다.
- Azure 관리 디스크에 씁니다.

이러한 작업을 완료하려면 계정에 가상 머신 참가자 기본 제공 역할이 할당되어야 합니다. 또한 자격 증명 모음에서 Site Recovery 작업을 관리하려면 계정에 Site Recovery 참가자 기본 제공 역할이 할당되어야 합니다.


## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

1. Azure Portal 메뉴에서 **리소스 만들기** 를 선택하고, Marketplace에서 **복구** 를 검색합니다.
2. 검색 결과에서 **Backup 및 Site Recovery** 를 클릭하고, Backup 및 Site Recovery 페이지에서 **만들기** 를 클릭합니다. 
3. **Recovery Services 자격 증명 모음 만들기** 페이지에서 **구독** 을 선택합니다. 지금은 **Contoso 구독** 을 사용하고 있습니다.
4. 기존 **리소스 그룹** 을 선택하거나 새 리소스 그룹을 만듭니다. 이 자습서에서는 **contosoRG** 를 사용합니다.
5. **자격 증명 모음 이름** 에 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 이 자습서의 집합의 경우 **ContosoVMVault** 를 사용합니다.
6. **지역** 에서 자격 증명 모음을 배치할 지역을 선택합니다. **서유럽** 를 사용합니다.
7. **검토 + 만들기** 를 선택합니다.

   ![Recovery Services 자격 증명 모음 만들기 페이지의 스크린샷](./media/tutorial-prepare-azure/new-vault-settings.png)

   이제 **대시보드** > **모든 리소스** 와 주 **Recovery Services 자격 증명 모음** 페이지에 새 자격 증명 모음이 표시됩니다.

## <a name="set-up-an-azure-network"></a>Azure 네트워크를 설정합니다

 Azure VMware Solution VM이 Azure 관리 디스크에 복제됩니다. 장애 조치(failover)가 발생하는 경우 이러한 관리 디스크에서 Azure VM이 생성되고 이 절차에서 지정된 Azure 네트워크에 조인됩니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **네트워킹** > **가상 네트워크** 를 선택합니다.
2. 배포 모델로 선택되어 있는 **리소스 관리자** 를 유지합니다.
3. **이름** 에 네트워크 이름을 입력합니다. 이름은 Azure 리소스 그룹 내에서 고유해야 합니다. 이 자습서에서는 **ContosoASRnet** 을 사용합니다.
4. **주소 공간** 에 가상 네트워크의 주소 범위를 CDR 표기법으로 입력합니다. 현재 **10.1.0.0/24** 를 사용하고 있습니다.
5. **구독** 에서 네트워크를 만드는 데 사용할 구독을 선택합니다.
6. 네트워크를 만들 **리소스 그룹** 을 지정합니다. 기존 리소스 그룹인 **contosoRG** 를 사용합니다.
7. **위치** 에서 Recovery Services 자격 증명 모음이 생성된 지역과 동일한 지역을 선택합니다. 이 자습서에서는 **서유럽** 입니다. 네트워크는 자격 증명 모음과 동일한 지역에 있어야 합니다.
8. **주소 범위** 에 네트워크 범위를 입력합니다. 여기서는 **10.1.0.0/24** 를 사용하고 서브넷은 사용하지 않습니다.
9. 네트워크에 서비스 엔드포인트 또는 방화벽이 없는 기본 DDoS 보호의 기본 옵션을 그대로 둡니다.
9. **만들기** 를 선택합니다.

   ![가상 네트워크 만들기 옵션의 스크린샷](media/tutorial-prepare-azure/create-network.png)

가상 네트워크를 만드는 데 몇 초 정도 걸립니다. 가상 네트워크가 만들어지면 Azure Portal 대시보드에 표시됩니다.




## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [인프라 준비](avs-tutorial-prepare-avs.md)
- Azure 네트워크에 대해 [자세히 알아보기](../virtual-network/virtual-networks-overview.md).
- Managed Disks에 대해 [자세히 알아보기](../virtual-machines/managed-disks-overview.md)
