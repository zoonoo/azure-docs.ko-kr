---
title: Synapse 작업 영역 기능을 사용하도록 설정
description: 이 문서에서는 사용자가 기존 전용 SQL 풀(이전의 SQL DW)에서 Synapse 작업 영역 기능을 사용하도록 설정하는 방법을 설명합니다.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/25/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 605f495fbc5913663499f5411abcaa531cea97ee
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312215"
---
# <a name="enabling-synapse-workspace-features-for-a-dedicated-sql-pool-formerly-sql-dw"></a>전용 SQL 풀(이전의 SQL DW)에 대해 Synapse 작업 영역 기능을 사용하도록 설정

모든 SQL 데이터 웨어하우스 사용자가 이제 Synapse Studio 및 작업 영역을 통해 기존 전용 SQL 풀(이전의 SQL DW) 인스턴스에 액세스하여 사용할 수 있습니다. 사용자는 자동화, 연결 또는 도구에 영향을 주지 않고 Synapse Studio 및 작업 영역을 사용할 수 있습니다. 이 문서에서는 기존 Azure Synapse Analytics 사용자가 기존 전용 SQL 풀(이전의 SQL DW)에 대해 Synapse 작업 영역 기능을 사용하도록 설정하는 방법을 설명합니다. 사용자는 이제 Synapse 작업 영역 및 Studio를 통해 사용 가능한 기능적으로 풍부해진 새로운 기능을 활용하여 기존 Analytics 솔루션을 확장할 수 있습니다.   

## <a name="prerequisites"></a>필수 구성 요소
데이터 웨어하우스에서 Synapse 작업 영역 기능을 사용하도록 설정하기 전에 다음 사항이 있는지 확인해야 합니다.
- SQL 논리 서버에서 호스트되는 SQL 리소스를 만들고 관리할 수 있는 권한
- 호스트 SQL Server에 대한 쓰기 권한 
- Azure Synapse 리소스를 만들 수 있는 권한
- 논리 서버에서 식별된 Azure Active Directory 관리자

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="enabling-synapse-workspace-features-for-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>기존 전용 SQL 풀(이전의 SQL DW)에 대해 Synapse 작업 영역 기능을 사용하도록 설정

Synapse 작업 영역 기능을 지원되는 지역의 기존 전용 SQL 풀(이전의 SQL DW)에서 사용하도록 설정할 수 있습니다. 이 기능은 Azure Portal을 통해서만 사용 가능합니다.

기존 데이터 웨어하우스에 대해 Synapse 작업 영역을 만들려면 다음 단계를 수행합니다.
1. 기존 전용 SQL 풀(이전의 SQL DW)을 선택하고 개요 페이지를 엽니다.
2. 위쪽 메뉴 모음이나 바로 아래에 있는 메시지에서 **새 Synapse 작업 영역** 을 선택합니다.
3. **새 Azure Synapse 작업 영역 만들기** 페이지에서 새 Synapse 작업 영역을 통해 사용할 수 있는 데이터 웨어하우스의 목록을 검토한 후, **계속** 을 선택하여 진행합니다.
4. 기본 페이지에서 기존 전용 SQL 풀(**프로젝트** 세부 정보 섹션)은 논리 서버에서 배포된 것과 동일한 **구독** 및 **리소스 그룹** 으로 미리 채워져 있어야 합니다. 그리고 프로비저닝 프로세스 중에 새 Synapse 작업 영역과 논리 서버 간의 연결을 만들 수 있도록 **작업 영역 세부 정보** 의 **작업 영역** 이름이 SQL 논리 서버와 동일한 이름 및 지역으로 미리 채워져 있어야 합니다. 작업 영역 및 Studio를 통해 전용 SQL 풀(이전의 SQL DW) 인스턴스에 계속 액세스하려면 이 연결을 프로비저닝한 후 보존해야 합니다.
5. Data Lake Storage Gen 2로 이동합니다.
6. **다음: 네트워킹** 을 선택하기 전에 **새로 만들기** 를 선택하거나 기존 **Data Lake Storage Gen2** 를 선택합니다.
7. **서버리스 인스턴스** 에 대한 **SQL 관리자 암호** 를 선택합니다. 이 암호를 변경해도 논리 서버 SQL 자격 증명은 업데이트되거나 변경되지 않습니다. 시스템에서 정의한 암호를 선호하는 경우 이러한 필드를 비워 둡니다. 이 암호는 새 작업 영역 내에서 언제든지 변경할 수 있습니다. 관리자 이름은 SQL Server에서 사용한 것과 동일해야 합니다.
8. 선호하는 **네트워킹 설정** 을 선택하고 **검토 + 만들기** 를 선택하여 작업 영역 프로비저닝을 시작합니다.
9. **리소스로 이동** 을 선택하여 새 작업 영역을 엽니다.

    > [!NOTE]
    > 논리 서버에서 호스트되는 모든 전용 SQL 풀(이전의 SQL DW) 인스턴스는 새 작업 영역을 통해 사용 가능합니다.

## <a name="post-provisioning-steps"></a>프로비저닝 후 단계
Synapse Studio를 통해 기존 전용 SQL 풀(이전의 SQL DW) 인스턴스에 액세스할 수 있으려면 다음 단계를 완료해야 합니다.
1. Synapse 작업 영역 개요 페이지에서 **연결된 서버** 를 선택합니다. **연결된 서버** 를 통해 데이터 웨어하우스를 호스트하는 연결된 SQL 논리 서버로 이동합니다. 필수 메뉴에서 **연결된 서버** 를 선택합니다.
2. **방화벽 및 가상 네트워크** 를 열고 클라이언트 IP 또는 미리 지정된 IP 범위에 논리 서버에 대한 액세스 권한이 있는지 확인합니다.
3. **Active Directory 관리자** 를 열고 논리 서버에 AAD 관리자가 설정되어 있는지 확인합니다.
4. 논리 서버에서 호스트되는 전용 SQL 풀(이전의 SQL DW) 인스턴스 중 하나를 선택합니다. 개요 페이지에서 **Synapse Studio 시작** 을 선택하거나 [Synapse Studio에 로그인](https://web.azuresynapse.net)으로 이동하여 작업 영역에 로그인합니다.

5. **데이터 허브** 를 열고 개체 탐색기에서 전용 SQL 풀을 확장하여 데이터 웨어하우스에 액세스하고 쿼리할 수 있는지 확인합니다.

    > [!NOTE] 
    > 연결된 작업 영역은 언제든지 삭제할 수 있습니다. 작업 영역을 삭제해도 연결된 전용 SQL 풀(이전의 SQL DW)은 삭제되지 않습니다. 삭제 작업이 완료되면 전용 SQL 풀(이전의 SQL DW)에서 작업 영역 기능을 다시 사용하도록 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Synapse 작업 영역 및 Studio](../get-started.md)를 시작합니다.
