---
title: Azure 리소스 이동 기를 사용 하 여 다른 지역으로 리소스 이동
description: Azure 리소스 이동 기를 사용 하 여 리소스 그룹 내 리소스를 다른 지역으로 이동 하는 방법을 알아봅니다.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 716928761d23c2cf04ebcc72e253ad7884408065
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061848"
---
# <a name="move-resources-across-regions-from-resource-group"></a>리소스 그룹에서 영역 간 리소스 이동

이 문서에서는 특정 리소스 그룹의 리소스를 다른 Azure 지역으로 이동 하는 방법에 대해 알아봅니다. 리소스 그룹에서 이동 하려는 리소스를 선택 합니다. 그런 다음 [Azure 리소스](overview.md)이동 기를 사용 하 여 이동 합니다.

> [!IMPORTANT]
> Azure 리소스 이동 기는 현재 공개 미리 보기로 제공 됩니다.


## <a name="prerequisites"></a>필수 구성 요소

- 이동 하려는 리소스가 있는 구독에 대 한 *소유자* 액세스 권한이 필요 합니다.
    - Azure 구독에서 특정 원본 및 대상 매핑에 대 한 리소스를 처음 추가 하는 경우 리소스 이동 기가 구독에서 신뢰 하는 [시스템 할당 관리 id](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (이전에는 MSI (관리 서비스 식별)로 알려짐)를 만듭니다.
    - Id를 만들고 필요한 역할 (원본 구독의 참가자 또는 사용자 액세스 관리자)을 할당 하려면 리소스를 추가 하는 데 사용 하는 계정에 구독에 대 한 *소유자* 권한이 있어야 합니다. Azure 역할에 대해 [자세히 알아보세요](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) .
- 구독에는 대상 지역에 원본 리소스를 만드는 데 충분 한 할당량이 필요 합니다. 그렇지 않으면 추가 제한을 요청 합니다. [자세히 알아보기](/azure/azure-resource-manager/management/azure-subscription-service-limits).
- Vm을 이동 하는 대상 지역과 관련 된 가격 책정 및 요금을 확인 합니다. [가격 계산기](https://azure.microsoft.com/pricing/calculator/) 를 사용 하 여 도움을 줍니다.
- 이동 하려는 리소스가 리소스 이동 기에서 지원 되는지 확인 합니다.
    - Azure Vm 및 연결 된 디스크
    - NIC
    - 가용성 집합
    - Azure 가상 네트워크
    - 공용 IP 주소
    - NSG(네트워크 보안 그룹)
    - 내부 및 공용 부하 분산 장치
    - Azure SQL database 및 탄력적 풀


## <a name="check-vm-requirements"></a>VM 요구 사항 확인

1. 이동 하려는 Vm이 지원 되는지 확인 합니다.

    - 지원 되는 Windows Vm을 [확인](support-matrix-move-region-azure-vm.md#windows-vm-support) 합니다.
    - 지원 되는 Linux Vm 및 커널 버전을 [확인](support-matrix-move-region-azure-vm.md#linux-vm-support) 합니다.
    - 지원 되는 [계산](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [저장소](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)및 [네트워킹](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) 설정을 확인 하세요.
2. Vm에 최신 신뢰할 수 있는 루트 인증서와 업데이트 된 CRL (인증서 해지 목록)이 있는지 확인 합니다. 
    - Windows를 실행 하는 Azure Vm에서 최신 Windows 업데이트를 설치 합니다.
    - Linux를 실행 하는 Vm에서 Linux 배포자 지침에 따라 컴퓨터에 최신 인증서 및 CRL이 있는지 확인 합니다. 
3. Vm에서 아웃 바운드 연결 허용:
    - URL 기반 방화벽 프록시를 사용 하 여 아웃 바운드 연결을 제어 하는 경우 이러한 [url](support-matrix-move-region-azure-vm.md#url-access) 에 대 한 액세스 허용
    - NSG (네트워크 보안 그룹) 규칙을 사용 하 여 아웃 바운드 연결을 제어 하는 경우 이러한 [서비스 태그 규칙](support-matrix-move-region-azure-vm.md#nsg-rules)을 만듭니다.

## <a name="select-resources-to-move"></a>이동할 리소스 선택

이동 하려는 리소스를 선택 합니다. 원본 지역 구독의 대상 영역으로 리소스를 이동 합니다. 구독을 변경 하려면 리소스를 이동한 후에 수행할 수 있습니다.

1. Azure Portal에서 관련 리소스 그룹을 엽니다.
2. 리소스 그룹 페이지에서 이동 하려는 리소스를 선택 합니다.
3. **Move**  >  **다른 지역으로 이동 이동을**선택 합니다.

    ![리소스를 다른 지역으로 이동 하기 위한 선택](./media/move-region-within-resource-group/select-move-region.png)
    
4. **원본 + 대상**에서 리소스를 이동 하려는 대상 지역을 선택 합니다. **다음**을 선택합니다.


    ![대상 지역을 선택 하는 원본 및 대상 페이지](./media/move-region-within-resource-group/source-target.png)


7. **이동할 리소스**에서 **다음**을 선택 합니다.  
8. **리소스 선택**에서 이동할 리소스를 선택 합니다. 이동에 대해 지원 되는 리소스만 추가할 수 있습니다. 그런 후 **완료**를 선택합니다.
9. **리소스 이동**에서 **다음**을 선택 합니다. 
10. **검토 + 추가**에서 원본 및 대상 세부 정보를 확인 합니다.
11. 이동 중인 리소스에 대 한 메타 데이터가이 목적을 위해 생성 된 리소스 그룹에 저장 되며, 리소스 이동 기가 시스템 관리 id를 만들어 구독 리소스에 액세스할 수 있도록 하는 것을 이해 하 고 있는지 확인 합니다.
1. **계속** 을 선택 하 여 리소스 추가를 시작 합니다.

    ![세부 정보를 확인 하 고 이동을 계속 하는 요약 페이지](./media/move-region-within-resource-group/summary.png)    

11. 리소스 추가 작업이 시작 됩니다. 작업이 완료 되 면 알림이 추가 된 것으로 표시 되 고 배포에 성공 했습니다.
13. 알림에서 **이동에 대 한 리소스 추가**를 선택 합니다.

    ![알림에 표시 되는 메시지](./media/move-region-within-resource-group/notification.png)    


14. 알림을 선택한 후 선택한 리소스가 Azure 리소스 이동 기 허브의 이동 컬렉션에 추가 됩니다.  리소스 이동 기는 종속성을 확인 하 고 대상 영역으로 리소스를 이동 하기 시작 합니다.

## <a name="resolve-dependencies"></a>종속성 오류 해결

이동 하는 리소스는 **전체 지역** 페이지에서 *준비 보류 중* 상태로 표시 됩니다. 다음과 같이 유효성 검사를 시작 합니다.

1. 리소스가 **문제** 열에서 *종속성 유효성 검사* 메시지를 표시 하는 경우 **종속성 유효성 검사** 단추를 선택 합니다. 유효성 검사 프로세스가 시작 됩니다.

    ![종속성의 유효성을 검사 하는 단추](./media/move-region-within-resource-group/validate-dependencies.png)

2. 종속성이 있는 경우 **종속성 추가**를 선택 합니다. 
3. **종속성 추가**에서 **종속성 추가**> 종속 리소스를 선택 합니다. 알림에서 진행률을 모니터링 합니다.

    ![종속성 추가 단추](./media/move-region-within-resource-group/add-dependencies.png)

3. 필요한 경우 종속성을 추가 하 고 필요에 따라 종속성의 유효성을 검사 합니다. 리소스에 최신 상태가 표시 되도록 하려면 **새로 고침** 을 선택 합니다.

4. **전체 지역** 페이지에서 리소스가 현재 *준비 보류 중* 상태 이며 아무런 문제도 발생 하지 않았는지 확인 합니다.

    ![모든 리소스에 대 한 준비 보류 중 상태를 표시 하는 페이지](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>원본 리소스 그룹 이동 

리소스를 준비 하 고 이동 하기 전에 원본 리소스 그룹이 대상 지역에 있어야 합니다. 

### <a name="prepare-to-move-the-source-resource-group"></a>원본 리소스 그룹 이동 준비

다음과 같이 준비 합니다.

1. **전체 지역**에서 **준비**> 원본 리소스 그룹을 선택 합니다.
2. **리소스 준비**에서 **준비**를 선택 합니다.
1. 
    ![원본 리소스 그룹을 준비 하는 단추](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    준비 과정에서 리소스 이동 기는 리소스 그룹 설정을 사용 하 여 ARM (Azure Resource Manager) 템플릿을 생성 합니다. 리소스 그룹 내의 리소스는 영향을 받지 않습니다.
    
> [!NOTE]
>  리소스 그룹을 준비한 후에는 *시작 보류 중* 상태로 전환 됩니다. 최신 상태를 표시 하려면 새로 고칩니다.

![시작 보류 중 상태를 표시 하는 상태](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>원본 리소스 그룹 이동

다음과 같이 이동을 시작 합니다.

1. **여러 지역**에서 리소스 그룹을 선택 하 > **이동 시작**
2. ln **리소스 이동**을 선택 하 고 **이동 시작**을 선택 합니다. 리소스 그룹이 *진행 중인 시작* 상태로 이동 합니다.
3. 이동을 시작한 후 생성 된 ARM 템플릿에 따라 대상 리소스 그룹이 생성 됩니다. 원본 리소스 그룹이 *커밋 이동 보류 중* 상태로 이동 합니다.

![커밋 이동을 표시 하는 상태](./media/move-region-availability-zone/commit-move-pending.png)

이동 프로세스를 커밋하고 완료 하려면 다음을 수행 합니다.

1. **여러 지역**에서 리소스 그룹을 선택 하 > **커밋을 이동** 합니다.
2. ln **리소스 이동**을 선택 하 고 **커밋**을 선택 합니다.

> [!NOTE]
> 이동을 커밋한 후 원본 리소스 그룹은 *삭제 원본 보류 중* 상태입니다.

## <a name="modify-target-settings"></a>대상 설정 수정

원본 리소스를 이동 하지 않으려면 다음 중 하나를 수행할 수 있습니다.

- 원본 지역의 리소스와 동일한 이름 및 설정을 사용 하 여 대상 지역에 리소스를 만듭니다.
- 대상 지역에 해당 하는 새 리소스를 만듭니다. 지정한 설정을 제외 하 고 대상 리소스는 원본과 동일한 설정으로 만들어집니다.
- 대상 지역에서 기존 리소스를 사용 합니다.

다음과 같이 설정을 수정 합니다.

1. 설정을 수정 하려면 리소스에 대 한 **대상 구성** 열에서 항목을 선택 합니다.
2. **대상 구성** 페이지에서 사용 하려는 대상 설정을 지정 합니다.
    편집 중인 리소스에 대해서만 변경 내용이 적용 됩니다. 종속 리소스를 개별적으로 업데이트 해야 합니다.   
    
수정 하는 정확한 설정은 리소스 유형에 따라 달라 집니다. 대상 설정 편집에 [대해 자세히 알아보세요](modify-target-settings.md) .

## <a name="prepare-resources-to-move"></a>이동할 리소스 준비

원본 리소스 그룹을 이동 했으므로 다른 리소스를 이동할 준비를 할 수 있습니다.

1. **여러 지역**에서 준비 하려는 리소스를 선택 합니다. 

    ![다른 리소스 준비를 선택할 페이지](./media/move-region-availability-zone/prepare-other.png)

2. **준비**를 선택 합니다.

> [!NOTE]
> - 준비 프로세스 중에는 복제를 위해 Azure Site Recovery 모바일 에이전트가 Vm에 설치 됩니다.
> - VM 데이터는 대상 지역에 주기적으로 복제 됩니다. 원본 VM에는 영향을 주지 않습니다.
> - 리소스 이동은 다른 소스 리소스에 대 한 ARM 템플릿을 생성 합니다.
> - 리소스를 준비한 후에는 *시작 보류 중* 상태로 전환 됩니다.

![이동 보류 중 상태의 리소스를 표시 하는 페이지](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>이동 시작

리소스를 준비한 후에는 이제 이동을 시작할 수 있습니다.

1. **여러 지역**에서 상태가 *이동 보류 중*인 리소스를 선택 합니다. 그런 다음 **이동 시작**을 선택 합니다.
2. **리소스 이동**에서 **이동 시작**을 선택 합니다.

    ![이동 시작 단추를 선택 합니다.](./media/move-region-within-resource-group/initiate-move.png)

3. 알림 표시줄에서 이동 진행률을 추적 합니다.


> [!NOTE]
> - Vm의 경우 대상 지역에 복제본 Vm이 만들어집니다. 원본 VM이 종료 되 고 일부 가동 중지 시간 (일반적으로 분)이 발생 합니다.<br/>
> - 리소스 이동 기는 준비 된 ARM 템플릿을 사용 하 여 다른 리소스를 다시 만듭니다. 일반적으로 가동 중지 시간이 없습니다.<br/> 
> - 부하 분산 장치의 경우 NAT 규칙이 복사 되지 않습니다. 이동을 커밋한 후 대상 지역에 만듭니다.
> - 공용 IP 주소의 경우 DNS 이름 레이블이 복사 되지 않습니다. 이동을 커밋한 후에 레이블을 다시 만듭니다.
> - 리소스를 준비 하 고 나면 *커밋 이동 보류 중* 상태가 됩니다.


## <a name="discard-or-commit"></a>취소 또는 커밋?

처음 이동한 후에는 이동을 커밋하거나 삭제할지 여부를 결정할 수 있습니다. 

- **취소**: 테스트 하는 경우에는 이동을 취소할 수 있으며 실제로 원본 리소스를 이동 하지는 않습니다. 이동을 삭제 하면 리소스가 *시작 보류 중*상태로 돌아갑니다.
- **Commit**: commit이 대상 지역으로 이동 하는 작업을 완료 합니다. 커밋 후 원본 리소스는 *삭제 원본 보류*중 상태가 되며, 해당 리소스를 삭제할지 여부를 결정할 수 있습니다.


## <a name="discard-the-move"></a>이동 취소 

다음과 같이 이동을 취소할 수 있습니다.

1. **여러 지역**에서 상태 *커밋 이동이 보류 중인*리소스를 선택 하 고 **이동 취소**를 선택 합니다.
2. **삭제 이동**에서 **삭제**를 선택 합니다.
3. 알림 표시줄에서 이동 진행률을 추적 합니다.
4. 알림이 성공적으로 이동 되었다는 알림이 표시 되 면 **새로 고침**을 선택 합니다. 

> [!NOTE]
> Vm의 경우 리소스를 삭제 한 후에는 *시작 보류 중* 상태로 전환 됩니다.

## <a name="commit-the-move"></a>이동 커밋

이동 프로세스를 완료 하려면 이동을 커밋합니다. 


1. **여러 지역**에서 상태 *커밋 이동이 보류 중인*리소스를 선택 하 고 **이동 커밋**을 선택 합니다.
2. **커밋 리소스**에서 **커밋**을 선택 합니다.

    ![이동을 완료할 리소스를 커밋하는 페이지입니다.](./media/move-region-within-resource-group/commit-resources.png)

3. 알림 표시줄에서 커밋 진행률을 추적 합니다.

> [!NOTE]
> - 이동을 커밋한 후 Vm은 복제를 중지 합니다. 원본 VM은 커밋의 영향을 받지 않습니다.
> - Commit은 원본 네트워킹 리소스에 영향을 주지 않습니다.
> - 이동을 커밋한 후 리소스는 *삭제 원본 보류 중* 상태입니다.

## <a name="configure-settings-after-the-move"></a>이동 후 설정 구성

1. 공용 IP 주소에 대해 DNS 이름 레이블이 복사 되지 않으므로 이동이 완료 된 후 대상 리소스로 이동 하 여 레이블을 업데이트 합니다. 
2. 내부 부하 분산 장치의 경우 NAT 규칙이 복사 되지 않으므로 대상 지역에서 만든 리소스로 이동 하 여 NAT 규칙을 업데이트 합니다.
3. 모바일 서비스는 Vm에서 자동으로 제거 되지 않습니다. 수동으로 제거 하거나 서버를 다시 이동 하려는 경우에는 그대로 둡니다.
## <a name="delete-source-resources-after-commit"></a>커밋 후 소스 리소스 삭제

이동 후 필요에 따라 원본 영역에서 리소스를 삭제할 수 있습니다. 

1. **여러 지역**에서 삭제 하려는 각 원본 리소스의 이름을 선택 합니다.
2. 각 리소스에 대 한 속성 페이지에서 **삭제**를 선택 합니다.

## <a name="delete-additional-resources-created-for-move"></a>이동을 위해 만든 추가 리소스 삭제

이동 후에는 이동 컬렉션을 수동으로 삭제 하 고 생성 된 리소스를 Site Recovery 수 있습니다.

- 이동 컬렉션은 기본적으로 숨겨져 있습니다. 이를 확인 하려면 숨겨진 리소스를 설정 해야 합니다.
- 캐시 저장소에 삭제 해야 하는 잠금이 있으므로 삭제할 수 있습니다.

다음과 같이 삭제 합니다. 

1. 리소스 그룹의 리소스를 ```RegionMoveRG-<sourceregion>-<target-region>``` 원본 영역에서 찾습니다.
2. 이동 컬렉션의 모든 VM 및 기타 원본 리소스가 이동/삭제 되었는지 확인 합니다. 이렇게 하면 보류 중인 리소스를 사용 하지 않습니다.
2. 리소스를 삭제 합니다.

    - 이동 컬렉션 이름은 ```movecollection-<sourceregion>-<target-region>``` 입니다.
    - 캐시 저장소 계정 이름은입니다. ```resmovecache<guid>```
    - 자격 증명 모음 이름은 ```ResourceMove-<sourceregion>-<target-region>-GUID``` 입니다.

## <a name="next-steps"></a>다음 단계


이동 프로세스 [에 대해 알아봅니다](about-move-process.md) .
