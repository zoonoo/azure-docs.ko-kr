---
title: Azure Synapse 작업 영역에서 관리되는 ID에 대한 권한을 부여하는 방법
description: Azure Synapse 작업 영역에서 관리되는 ID에 대한 사용 권한을 구성하는 방법을 설명하는 문서입니다.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9f519022fffe98c565c3b2d30f6578b9ebb70c57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428018"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>작업 영역 관리 ID에 대한 사용 권한 부여(미리 보기)

이 문서에서는 Azure 시냅스 작업 영역에서 관리되는 ID에 대한 권한을 부여하는 방법을 설명합니다. 또한 권한은 Azure 포털을 통해 작업 영역의 SQL 풀 및 ADLS gen2 저장소 계정에 대한 액세스를 허용합니다.

>[!NOTE]
>이 작업 영역 관리 ID는 이 문서의 나머지 부분을 통해 관리되는 ID라고 합니다.

## <a name="grant-the-managed-identity--permissions-to-the-sql-pool"></a>SQL 풀에 관리되는 ID 사용 권한을 부여합니다.

관리되는 ID는 작업 영역의 SQL 풀에 권한을 부여합니다. 사용 권한이 부여된 경우 SQL 풀 관련 활동을 수행하는 파이프라인을 오케스트레이션할 수 있습니다. Azure 포털을 사용하여 Azure Synapse 작업 영역을 만들 때 SQL 풀에서 관리되는 ID CONTROL 권한을 부여할 수 있습니다.

Azure Synapse 작업 영역을 만들 때 **보안 + 네트워킹을** 선택합니다. 그런 다음 **SQL 풀에서 작업 영역의 관리ID에 대한 권한 부여 제어를 선택합니다.**

![SQL 풀에 대한 제어 권한](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>ADLS gen2 저장소 계정에 관리되는 ID 권한을 부여합니다.

Azure Synapse 작업 영역을 만들려면 ADLS gen2 저장소 계정이 필요합니다. Azure Synapse 작업 영역에서 Spark 풀을 성공적으로 시작하려면 Azure Synapse 관리 ID에 이 저장소 계정에 *저장소 Blob 데이터 기여자* 역할이 필요합니다. Azure Synapse의 파이프라인 오케스트레이션도 이 역할의 이점을 누릴 수 있습니다.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>작업 영역을 만드는 동안 관리되는 ID에 대한 권한 부여

Azure Synapse는 Azure 포털을 사용하여 Azure Synapse 작업 영역을 만든 후 관리되는 ID에 저장소 Blob 데이터 기여자 역할을 부여하려고 시도합니다. **기본** 탭에서 ADLS gen2 저장소 계정 세부 정보를 제공합니다.

![작업 영역 생성 흐름의 기본 탭](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

**계정 이름** 및 파일 **시스템 이름에서**ADLS gen2 저장소 계정 및 파일 시스템을 선택합니다.

![ADLS gen2 스토리지 계정 세부 정보 제공](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

작업 영역 작성자가 ADLS gen2 저장소 계정의 **소유자이기도** 한 경우 Azure Synapse는 *저장소 Blob 데이터 참여자* 역할을 관리되는 ID에 할당합니다. 입력한 저장소 계정 세부정보 아래에 다음 메시지가 표시됩니다.

![성공적인 스토리지 Blob 데이터 기여자 할당](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

작업 영역 작성자가 ADLS gen2 저장소 계정의 소유자가 아닌 경우 Azure Synapse는 *저장소 Blob 데이터 참여자* 역할을 관리되는 ID에 할당하지 않습니다. 저장소 계정 세부 정보 아래에 나타나는 메시지는 작업 영역 작성자가 관리되는 ID에 *저장소 Blob 데이터 기여자* 역할을 부여할 수 있는 충분한 권한이 없다는 것을 표시합니다.

![실패한 저장소 Blob 데이터 기여자 할당](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

메시지에서 설명한 대로 *저장소 Blob 데이터 참여자가* 관리되는 ID에 할당되지 않으면 Spark 풀을 만들 수 없습니다.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>작업 영역을 만든 후 관리되는 ID에 대한 사용 권한 부여

작업 영역을 만드는 동안 *저장소 Blob 데이터 기여자를* 관리되는 ID에 할당하지 않으면 ADLS gen2 저장소 계정의 **소유자가** 해당 역할을 ID에 수동으로 할당합니다. 다음 단계는 수동 과제를 수행하는 데 도움이 될 것이다.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>1단계: Azure 포털의 ADLS gen2 저장소 계정으로 이동

Azure 포털에서 ADLS gen2 저장소 계정을 열고 왼쪽 탐색에서 **개요를** 선택합니다. 컨테이너 또는 파일 시스템 수준에서 *저장소 Blob 데이터 기여자* 역할만 할당하면 됩니다. **컨테이너**를 선택합니다.  
![ADLS gen2 스토리지 계정 개요](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>2단계: 컨테이너 선택

관리되는 ID는 작업 영역을 만들 때 제공된 컨테이너(파일 시스템)에 대한 데이터 액세스 권한을 가져야 합니다. Azure 포털에서 이 컨테이너 또는 파일 시스템을 찾을 수 있습니다. Azure 포털에서 Azure Synapse 작업 영역을 열고 왼쪽 탐색에서 **개요** 탭을 선택합니다.
![ADLS gen2 스토리지 계정 컨테이너](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


동일한 컨테이너 또는 파일 시스템을 선택하여 *저장소 Blob 데이터 기여자* 역할을 관리되는 ID에 부여합니다.
![ADLS gen2 스토리지 계정 컨테이너 선택](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>3단계: 액세스 제어로 이동

**액세스 제어(IAM)를**선택합니다.

![액세스 제어(IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>4단계: 새 역할 할당 추가

**+ 추가**를 선택합니다.

![새 역할 할당 추가](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-rbac-role"></a>5단계: RBAC 역할 선택

저장소 **Blob 데이터 기여자 역할을 선택합니다.**

![RBAC 역할 선택](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>6단계: Azure AD 보안 주체 선택

드롭다운할 액세스 할당에서 **Azure AD 사용자, 그룹 또는 서비스 주체를** **선택합니다.**

![AAD 보안 주체 선택](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>7단계: 관리되는 ID 검색

관리되는 ID의 이름은 작업 영역 이름이기도 합니다. **선택**에서 Azure Synapse 작업 영역 이름을 입력하여 관리되는 ID를 검색합니다. 관리되는 ID가 나열되어 있어야 합니다.

![관리되는 ID 찾기](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>8단계: 관리되는 ID 선택

선택한 멤버에 대한 관리되는 ID를 **선택합니다.** 역할 할당을 추가하려면 **저장을** 선택합니다.

![관리되는 ID 선택](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>9단계: 저장소 Blob 데이터 기여자 역할이 관리되는 ID에 할당되었는지 확인

**IAM(액세스 제어)을** 선택한 다음 **역할 할당을 선택합니다.**

![역할 할당 확인](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

**저장소 Blob 데이터 기여자** 섹션에 할당된 *저장소 Blob 데이터 기여자* 역할이 나열된 관리 ID가 표시됩니다. 
![ADLS gen2 스토리지 계정 컨테이너 선택](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>다음 단계

[작업 영역 관리 ID에](./synapse-workspace-managed-identity.md) 대해 자세히 알아보기
