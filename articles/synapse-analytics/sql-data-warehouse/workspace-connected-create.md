---
title: Synapse 작업 영역 기능 사용
description: 이 문서에서는 사용자가 기존 전용 SQL 풀 (이전의 SQL DW)에서 Synapse 작업 영역 기능을 사용 하도록 설정 하는 방법을 설명 합니다.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/25/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 769ca4cf0ecbdba5fd80d977eb69c8a4f58df55f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98071190"
---
# <a name="enabling-synapse-workspace-features-for-a-dedicated-sql-pool-formerly-sql-dw"></a>전용 SQL 풀에 대해 Synapse 작업 영역 기능 사용 (이전의 SQL DW)

모든 SQL data warehouse 사용자는 이제 Synapse Studio 및 작업 영역을 통해 기존 전용 SQL 풀 (이전의 SQL DW) 인스턴스에 액세스 하 여 사용할 수 있습니다. 사용자는 자동화, 연결 또는 도구에 영향을 주지 않고 Synapse Studio 및 작업 영역을 사용할 수 있습니다. 이 문서에서는 기존 Azure Synapse Analytics 사용자가 기존 전용 SQL 풀 (이전의 SQL DW)에 대해 Synapse 작업 영역 기능을 사용 하도록 설정 하는 방법을 설명 합니다. 사용자는 이제 Synapse 작업 영역 및 Studio를 통해 사용할 수 있는 새로운 기능의 새로운 기능을 활용 하 여 기존 분석 솔루션을 확장할 수 있습니다.   

## <a name="prerequisites"></a>필수 구성 요소
데이터 웨어하우스에서 Synapse 작업 영역 기능을 사용 하도록 설정 하기 전에 다음이 있는지 확인 해야 합니다.
- SQL 논리 서버에서 호스트 되는 SQL 리소스를 만들고 관리할 수 있는 권한입니다.
- Azure Synapse 리소스를 만들 수 있는 권한.
- 논리 서버에서 식별 된 Azure Active Directory 관리자

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="enabling-synapse-workspace-features-for-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>기존 전용 SQL 풀에 대해 Synapse 작업 영역 기능 사용 (이전의 SQL DW)

Synapse 작업 영역 기능은 지원 되는 지역에서 기존 전용 SQL 풀 (이전의 SQL DW)에 대해 사용 하도록 설정할 수 있습니다. 이 기능은 Azure Portal 통해서만 사용할 수 있습니다.

기존 데이터 웨어하우스의 Synapse 작업 영역을 만들려면 다음 단계를 수행 합니다.
1. 기존 전용 SQL 풀 (이전의 SQL DW)을 선택 하 고 개요 페이지를 엽니다.
2. 위쪽 메뉴 모음에서 **새 Synapse 작업 영역** 을 선택 하거나 바로 아래에 있는 메시지를 선택 합니다.
3. 새 **Azure Synapse 작업 영역 만들기** 페이지에서 새 Synapse 작업 영역을 통해 사용할 수 있는 데이터 웨어하우스의 목록을 검토 한 후 **계속 하려면 계속** 을 선택 합니다.
4. 기본 사항 페이지에서 기존 전용 SQL 풀 ( **프로젝트** 세부 정보 섹션은 논리적 서버에 배포 된 것과 동일한 **구독** 및 **리소스 그룹** 으로 미리 채워져 있어야 합니다. 그리고 **작업 영역 세부 정보** 에서 **작업 영역** 이름은 SQL 논리 서버의 이름과 지역이 미리 채워지므로 프로 비전 프로세스 중에 새 Synapse 작업 영역과 논리 서버를 만들 수 있습니다. 작업 영역 및 스튜디오를 통해 전용 SQL 풀 (이전의 SQL DW) 인스턴스에 계속 액세스 하려면이 연결을 프로 비전 한 후 보존 해야 합니다.
5. Data Lake Storage Gen 2로 이동합니다.
6. **다음: 네트워킹** 을 선택 하기 전에 **새로 만들기** 를 선택 하거나 기존 **Data Lake Storage Gen2** 선택 합니다.
7. 서버를 사용 하지 않는 **인스턴스의** **SQL 관리자 암호** 를 선택 합니다. 이 암호를 변경 해도 논리 서버 SQL 자격 증명은 업데이트 되거나 변경 되지 않습니다. 시스템에서 정의한 암호를 선호 하는 경우에는 이러한 필드를 비워 둡니다. 이 암호는 새 작업 영역 내에서 언제 든 지 변경할 수 있습니다. 관리자 이름은 SQL Server에서 사용 해야 합니다.
8. 기본 설정 **네트워킹 설정을** 선택 하 고 **검토 + 만들기** 를 선택 하 여 작업 영역 프로 비전을 시작 합니다.
9. **리소스 이동** 을 선택 하 여 새 작업 영역을 엽니다.

    > [!NOTE]
    > 논리 서버에서 호스트 되는 모든 전용 SQL 풀 (이전의 SQL DW) 인스턴스는 새 작업 영역을 통해 사용할 수 있습니다.

## <a name="post-provisioning-steps"></a>사후 프로 비전 단계
Synapse Studio를 통해 기존 전용 SQL 풀 (이전의 SQL DW) 인스턴스에 액세스할 수 있도록 하려면 다음 단계를 완료 해야 합니다.
1. Synapse 작업 영역 개요 페이지에서 **연결 된 서버** 를 선택 합니다. **연결 된 서버** 는 데이터 웨어하우스를 호스트 하는 연결 된 SQL 논리 서버로 이동 합니다. 필수 메뉴에서 **연결 된 서버** 를 선택 합니다.
2. **방화벽 및 가상 네트워크** 를 열고 클라이언트 ip 또는 미리 지정 된 ip 범위에 논리 서버에 대 한 액세스 권한이 있는지 확인 합니다.
3. **Active Directory admin** 을 열고 논리 서버에 AAD 관리자가 설정 되어 있는지 확인 합니다.
4. 논리 서버에서 호스트 되는 전용 SQL 풀 (이전의 SQL DW) 인스턴스 중 하나를 선택 합니다. 개요 페이지에서 **Synapse Studio 시작** 을 선택 하거나 [Synapse studio에 로그인](https://web.azuresynapse.net) 하 여 작업 영역에 로그인 합니다.

5. 데이터 **허브** 를 열고 개체 탐색기에서 전용 SQL 풀을 확장 하 여 액세스 하 고 데이터 웨어하우스를 쿼리할 수 있는지 확인 합니다.

    > [!NOTE] 
    > 언제 든 지 연결 된 작업 영역을 삭제할 수 있습니다. 작업 영역을 삭제 해도 연결 된 전용 SQL 풀 (이전의 SQL DW)은 삭제 되지 않습니다. 삭제 작업이 완료 되 면 전용 SQL 풀 (이전의 SQL DW)에서 작업 영역 기능을 다시 사용 하도록 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Synapse Workspace 및 Studio](../get-started.md)시작
