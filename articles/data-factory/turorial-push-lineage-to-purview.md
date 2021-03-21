---
title: Data Factory 계보 데이터를 Azure Purview에 푸시
description: Azure 부서의 범위에 Data Factory 계보 데이터를 푸시하는 방법에 대해 알아봅니다.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 3f2297ae619145ec19b53ba79d70b7c085cbcaab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361454"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Azure 부서의 범위 (미리 보기)에 Data Factory 계보 데이터 푸시

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 자습서에서는 Data Factory UI (사용자 인터페이스)를 사용 하 여 Azure 부서의 범위 계정에 작업을 실행 하 고 계보 데이터를 보고 하는 파이프라인을 만듭니다. 그런 다음 Azure 부서의 범위 계정에서 모든 계보 정보를 볼 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Data Factory**. Azure Data Factory 없는 경우 [Azure Data Factory 만들기](./quickstart-create-data-factory-portal.md)를 참조 하세요.
* **Azure 부서의 범위 계정**. 부서의 범위 계정은 데이터 팩터리에서 생성 된 모든 계보 데이터를 캡처합니다. Azure 부서의 범위 계정이 없는 경우 [Azure 부서의 범위 만들기](../purview/create-catalog-portal.md)를 참조 하세요.


## <a name="run-data-factory-activities-and-push-lineage-data-to-azure-purview"></a>Data Factory 작업을 실행 하 고 Azure 부서의 범위에 계보 데이터 푸시
### <a name="step-1--connect-data-factory-to-your-purview-account"></a>1 단계: 부서의 범위 계정에 Data Factory 연결
부서의 범위 portal에서 부서의 범위 계정에 로그인 하 고 **관리 센터** 로 이동 합니다. **외부 연결** 에서 **Data Factory** 를 선택 하 고 **새로** 만들기 단추를 클릭 하 여 새 Data Factory에 대 한 연결을 만듭니다. 
[![Data Factory 및 부서의 범위 계정 ](./media/data-factory-purview/connect-adf-to-purview.png) 간의 연결 만들기에 대 한 스크린샷 ](./media/data-factory-purview/connect-adf-to-purview.png#lightbox)

팝업 페이지에서이 부서의 범위 계정에 연결 하려는 Data Factory를 선택할 수 있습니다. 
![새 연결에 대 한 스크린샷](./media/data-factory-purview/new-adf-purview-connection.png)

연결을 만든 후에 상태를 확인할 수 있습니다. **연결 됨** 은 Data Factory와이 부서의 범위 간의 연결이 성공적으로 연결 되었음을 의미 합니다. 
> [!NOTE]
> Data Factory와 Azure 부서의 범위 간의 연결을 만들려면 부서의 범위 계정 및 Data Factory **참여자** 역할에 아래 역할 중 하나를 할당 해야 합니다.
> - 소유자
> - 사용자 액세스 관리자

### <a name="step-2-run-copy-and-dataflow-activities-in-data-factory"></a>2 단계: Data Factory에서 복사 및 데이터 흐름 작업 실행
Data Factory에서 파이프라인, 복사 작업 및 데이터 흐름 작업을 만들 수 있습니다. 계보 데이터 캡처에 대 한 추가 구성은 필요 하지 않습니다. 계보 데이터는 활동을 실행 하는 동안 자동으로 캡처됩니다.
![복사 및 데이터 흐름 작업의 스크린샷 복사 ](./media/data-factory-purview/adf-activities-for-lineage.png) 및 데이터 흐름 작업을 만드는 방법을 모르는 경우 Azure Data Factory를 [사용 하 여 Azure SQL Database에서 데이터베이스로 데이터 복사](./tutorial-copy-data-portal.md) 및 데이터 [흐름 매핑을 사용 하 여 데이터 변환](./tutorial-data-flow.md)을 참조 하세요.

### <a name="step-3-run-execute-ssis-package-activities-in-data-factory"></a>3 단계: Data Factory에서 SSIS 패키지 실행 작업 실행
Data Factory에서 파이프라인을 만들고 SSIS 패키지 작업을 실행할 수 있습니다. 계보 데이터 캡처에 대 한 추가 구성은 필요 하지 않습니다. 계보 데이터는 활동을 실행 하는 동안 자동으로 캡처됩니다.
![SSIS 패키지 실행 작업 스크린샷](./media/data-factory-purview/ssis-activities-for-lineage.png)

SSIS 패키지 작업을 만드는 방법을 모를 경우 [Azure에서 Ssis 패키지 실행](./tutorial-deploy-ssis-packages-azure.md)을 참조 하세요.

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>4 단계: 부서의 범위 계정에서 계보 정보 보기
부서의 범위 계정으로 돌아갑니다. 홈 페이지에서 **자산 찾아보기** 를 선택 합니다. 원하는 자산을 선택 하 고 계보 탭을 클릭 합니다. 모든 계보 정보를 볼 수 있습니다.
[![부서의 범위 계정 ](./media/data-factory-purview/view-dataset.png) 스크린샷 ](./media/data-factory-purview/view-dataset.png#lightbox)

복사 활동에 대 한 계보 데이터를 볼 수 있습니다.
[![계보 ](./media/data-factory-purview/copy-lineage.png) 복사 스크린샷 ](./media/data-factory-purview/copy-lineage.png#lightbox)

데이터 흐름 작업의 계보 데이터도 볼 수 있습니다.
[![데이터 흐름 계보 ](./media/data-factory-purview/dataflow-lineage.png) 의 스크린샷 ](./media/data-factory-purview/dataflow-lineage.png#lightbox)

> [!NOTE] 
> 데이터 흐름 작업의 계보의 경우 원본 및 싱크만 지원 합니다. 데이터 흐름 변환에 대 한 계보는 아직 지원 되지 않습니다.

SSIS 패키지 실행 작업에 대 한 계보 데이터도 볼 수 있습니다.
[![SSIS 계보 ](./media/data-factory-purview/ssis-lineage.png) 스크린샷 ](./media/data-factory-purview/ssis-lineage.png#lightbox)

> [!NOTE] 
> SSIS 패키지 실행 작업의 계보의 경우 원본 및 대상만 지원 합니다. 변환에 대 한 계보는 아직 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계
[카탈로그 계보 사용자 가이드](../purview/catalog-lineage-user-guide.md)

[Azure 부서의 범위에 Data Factory 연결](connect-data-factory-to-azure-purview.md)