---
title: Synapse 작업 영역에서 관리 ID에 사용 권한 부여
description: Azure Synapse 작업 영역에서 관리 ID에 대한 사용 권한을 구성하는 방법을 설명하는 문서입니다.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 46fe27205a2c30d6cb2319bf620c6fe1ee34c31e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96459008"
---
# <a name="grant-permissions-to-workspace-managed-identity"></a>작업 영역 관리 ID에 권한 부여

이 문서에서는 Azure Synapse 작업 영역에서 관리 ID에 대한 사용 권한을 부여3하는 방법을 설명합니다. 그러면 권한은 Azure Portal을 통해 작업 영역 및 ADLS Gen2 스토리지 계정의 전용 SQL 풀에 대한 액세스를 허용합니다.

>[!NOTE]
>이 작업 영역 관리 ID는 이 문서의 나머지 부분에서 관리 ID라고 합니다.

## <a name="grant-managed-identity-permissions-to-the-dedicated-sql-pool"></a>전용 SQL 풀에 관리 ID 권한 부여

관리 ID는 작업 영역의 전용 SQL 풀에 사용 권한을 부여합니다. 권한이 부여된 경우 전용 SQL 풀 관련 작업을 수행하는 파이프라인을 오케스트레이션할 수 있습니다. Azure Portal을 사용하여 Azure Synapse 작업 영역을 만들 때 전용 SQL 풀에 관리 ID CONTROL 권한을 부여할 수 있습니다.

Azure Synapse 작업 영역을 만들 때 **보안** 을 선택합니다. 그런 다음, **파이프라인(작업 영역 시스템 할당 ID로 실행)을 선택하여 SQL 풀에 액세스** 합니다.

![전용 SQL 풀에 대한 CONTROL 권한](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>ADLS Gen2 스토리지 계정에 관리 ID 사용 권한 부여

Azure Synapse 작업 영역을 만들려면 ADLS Gen2 스토리지 계정이 필요합니다. Azure Synapse 작업 영역에서 Spark 풀을 성공적으로 시작하려면 Azure Synapse 관리 ID에 이 스토리지 계정에 대한 *Storage Blob 데이터 참가자* 역할이 있어야 합니다. Azure Synapse의 파이프라인 오케스트레이션도 이 역할의 이점을 얻습니다.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>작업 영역을 만드는 동안 관리 ID에 권한 부여

Azure Synapse는 Azure Portal을 사용하여 Azure Synapse 작업 영역을 만든 후 관리 ID에 Storage Blob 데이터 참가자 역할을 부여하려고 합니다. **기본 사항** 탭에 ADLS Gen2 스토리지 계정 세부 정보를 제공합니다.

![작업 영역 생성 흐름의 기본 사항 탭](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

**계정 이름** 및 **파일 시스템 이름** 에서 ADLS Gen2 스토리지 계정 및 파일 시스템을 선택합니다.

![ADLS Gen2 스토리지 계정 세부 정보 제공](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

작업 영역 작성자도 ADLS Gen2 스토리지 계정의 **소유자** 인 경우 Azure Synapse는  *데이터 참가자* 역할을 관리 ID에 할당합니다. 입력한 스토리지 계정 세부 정보 아래에 다음 메시지가 표시됩니다.

![Storage Blob 데이터 참가자 할당 성공](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

작업 영역 작성자가 ADLS Gen2 스토리지 계정의 소유자가 아닌 경우, Azure Synapse는 관리 ID에 *Storage Blob 데이터 참가자* 역할을 할당하지 않습니다. 스토리지 계정 세부 정보 아래에 표시되는 메시지는 작업 영역 작성자에게 관리 ID에 *데이터 참가자* 역할을 부여할 수 있는 권한이 없음을 알립니다.

![Storage Blob 데이터 참가자 할당 실패](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

메시지에 나온 것과 같이 *Storage Blob 데이터 참가자* 가 관리 ID에 할당되지 않으면 Spark 풀을 만들 수 없습니다.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>작업 영역을 만든 후 관리 ID에 권한 부여

작업 영역을 만드는 동안  *데이터 참가자* 를 관리 ID에 할당하지 않으면 ADLS Gen2 스토리지 계정의 **소유자** 가 해당 역할을 ID에 수동으로 할당합니다. 다음 단계는 수동 할당을 수행하는 데 도움이 됩니다.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>1단계: Azure Portal에서 ADLS Gen2 스토리지 계정으로 이동

Azure Portal에서 ADLS Gen2 스토리지 계정을 열고 왼쪽 탐색 영역에서 **개요** 를 선택합니다. 컨테이너 또는 파일 시스템 수준에서 *Storage Blob 데이터 참가자* 역할만 할당하면 됩니다. **컨테이너** 를 선택합니다.  
![ADLS Gen2 스토리지 계정 개요](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>2단계: 컨테이너 선택

관리 ID에는 작업 영역이 생성되었을 때 제공된 컨테이너(파일 시스템)에 대한 데이터 액세스 권한이 있어야 합니다. Azure Portal에서 이 컨테이너 또는 파일 시스템을 찾을 수 있습니다. Azure Portal에서 Azure Synapse 작업 영역을 열고 왼쪽 탐색 메뉴에서 **개요** 탭을 선택합니다.
![ADLS Gen2 스토리지 계정 컨테이너](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


*Storage Blob 데이터 참가자* 역할을 관리 ID에 부여하려면 동일한 컨테이너 또는 파일 시스템을 선택합니다.
![선택해야 하는 컨테이너 또는 파일 시스템을 보여주는 스크린샷입니다.](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>3단계: 액세스 제어로 이동

**Access Control(IAM)** 을 선택합니다.

![IAM(액세스 제어)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>4단계: 새 역할 할당 추가

**+추가** 를 선택합니다.

![새 역할 할당 추가](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-azure-role"></a>5단계: Azure 역할 선택

**Storage Blob 데이터 참가자** 역할을 선택합니다.

![Azure 역할 선택](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>6단계: Azure AD 보안 주체 선택

**액세스 할당 대상** 드롭다운에서 **Azure AD 사용자, 그룹 또는 서비스 사용자** 를 선택합니다.

![AAD 보안 주체 선택](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>7단계: 관리 ID 검색

관리 ID의 이름은 작업 영역 이름이기도 합니다. **선택** 에 Azure Synapse 작업 영역 이름을 입력하여 관리 ID를 검색합니다. 그러면 관리 ID가 나열됩니다.

![관리 ID 찾기](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>8단계: 관리 ID 선택

**선택한 멤버** 에 대한 관리 ID를 선택합니다. **저장** 을 선택하여 역할 할당을 추가합니다.

![관리 ID 선택](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>9단계: Storage Blob 데이터 참가자 역할이 관리 ID에 할당되었는지 확인

**IAM(액세스 제어)** , **역할 할당** 을 차례로 선택합니다.

![역할 할당 확인](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

**Storage Blob 데이터 참가자** 섹션 아래에 *Storage Blob 데이터 참가자* 역할이 할당된 관리 ID가 나열됩니다. 
![ADLS Gen2 스토리지 계정 컨테이너 선택](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>다음 단계

[작업 영역 관리 ID](./synapse-workspace-managed-identity.md)에 대한 자세한 정보
