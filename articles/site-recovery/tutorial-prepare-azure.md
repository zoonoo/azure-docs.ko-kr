---
title: "Azure Site Recovery를 사용하여 리소스 만들기 | Microsoft Docs"
description: "Azure Site Recovery 서비스를 사용하여 온-프레미스 컴퓨터를 복제하기 위해 Azure를 준비하는 방법을 알아봅니다."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 63290127b298efced14ad34e9223840f3229f046
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2018
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>온-프레미스 컴퓨터 복제를 위한 Azure 리소스 준비

[Azure Site Recovery](site-recovery-overview.md) 서비스는 계획된 정전 및 계획되지 않은 정전 중 비즈니스 앱 작동을 유지하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. Site Recovery는 복제, 장애 조치(failover), 복구를 포함하여 온-프레미스 컴퓨터 및 Azure VM(Virtual Machines)의 재해 복구를 오케스트레이션합니다.

이 자습서에서는 온-프레미스 VM(Hyper-V 또는 VMware)이나 Windows/Linux 물리적 서버를 Azure에 복제할 때 Azure 구성 요소를 준비하는 방법을 설명합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 계정에 복제 권한이 있는지 확인
> * Azure 저장소 계정 만들기
> * Azure 네트워크를 설정합니다. 장애 조치(failover) 후 만들어지는 Azure VM이 이 Azure 네트워크에 연결됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure Portal( http://portal.azure.com )에 로그인합니다.

## <a name="verify-account-permissions"></a>계정 권한 확인

Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 관리자가 됩니다. 구독 관리자가 아닌 경우에는 필요한 권한을 할당해 줄 것을 관리자에게 요청합니다. 새 가상 머신에 대한 복제를 사용하려면 다음 권한이 있어야 합니다.

- 선택한 리소스 그룹에서 VM을 만들 수 있는 권한
- 선택한 가상 네트워크에서 VM을 만들 수 있는 권한
- 선택한 저장소 계정에 대한 쓰기 권한

기본 제공되는 ‘Virtual Machine 참가자’ 역할에는 이 권한이 있습니다. Azure Site Recovery 작업을 관리할 수 있는 권한도 있어야 합니다. ‘Site Recovery 참가자’ 역할에는 Recovery Services 자격 증명 모음에서 Site Recovery 작업을 관리하는 데 필요한 모든 사용 권한이 있습니다.

## <a name="create-a-storage-account"></a>저장소 계정 만들기

복제된 컴퓨터의 이미지는 Azure Storage에 유지됩니다. 온-프레미스에서 Azure로 장애 조치할 때 저장소에서 Azure VM이 만들어집니다.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **새로 만들기** -> **저장소** -> **저장소 계정**을 클릭합니다.
2. **저장소 계정 만들기**에서 계정의 이름을 입력합니다. 이 자습서에서는 **contosovmsacct1910171607**이라는 이름을 사용합니다. 이름은 Azure 내에서 고유해야 하며 길이가 3자에서 24자 사이이고 숫자 및 소문자만 사용해야 합니다.
3. **리소스 관리자** 배포 모델을 사용합니다.
4. **범용** > **표준**을 선택합니다. Blob Storage를 선택하지 마세요.
5. 저장소 중복에 대해 기본 **RA-GRS**를 선택합니다.
6. 새 저장소 계정을 만들려는 구독을 선택합니다.
7. 새 리소스 그룹을 선택합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 이 자습서에서는 **ContosoRG**라는 이름을 사용합니다.
8. 저장소 계정에 대한 지리적 위치를 선택합니다. 저장소 계정은 Recovery Services 자격 증명 모음과 동일한 영역에 있어야 합니다. 이 자습서에서는 **유럽 서부** 지역을 사용합니다.

   ![create-storageacct](media/tutorial-prepare-azure/create-storageacct.png)

9. **만들기** 를 클릭하여 저장소 계정을 만들 수 있습니다.

## <a name="create-a-vault"></a>자격 증명 모음 만들기

1. Azure Portal 메뉴에서 **새로 만들기** > **모니터링 및 관리** >
   **백업 및 사이트 복구**를 클릭합니다.
2. **이름**에 자격 증명 모음을 식별하기 위한 이름을 지정합니다. 이 자습서에서는 **ContosoVMVault**를 사용합니다.
3. **contosoRG**라는 기존 리소스 그룹을 선택합니다.
4. 이 자습서 집합에서 사용할 Azure 지역 **유럽 서부**를 지정합니다.
5. 대시보드에서 자격 증명 모음에 빠르게 액세스하려면 **대시보드에 고정** > **만들기**를 클릭합니다.

   ![새 자격 증명 모음](./media/tutorial-prepare-azure/new-vault-settings.png)

   **대시보드** > **모든 리소스**와 주 **Recovery Services 자격 증명 모음** 페이지에 새 자격 증명 모음이 표시됩니다.

## <a name="set-up-an-azure-network"></a>Azure 네트워크를 설정합니다

장애 조치(failover) 후 저장소에서 만들어지는 Azure VM이 이 Azure 네트워크에 연결됩니다.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **새로 만들기** > **네트워킹** >
   **가상 네트워크**를 클릭합니다.
2. 배포 모델로 선택되어 있는 **리소스 관리자**를 허용합니다. 리소스 관리자는 기본 배포 모델입니다.
   - 네트워크 이름을 지정합니다. 이름은 Azure 리소스 그룹 내에서 고유해야 합니다. 이름으로 **ContosoASRnet**을 사용합니다.
   - 기존 리소스 그룹인 **contosoRG**를 사용합니다.
   - 네트워크 주소 범위 10.0.0.0/24를 지정합니다.
   - 이 자습서에서는 서브넷이 필요 없습니다.
   - 만든 네트워크를 배치할 구독을 선택합니다.
   - 위치 **유럽 서부**를 선택합니다. 네트워크는 Recovery Services 자격 증명 모음과 동일한 지역에 있어야 합니다.
3. **만들기**를 클릭합니다.

   ![create-network](media/tutorial-prepare-azure/create-network.png)

   가상 네트워크를 만드는 데 몇 초 정도 걸립니다. 만들어지면 Azure Portal 대시보드에 표시됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure에 재해 복구하기 위해 온-프레미스 VMware 인프라 준비](tutorial-prepare-on-premises-vmware.md)
