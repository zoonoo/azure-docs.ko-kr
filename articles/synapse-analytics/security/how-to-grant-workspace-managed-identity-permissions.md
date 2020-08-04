---
title: Synapse 작업 영역에서 관리 id에 사용 권한 부여
description: Azure Synapse 작업 영역에서 관리 되는 id에 대 한 사용 권한을 구성 하는 방법을 설명 하는 문서입니다.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5c07943d517f893e67eed5103660a953b508cc87
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535045"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>작업 영역 관리 id에 사용 권한 부여 (미리 보기)

이 문서에서는 Azure synapse 작업 영역에서 관리 되는 id에 대 한 사용 권한을 부여 하는 방법을 설명 합니다. 그런 다음 작업 영역에서 SQL 풀에 대 한 액세스를 허용 하 고 Azure Portal를 통해 저장소 계정을 ADLS Gen2 합니다.

>[!NOTE]
>이 작업 영역 관리 ID는 이 문서의 나머지 부분에서 관리 ID라고 합니다.

## <a name="grant-managed-identity-permissions-to-the-sql-pool"></a>SQL 풀에 관리 id 권한 부여

관리 id는 작업 영역의 SQL 풀에 권한을 부여 합니다. 권한이 부여 되 면 SQL 풀 관련 작업을 수행 하는 파이프라인을 오케스트레이션 할 수 있습니다. Azure Portal를 사용 하 여 Azure Synapse 작업 영역을 만들 때 SQL 풀에 관리 되는 id 제어 권한을 부여할 수 있습니다.

Azure Synapse 작업 영역을 만들 때 **보안 + 네트워킹** 을 선택 합니다. 그런 다음 **SQL 풀에서 작업 영역 관리 id에 제어 권한 부여를**선택 합니다.

![SQL 풀에 대 한 CONTROL 권한](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>ADLS Gen2 저장소 계정에 관리 id 사용 권한 부여

Azure Synapse 작업 영역을 만들려면 ADLS Gen2 storage 계정이 필요 합니다. Azure Synapse 작업 영역에서 Spark 풀을 성공적으로 시작 하려면 Azure Synapse 관리 id에이 저장소 계정에 대 한 *저장소 Blob 데이터 참가자* 역할이 있어야 합니다. Azure Synapse의 파이프라인 오케스트레이션은이 역할의 이점도 있습니다.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>작업 영역을 만드는 동안 관리 id에 권한 부여

Azure Synapse는 Azure Portal을 사용 하 여 Azure Synapse 작업 영역을 만든 후 관리 되는 id에 저장소 Blob 데이터 참가자 역할을 부여 하려고 시도 합니다. **기본 사항** 탭에서 ADLS Gen2 저장소 계정 세부 정보를 제공 합니다.

![작업 영역 생성 흐름의 기본 사항 탭](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

**계정 이름** 및 **파일 시스템 이름**에서 ADLS Gen2 저장소 계정 및 파일 시스템을 선택 합니다.

![ADLS Gen2 저장소 계정 정보 제공](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

작업 영역 작성자도 ADLS Gen2 저장소 계정의 **소유자** 인 경우 Azure Synapse는 *저장소 Blob 데이터 참가자* 역할을 관리 되는 id에 할당 합니다. 입력 한 저장소 계정 세부 정보 아래에 다음 메시지가 표시 됩니다.

![저장소 Blob 데이터 참여자 할당 성공](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

작업 영역 작성자가 ADLS Gen2 저장소 계정의 소유자가 아닌 경우, Azure Synapse는 관리 되는 id에 *저장소 Blob 데이터 참가자* 역할을 할당 하지 않습니다. 저장소 계정 세부 정보 아래에 표시 되는 메시지는 작업 영역 작성자에 게 *저장소 Blob 데이터 참가자* 역할에 관리 되는 id에 대 한 권한을 부여할 수 있는 권한이 없음을 알립니다.

![저장소 Blob 데이터 참가자 할당 실패](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

메시지 상태에서 *저장소 Blob 데이터 참가자* 가 관리 id에 할당 되지 않으면 Spark 풀을 만들 수 없습니다.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>작업 영역을 만든 후 관리 되는 id에 권한 부여

작업 영역을 만드는 동안 *저장소 Blob 데이터 참가자* 를 관리 id에 할당 하지 않으면 ADLS Gen2 저장소 계정의 **소유자** 가 해당 역할을 id에 수동으로 할당 합니다. 다음 단계는 수동 할당을 수행 하는 데 도움이 됩니다.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>1 단계:에서 ADLS Gen2 저장소 계정으로 이동 Azure Portal

Azure Portal에서 ADLS Gen2 storage 계정을 열고 왼쪽 탐색 영역에서 **개요** 를 선택 합니다. 컨테이너 또는 filesystem 수준 에서만 *저장소 Blob 데이터 참가자* 역할을 할당 해야 합니다. **컨테이너**를 선택합니다.  
![ADLS Gen2 storage 계정 개요](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>2 단계: 컨테이너 선택

관리 id에는 작업 영역을 만들 때 제공 된 컨테이너 (파일 시스템)에 대 한 데이터 액세스 권한이 있어야 합니다. Azure Portal에서이 컨테이너 또는 파일 시스템을 찾을 수 있습니다. Azure Portal에서 Azure Synapse 작업 영역을 열고 왼쪽 탐색 영역에서 **개요** 탭을 선택 합니다.
![저장소 계정 컨테이너 ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


*저장소 Blob 데이터 참가자* 역할을 관리 되는 id에 부여 하려면 동일한 컨테이너 또는 파일 시스템을 선택 합니다.
![ADLS Gen2 storage 계정 컨테이너 선택](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>3 단계: 액세스 제어로 이동

**Access Control(IAM)** 을 선택합니다.

![액세스 제어 (IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>4 단계: 새 역할 할당 추가

**+추가**를 선택합니다.

![새 역할 할당 추가](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-azure-role"></a>5 단계: Azure 역할 선택

**저장소 Blob 데이터 참가자** 역할을 선택 합니다.

![Azure 역할 선택](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>6 단계: Azure AD 보안 주체 선택

**액세스 할당** 드롭다운에서 **Azure AD 사용자, 그룹 또는 서비스 사용자** 를 선택 합니다.

![AAD 보안 주체 선택](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>7 단계: 관리 되는 id 검색

관리 id의 이름은 작업 영역 이름 이기도 합니다. **Select**에서 Azure Synapse 작업 영역 이름을 입력 하 여 관리 id를 검색 합니다. 표시 되는 관리 id가 표시 되어야 합니다.

![관리 id 찾기](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>8 단계: 관리 id 선택

**선택한 멤버**에 대 한 관리 되는 id를 선택 합니다. **저장** 을 선택 하 여 역할 할당을 추가 합니다.

![관리 id 선택](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>9 단계: 저장소 Blob 데이터 참가자 역할이 관리 id에 할당 되었는지 확인

**Access Control (IAM)** 을 선택 하 고 **역할 할당**을 선택 합니다.

![역할 할당 확인](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

저장소 blob *데이터* 참가자 역할이 할당 된 **저장소 blob 데이터 참가자** 섹션에 나열 된 관리 id가 표시 되어야 합니다. 
![ADLS Gen2 storage 계정 컨테이너 선택](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>다음 단계

[작업 영역 관리 id](./synapse-workspace-managed-identity.md) 에 대 한 자세한 정보
