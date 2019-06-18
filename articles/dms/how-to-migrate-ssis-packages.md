---
title: Azure SQL Database로 SQL Server Integration Services 패키지를 다시 배포 | Microsoft Docs
description: Azure SQL Database로 SQL Server Integration Services 패키지를 마이그레이션하는 방법에 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: 603a9df8e3f499c832bbfdcbef966de86003d6b7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080645"
---
# <a name="redeploy-sql-server-integration-services-packages-to-azure-sql-database"></a>Azure SQL Database로 SQL Server Integration Services 패키지를 다시 배포

SQL Server Integration Services (SSIS)를 사용 하 고 대상 Azure SQL Database에서 호스팅되는 SSISDB에 SQL Server에서 호스팅되는 SSISDB 원본에서 SSIS 프로젝트/패키지를 마이그레이션할 경우 Integration Services 배포를 사용 하 여 배포할 수 있습니다. 마법사입니다. SSMS(SQL Server Management Studio) 내에서 마법사를 시작할 수 있습니다.

사용하는 SSIS 버전이 2012 이전인 경우 SSIS 프로젝트/패키지를 프로젝트 배포 모델로 재배포하기 전에 먼저 SSMS에서 시작할 수도 있는 Integration Services 프로젝트 변환 마법사를 사용하여 변환해야 합니다. 자세한 내용은 문서 [프로젝트를 프로젝트 배포 모델로 변환](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)을 참조하세요.

> [!NOTE]
> Azure 데이터베이스 마이그레이션 서비스 (DMS) 현재 Azure SQL Database 서버를 원본 SSISDB의 마이그레이션을 지원 하지 않습니다 하지만 SSIS 프로젝트/패키지 프로세스를 사용 하 여 다시 배포할 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
>
> * 원본 SSIS 프로젝트/패키지를 평가합니다.
> * Azure에 SSIS 프로젝트/패키지를 마이그레이션합니다.

## <a name="prerequisites"></a>필수 조건

이러한 단계를 완료하려면 다음이 필요합니다.

* SSMS 버전 17.2 이상
* SSISDB를 호스트할 대상 데이터베이스 서버의 인스턴스 하면 이미 계정이 없는 경우 SQL Server (논리 서버에만 해당)로 이동 하 여 Azure portal을 사용 하 여 (데이터베이스 없이) Azure SQL Database 서버 만들기 [폼](https://ms.portal.azure.com/#create/Microsoft.SQLServer)합니다.
* SSIS에서 Azure 데이터 팩터리 (ADF) 대상 Azure SQL Database 서버 인스턴스에서 호스팅되는 SSISDB 사용 하 여 Azure SSIS IR (Integration Runtime)을 포함 하 프로 비전 되어야 합니다 (이 문서에 설명 된 대로 [AZURE-SSIS 통합 프로 비전 Azure Data Factory에서 런타임](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)).

## <a name="assess-source-ssis-projectspackages"></a>원본 SSIS 프로젝트/패키지 평가

SSISDB 원본의 평가 아직 통합 되지 데이터베이스 마이그레이션 길잡이 (DMA) 또는 Azure 데이터베이스 마이그레이션 서비스 (DMS) 하는 동안 SSIS 프로젝트/패키지는 평가/유효성을 검사할 수 SSISDB에 호스팅된 대상 재배포 되는 Azure SQL Database 서버입니다.

## <a name="migrate-ssis-projectspackages"></a>SSIS 프로젝트/패키지 마이그레이션

Azure SQL Database 서버에 SSIS 프로젝트/패키지를 마이그레이션하려면 다음 단계를 수행 합니다.

1. SSMS를 연 다음, **옵션**을 선택하여 **서버에 연결** 대화 상자를 표시합니다.

2. 에 **로그인** 탭에서 대상 SSISDB를 호스트 하는 Azure SQL Database 서버에 연결 하는 데 필요한 정보를 지정 합니다.

    ![SSIS 로그인 탭](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. **연결 속성** 탭의 **데이터베이스에 연결** 텍스트 상자에서 **SSISDB**를 선택하거나 입력한 다음, **연결**을 선택합니다.

    ![SSIS 연결 속성 탭](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. SSMS 개체 탐색기에서 **Integration Services 카탈로그** 노드를 확장하고, **SSISDB**를 확장하고, 기존 폴더가 없는 경우 **SSISDB**를 마우스 오른쪽 단추로 클릭하고 새 폴더를 만듭니다.

5. **SSISDB** 아래에서 폴더를 확장하고, **프로젝트**를 마우스 오른쪽 단추로 클릭한 다음, **프로젝트 배포**를 선택합니다.

    ![SSIS SSISDB 노드 확장됨](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. Integration Services 배포 마법사의 **소개** 페이지에서 정보를 검토한 다음, **다음**을 선택합니다.

    ![배포 마법사 소개 페이지](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. **원본 선택** 페이지에서 배포하려는 기존 SSIS 프로젝트를 지정합니다.

    SSMS도 원본 SSISDB를 호스팅하는 SQL Server에 연결된 경우 **Integration Services 카탈로그**를 선택한 다음, 프로젝트를 직접 배포할 카탈로그의 서버 이름 및 프로젝트 경로를 입력합니다.

    또는 **프로젝트 배포 파일**을 선택한 다음, 프로젝트를 배포하도록 기존 프로젝트 배포 파일(.ispac)에 경로를 지정합니다.

    ![배포 마법사 원본 선택 페이지](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. **다음**을 선택합니다.
9. **대상 선택** 페이지에서 프로젝트에 대한 대상을 지정합니다.

    a. 서버 이름 텍스트 상자에 정규화 된 Azure SQL Database 서버 이름 입력 (< 서버 _ 이름 >. database.windows.net).

    b. 인증 정보를 제공한 다음, **연결**을 선택합니다.

    ![배포 마법사 대상 선택 페이지](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. 선택 **찾아보기** SSISDB에서 대상 폴더를 지정 하 여 선택한 **다음**합니다.

    > [!NOTE]
    > **연결**을 선택한 후에만 **다음** 단추가 활성화됩니다.

10. **유효성 검사** 페이지에서 모든 오류/경고를 본 다음, 필요한 경우 패키지를 적절하게 수정합니다.

    ![배포 마법사 유효성 검사 페이지](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. **다음**을 선택합니다.

12. **검토** 페이지에서 배포 설정을 검토합니다.

    > [!NOTE]
    > 선택 하 여 설정을 변경할 수 있습니다 **이전** 또는 왼쪽 창의 단계 링크 중 하나를 선택 합니다.

13. **배포**를 선택하여 배포 프로세스를 시작합니다.

14. 배포 프로세스가 완료된 후에 각 배포 작업의 성공 여부를 표시하는 결과 페이지를 볼 수 있습니다.
    a. 작업이 실패한 경우 **결과** 열에서 **실패함**을 선택하여 오류의 설명을 표시합니다.
    b. 필요에 따라 **보고서 저장**을 선택하여 XML 파일에 결과를 저장합니다.

15. **닫기**를 선택하여 Integration Services 배포 마법사를 종료합니다.

프로젝트의 배포가 오류 없이 성공하면 Azure-SSIS IR에서 실행하기 위해 포함된 모든 패키지를 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Microsoft [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)의 마이그레이션 지침을 검토합니다.
