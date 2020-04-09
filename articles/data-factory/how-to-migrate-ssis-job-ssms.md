---
title: 온-프레미스 SQL 서버 통합 서비스(SSIS) 작업을 Azure 데이터 팩터리로 마이그레이션합니다.
description: 이 문서에서는 SQL Server 관리 Studio를 사용하여 SSIS(SQL Server 통합 서비스) 작업을 Azure Data Factory 파이프라인/활동/트리거로 마이그레이션하는 방법을 설명합니다.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: c65fbd9bbd83db9c7c8ec0e9041d08372243b72f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887646"
---
# <a name="migrate-on-premises-ssis-jobs-to-azure-data-factory"></a>온-프레미스 SSIS 작업을 Azure 데이터 팩터리로 마이그레이션

[온-프레미스 SQL Server 통합 서비스(SSIS) 워크로드를 ADF의 SSIS로 마이그레이션할](scenario-ssis-migration-overview.md)때 SSIS 패키지를 마이그레이션한 후 SQL Server 통합 서비스 패키지의 작업 단계 유형을 사용하여 SQL Server Server 에이전트 작업의 일괄 마이그레이션을 ADF(ADF) 파이프라인/활동/일정 트리거를 SQL Server 관리 스튜디오(SSMS) **SSIS 작업 마이그레이션 마법사를**통해 수행할 수 있습니다.

일반적으로 적용 가능한 작업 단계 유형이 있는 선택된 SQL 에이전트 작업의 경우 **SSIS 작업 마이그레이션 마법사는 다음을** 수행할 수 있습니다.

- 패키지가 마이그레이션되는 위치로 온-프레미스 SSIS 패키지 위치를 매핑하며, ADF의 SSIS에서 액세스할 수 있습니다.
    > [!NOTE]
    > 파일 시스템의 패키지 위치는 지원됩니다.
- 적용 가능한 작업 단계를 사용하여 해당 작업을 다음과 같이 해당 ADF 리소스로 마이그레이션합니다.

|SQL 에이전트 작업 개체  |ADF 리소스  |메모|
|---------|---------|---------|
|SQL 에이전트 작업|pipeline     |>작업 이름에 *대해 \< *파이프라인 이름이 생성됩니다. <br> <br> 기본 제공 에이전트 작업은 적용되지 않습니다. <li> SSIS 서버 유지 관리 작업 <li> syspolicy_purge_history <li> collection_set_* <li> mdw_purge_data_* <li> sysutility_*|
|SSIS 작업 단계|SSIS 패키지 활동 실행|<li> 활동의 이름은> \<단계 이름입니다. <li> 작업 단계에 사용되는 프록시 계정은 이 활동의 Windows 인증으로 마이그레이션됩니다. <li> 작업 단계에 정의된 *32비트 런타임 사용을* 제외한 *실행 옵션은* 마이그레이션시 무시됩니다. <li> 작업 단계에 정의된 *확인은* 마이그레이션시 무시됩니다.|
|schedule      |일정 트리거        |일정 트리거의 이름은 *>\<일정 이름에 대해 생성됩니다. * <br> <br> SQL Agent 작업 일정의 아래 옵션은 마이그레이션에서 무시됩니다. <li> 두 번째 수준 간격입니다. <li> *SQL Server 에이전트가 시작될 때 자동으로 시작* <li> *CPU가 유휴 상태로 될 때마다 시작* <li> *평일* 및 *주말*<time zone> <br> 다음은 SQL 에이전트 작업 일정이 ADF 일정 트리거로 마이그레이션된 후의 차이점입니다. <li> ADF 일정 트리거 후속 실행은 선행 트리거 실행의 실행 상태와 독립적입니다. <li> ADF 일정 트리거 되풀이 구성은 SQL 에이전트 작업의 일일 빈도와 다릅니다.|

- 로컬 출력 폴더에서 AZURE 리소스 관리자(ARM) 템플릿을 생성하고 직접 또는 나중에 수동으로 데이터 팩터리에 배포합니다. ADF 리소스 관리자 템플릿에 대한 자세한 내용은 [Microsoft.DataFactory 리소스 유형을](https://docs.microsoft.com/azure/templates/microso.ft.datafactory/allversions)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서 설명하는 기능은 SQL Server 관리 Studio 버전 18.5 이상입니다. SSMS의 최신 버전을 다운로드하려면 [Download SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)(SSMS(SQL Server Management Studio) 다운로드)를 참조하세요.

## <a name="migrate-ssis-jobs-to-adf"></a>SSIS 작업을 ADF로 마이그레이션

1. SSMS에서 개체 탐색기에서 SQL 서버 에이전트를 선택하고 작업을 선택한 다음 마우스 오른쪽 단추를 클릭하고 **SSIS 작업을 ADF로 마이그레이션합니다.**
![메뉴](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Azure에 로그인하고 Azure 구독, 데이터 팩터리 및 통합 런타임을 선택합니다. Azure Storage는 선택 사항으로, 마이그레이션할 SSIS 작업에 SSIS 파일 시스템 패키지가 있는 경우 패키지 위치 매핑 단계에서 사용됩니다.
![메뉴](media/how-to-migrate-ssis-job-ssms/step1.png)

1. SSIS 작업의 SSIS 패키지 및 구성 파일의 경로를 마이그레이션된 파이프라인이 액세스할 수 있는 대상 경로에 매핑합니다. 이 매핑 단계에서다음을 수행할 수 있습니다.

    1. 소스 폴더를 선택한 다음 **매핑 을 추가합니다.**
    1. 소스 폴더 경로를 업데이트합니다. 유효한 경로는 패키지의 폴더 경로 또는 상위 폴더 경로입니다.
    1. 대상 폴더 경로를 업데이트합니다. 기본값은 1단계에서 선택된 기본 저장소 계정에 대한 상대 경로입니다.
    1. 매핑 삭제를 통해 선택한 **매핑을 삭제합니다.**
![단계2](media/how-to-migrate-ssis-job-ssms/step2.png)
![2-1](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. 마이그레이션할 해당 작업을 선택하고 *해당 실행된 SSIS 패키지 활동의*설정을 구성합니다.

    - *기본 설정은*기본적으로 선택한 모든 단계에 적용됩니다. 각 속성에 대한 자세한 내용은 패키지 위치가 *파일 시스템(패키지)인*경우 [SSIS 패키지 실행 활동에](how-to-invoke-ssis-package-ssis-activity.md) 대한 설정 *탭을* 참조하십시오.
    ![3단계](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *단계 설정,* 선택한 단계에 대한 설정 구성.
        
        **기본 설정 적용**: 기본값이 선택되었습니다. 선택 단계만 구성하려면 선택을 취소합니다.  
        다른 속성에 대한 자세한 내용은 패키지 위치가 *파일 시스템(패키지)인*경우 [SSIS 패키지 실행 활동에](how-to-invoke-ssis-package-ssis-activity.md) 대한 설정 *탭을* 참조하십시오.
    ![3단계-2단계](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. ARM 템플릿을 생성하고 배포합니다.
    1. 마이그레이션된 ADF 파이프라인의 ARM 템플릿에 대한 출력 경로를 선택하거나 입력합니다. 폴더가 없으면 자동으로 만들어집니다.
    2. 데이터 팩터리에 ARM 템플릿 배포 옵션을 **선택합니다.**
        - 기본값은 선택되지 않았습니다. 나중에 생성된 ARM 템플릿을 수동으로 배포할 수 있습니다.
        - 생성된 ARM 템플릿을 데이터 팩터리에 직접 배포하려면 선택합니다.
    ![4단계](media/how-to-migrate-ssis-job-ssms/step4.png)

1. 마이그레이션한 다음 결과를 확인합니다.
![단계 5](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>다음 단계

[파이프라인 실행 및 모니터링](how-to-invoke-ssis-package-ssis-activity.md)
