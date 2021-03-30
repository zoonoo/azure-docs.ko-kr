---
title: '빠른 시작: 시작 - Synapse 작업 영역 만들기'
description: 이 자습서에서는 Synapse 작업 영역, 전용 SQL 풀 및 서버리스 Apache Spark 풀을 만드는 방법에 대해 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: ee8a192bcfe20c77e47ee9c89dfc3286a979e358
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722296"
---
# <a name="creating-a-synapse-workspace"></a>Synapse 작업 영역 만들기

이 자습서에서는 Synapse 작업 영역, 전용 SQL 풀 및 서버리스 Apache Spark 풀을 만드는 방법에 대해 알아봅니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 단계를 완료하려면 **소유자** 역할이 할당된 리소스 그룹에 대한 액세스 권한이 있어야 합니다. 이 리소스 그룹에 Synapse 작업 영역을 만듭니다.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Azure Portal에서 Synapse 작업 영역 만들기

### <a name="start-the-process"></a>프로세스 시작
1. [Azure Portal](https://portal.azure.com)을 열고 검색 창에서 enter 키를 누르지 않고 **Synapse** 를 입력합니다.
1. **서비스** 아래의 검색 결과에서 **Azure Synapse Analytics** 를 선택합니다.
1. **추가** 를 선택하여 작업 영역을 만듭니다.

## <a name="basics-tab--project-details"></a>기본 사항 탭 > 프로젝트 세부 정보
1. **기본 사항** 탭의 **프로젝트 세부 정보** 아래에서 다음 필드를 입력합니다.
      1. **구독** - 구독을 선택합니다.
      2. **리소스 그룹** - 리소스 그룹을 사용합니다.
      3. **리소스 그룹** - 비워 둡니다.


## <a name="basics-tab--workspace-details"></a>기본 사항 탭 > 작업 영역 세부 정보
1. **기본 사항** 탭의 **작업 영역 정보** 에서 다음 필드를 입력합니다.
      1. **작업 영역 이름** - 전역적으로 고유한 이름을 선택합니다. 이 자습서에서는 **myworkspace** 를 사용합니다.
      1. **지역** - 지역을 선택합니다.
      1. **Data Lake Storage Gen 2 선택**
        1. **구독 시작** 의 단추를 클릭합니다.
        1. **계정 이름** 별로 **새로 만들기** 를 클릭하고 새 스토리지 계정의 이름을 **contosolake** 로 지정하고, 이 이름은 고유해야 합니다.
        1. **파일 시스템 이름** 별로 **새로 만들기** 를 클릭하고 **사용자** 이름을 지정합니다. 이렇게 하면 **users** 라는 스토리지 컨테이너가 생성됩니다. 작업 영역에서는 이 스토리지 계정을 Spark 테이블 및 Spark 애플리케이션 로그에 대한 "기본" 스토리지 계정으로 사용합니다.
        1. "Data Lake Storage Gen2 계정에 Storage Blob 데이터 기여자 역할 할당" 상자를 선택합니다. 

### <a name="completing-the-process"></a>프로세스 완료
5. **검토 + 만들기** > **만들기** 를 차례로 선택합니다. 몇 분 안에 작업 영역이 준비됩니다.

> [!NOTE]
> 기존 전용 SQL 풀(이전의 SQL DW)에서 작업 영역 기능을 사용하도록 설정하려면 [전용 SQL 풀(이전의 SQL DW)에 작업 영역을 사용하도록 설정하는 방법](./sql-data-warehouse/workspace-connected-create.md)을 참조하세요.


## <a name="open-synapse-studio"></a>Synapse Studio 열기

Azure Synapse 작업 영역이 만들어지면 다음 두 가지 방법으로 Synapse Studio를 열 수 있습니다.

* [Azure Portal](https://portal.azure.com)에서 Synapse 작업 영역을 열고, Synapse 작업 영역의 **개요** 섹션에 있는 Synapse Studio 열기 상자에서 **열기** 를 선택합니다.
* `https://web.azuresynapse.net`(으)로 이동하여 작업 영역에 로그인합니다.











## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [서버리스 SQL 풀을 사용하여 분석](get-started-analyze-sql-on-demand.md)
