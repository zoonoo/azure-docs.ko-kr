---
title: Data Factory 계보 데이터를 Azure Purview에 푸시
description: Data Factory 계보 데이터를 Azure Purview에 푸시하는 방법을 알아봅니다.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 3f2297ae619145ec19b53ba79d70b7c085cbcaab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361454"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Data Factory 계보 데이터를 Azure Purview에 푸시(미리 보기)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 자습서에서는 Data Factory UI(사용자 인터페이스)를 사용하여 작업을 실행하고 계보 데이터를 Azure Purview 계정에 보고하는 파이프라인을 만듭니다. 그러면, 모든 계보 정보를 Azure Purview 계정에서 볼 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Data Factory**. Azure Data Factory가 없는 경우 [Azure Data Factory 만들기](./quickstart-create-data-factory-portal.md)를 참조하세요.
* **Azure  Purview 계정** Purview 계정은 Data Factory에서 생성된 모든 계보 데이터를 캡처합니다. Azure Purview 계정이 없는 경우 [Azure Purview 만들기](../purview/create-catalog-portal.md)를 참조하세요.


## <a name="run-data-factory-activities-and-push-lineage-data-to-azure-purview"></a>Data Factory 작업을 실행하고 계보 데이터를 Azure Purview에 푸시
### <a name="step-1--connect-data-factory-to-your-purview-account"></a>1단계: Purview 계정에 Data Factory 연결
Purview 포털에서 Purview 계정에 로그인하고 **관리 센터** 로 이동합니다. **외부 연결** 에서 **Data Factory** 를 선택하고 **새로 만들기** 단추를 클릭하여 새 Data Factory에 대한 연결을 만듭니다. 
[![Data Factory 및 Purview 계정 간의 연결 만들기 스크린샷](./media/data-factory-purview/connect-adf-to-purview.png)](./media/data-factory-purview/connect-adf-to-purview.png#lightbox)

팝업 페이지에서 이 Purview 계정에 연결하려는 Data Factory를 선택할 수 있습니다. 
![새 연결 스크린샷](./media/data-factory-purview/new-adf-purview-connection.png)

연결을 만든 후에 상태를 확인할 수 있습니다. **연결됨** 은 Data Factory와 이 Purview 간의 연결이 성공적으로 연결되었음을 의미합니다. 
> [!NOTE]
> Data Factory와 Azure Purview 간의 연결을 만들려면 Purview 계정 및 Data Factory **기여자** 역할에 아래 역할 중 하나를 할당해야 합니다.
> - 소유자
> - 사용자 액세스 관리자

### <a name="step-2-run-copy-and-dataflow-activities-in-data-factory"></a>2단계: Data Factory에서 복사 및 데이터 흐름 작업 실행
Data Factory에서 파이프라인, 복사 작업 및 데이터 흐름 작업을 만들 수 있습니다. 계보 데이터 캡처에 대한 추가 구성은 필요하지 않습니다. 계보 데이터는 작업 실행 중에 자동으로 캡처됩니다.
![복사 및 데이터 흐름 작업에 대한 스크린샷](./media/data-factory-purview/adf-activities-for-lineage.png) 복사 및 데이터 흐름 작업을 만드는 방법을 모르는 경우 [Azure Data Factory를 사용하여 Azure Blob Storage에서 Azure SQL Database의 데이터베이스로 데이터 복사](./tutorial-copy-data-portal.md) 및 [데이터 흐름 매핑을 사용하여 데이터 변환](./tutorial-data-flow.md)을 참조하세요.

### <a name="step-3-run-execute-ssis-package-activities-in-data-factory"></a>3단계: Data Factory에서 SSIS 패키지 실행 작업 실행
Data Factory에서 파이프라인 및 SSIS 패키지 실행 작업을 만들 수 있습니다. 계보 데이터 캡처에 대한 추가 구성은 필요하지 않습니다. 계보 데이터는 작업 실행 중에 자동으로 캡처됩니다.
![SSIS 패키지 실행 작업 스크린샷](./media/data-factory-purview/ssis-activities-for-lineage.png)

SSIS 패키지 실행 작업을 만드는 방법을 모르는 경우 [Azure에서 SSIS 패키지 실행](./tutorial-deploy-ssis-packages-azure.md)을 참조하세요.

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>4단계: Purview 계정에서 계보 정보 보기
Purview 계정으로 다시 이동합니다. 홈 페이지에서 **자산 찾아보기** 를 선택합니다. 원하는 자산을 선택하고 계보 탭을 클릭합니다. 모든 계보 정보를 볼 수 있습니다.
[![Purview 계정 스크린샷](./media/data-factory-purview/view-dataset.png)](./media/data-factory-purview/view-dataset.png#lightbox)

복사 작업의 계보 데이터를 볼 수 있습니다.
[![계보 복사 스크린샷](./media/data-factory-purview/copy-lineage.png)](./media/data-factory-purview/copy-lineage.png#lightbox)

데이터 흐름 작업의 계보 데이터도 볼 수 있습니다.
[![데이터 흐름 계보 스크린샷](./media/data-factory-purview/dataflow-lineage.png)](./media/data-factory-purview/dataflow-lineage.png#lightbox)

> [!NOTE] 
> 데이터 흐름 작업의 계보의 경우 원본 및 싱크만 지원됩니다. 데이터 흐름 변환의 계보는 아직 지원되지 않습니다.

SSIS 패키지 실행 작업의 계보 데이터도 볼 수 있습니다.
[![SSIS 계보 스크린샷](./media/data-factory-purview/ssis-lineage.png)](./media/data-factory-purview/ssis-lineage.png#lightbox)

> [!NOTE] 
> SSIS 패키지 실행 작업의 계보의 경우 원본 및 대상만 지원됩니다. 변환의 계보는 아직 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계
[카탈로그 계보 사용자 가이드](../purview/catalog-lineage-user-guide.md)

[Azure Purview에 Data Factory 연결](connect-data-factory-to-azure-purview.md)