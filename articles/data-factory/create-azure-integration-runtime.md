---
title: Azure Data Factory에서 Azure 통합 런타임 만들기
description: Azure Data Factory에서 데이터를 복사하고 변환 작업을 디스패치하는 Azure 통합 런타임을 만드는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/13/2020
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 901761c8a5dc17cd7f00c3f04153c8e9aa99b648
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858026"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Azure 통합 런타임을 만들고 구성하는 방법
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

IR(통합 런타임)은 서로 다른 네트워크 환경에서 데이터 통합 기능을 제공하기 위해 Azure Data Factory에서 사용하는 컴퓨팅 인프라입니다. IR에 대한 자세한 내용은 [통합 런타임](concepts-integration-runtime.md)을 참조하세요.

Azure IR은 완전히 관리되는 컴퓨팅을 제공하여 기본적으로 데이터 이동을 수행하고 HDInsight와 같은 컴퓨팅 서비스로 데이터 변환 작업을 디스패치합니다. Azure 환경에서 호스트되며 액세스 가능한 공용 엔드포인트를 통해 공용 네트워크 환경의 리소스에 연결할 수 있습니다.

이 문서에서는 Azure 통합 런타임을 만들고 구성할 수 있는 방법을 소개합니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>기본 Azure IR
기본적으로 각 데이터 팩터리에는 백 엔드에 클라우드 데이터 저장소의 작업 및 공용 네트워크의 컴퓨팅 서비스를 지원하는 Azure IR이 있습니다. 해당 Azure IR 위치는 자동으로 확인됩니다. **connectVia** 속성이 연결된 서비스 정의에 지정되지 않은 경우 기본 Azure IR이 사용됩니다. IR의 위치를 명시적으로 정의하려는 경우 또는 관리 목적으로 다른 IR에 대한 작업 실행을 가상으로 그룹화하려는 경우에만 Azure IR을 명시적으로 만들어야 합니다. 

## <a name="create-azure-ir"></a>Azure IR 만들기

Azure IR를 만들고 설정 하려면 다음 절차를 사용할 수 있습니다.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Azure PowerShell를 통해 Azure IR 만들기
Integration Runtime는 **AzDataFactoryV2IntegrationRuntime** PowerShell cmdlet을 사용 하 여 만들 수 있습니다. Azure IR를 만들려면 명령에 이름, 위치 및 유형을 지정 합니다. 다음은 위치가 "West Europe"으로 설정된 Azure IR을 만드는 샘플 명령입니다.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Azure IR의 경우 형식은 **Managed**로 설정되어야 합니다. 컴퓨팅 세부 정보는 클라우드에서 탄력적으로 완전히 관리되므로 지정할 필요가 없습니다. Azure-SSIS IR을 만들려는 경우 노드 크기 및 노드 개수와 같은 컴퓨팅 세부 정보를 지정합니다. 자세한 내용은 [Azure SSIS IR 만들기 및 구성](create-azure-ssis-integration-runtime.md)을 참조하세요.

AzDataFactoryV2IntegrationRuntime PowerShell cmdlet을 사용 하 여 해당 위치를 변경 하도록 기존 Azure IR를 구성할 수 있습니다. Azure IR의 위치에 대한 자세한 내용은 [통합 런타임 소개](concepts-integration-runtime.md)를 참조하세요.

### <a name="create-an-azure-ir-via-azure-data-factory-ui"></a>Azure Data Factory UI를 통해 Azure IR 만들기
다음 단계를 사용 하 여 Azure Data Factory UI를 사용 하 여 Azure IR를 만듭니다.

1. Azure Data Factory UI의 **시작** 페이지에서 왼쪽 창의 **작성자** 탭을 선택 합니다.

   ![홈 페이지 작성자 단추](media/doc-common-process/get-started-page-author-button.png)

1. 왼쪽 창의 맨 아래에서 **연결** 을 선택 하 고 **연결** 창에서 **통합 런타임** 을 선택 합니다. **+ 새로 만들기**를 선택 합니다.

   ![Integration Runtime 만들기](media/create-azure-integration-runtime/new-integration-runtime.png)

1. **Integration runtime 설정** 페이지에서 **Azure, 자체 호스팅**을 차례로 선택 하 고 **계속**을 선택 합니다. 

1. 다음 페이지에서 **Azure** 를 선택 하 여 Azure IR를 만든 후 **계속**을 선택 합니다.
   ![Integration Runtime 만들기](media/create-azure-integration-runtime/new-azure-integration-runtime.png)

1. Azure IR에 대 한 이름을 입력 하 고 **만들기**를 선택 합니다.
   ![Azure IR 만들기](media/create-azure-integration-runtime/create-azure-integration-runtime.png)

1. 만들기가 완료 되 면 팝업 알림이 표시 됩니다. **통합 런타임** 페이지에서 새로 만든 IR이 목록에 표시 되는지 확인 합니다.

## <a name="use-azure-ir"></a>Azure IR 사용

Azure IR을 만든 후 연결된 서비스 정의에서 참조할 수 있습니다. 다음은 Azure Storage 연결된 서비스에서 이전에 만든 Azure 통합 런타임을 참조할 수 있는 방법에 대한 샘플입니다.

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>다음 단계
다른 형식의 통합 런타임을 만드는 방법은 다음 문서를 참조하세요.

- [자체 호스팅 통합 런타임 만들기](create-self-hosted-integration-runtime.md)
- [Azure SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md)
 
