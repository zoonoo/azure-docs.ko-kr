---
title: "Azure Portal을 사용하여 Azure Data Factory 만들기 | Microsoft Docs"
description: "클라우드 데이터 저장소(Azure Blob Storage)에서 다른 클라우드 데이터 저장소(Azure SQL Databse)로 데이터를 복사하는 Azure Data Factory를 만듭니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: b884d7f08311cc60dc3af500f552d525d23b91e6
ms.contentlocale: ko-kr
ms.lasthandoff: 09/26/2017

---
# <a name="create-a-data-factory-using-the-azure-portal"></a>Azure Portal을 사용하여 데이터 팩터리 만들기
Azure Data Factory는 데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위해 클라우드에서 데이터 기반 워크플로를 만들 수 있는 클라우드 기반 데이터 통합 서비스입니다. Azure Data Factory를 사용하여 서로 다른 데이터 저장소에서 데이터를 수집할 수 있는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 일정을 조정하며, Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics 및 Azure Machine Learning과 같은 계산 서비스를 사용하여 데이터를 처리/변환하고, 사용할 BI(비즈니스 인텔리전스) 응용 프로그램의 Azure SQL Data Warehouse와 같은 데이터 저장소에 출력 데이터를 게시할 수 있습니다. 

이 빠른 시작에서 Azure Portal을 사용하여 데이터 팩터리를 만듭니다. 데이터 팩터리를 만든 후, 다른 빠른 시작처럼 PowerShell, .NET SDK, Python SDK 또는 REST API를 사용하여 원본 데이터 저장소에서 대상 데이터 저장소로 데이터를 복사하는 데이터 파이프라인을 만들어야 합니다. 현재 Azure Portal을 사용하여 데이터 팩터리에 파이프라인을 만들 수 없습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인
[Azure 포털](https://portal.azure.com/) 에 로그인합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
이 빠른 시작의 일부로 수행하는 단계는 다음과 같습니다.
1. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 블레이드에서 **이름**에 **ADFTutorialDataFactory**를 입력합니다. 
      
     ![새 데이터 팩터리 블레이드](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 다음 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameADFTutorialDataFactory) 다시 만듭니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](naming-rules.md) 항목을 참조하세요.
  
       `Data factory name “ADFTutorialDataFactory” is not available`
3. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
     
      - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
      - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
      이 빠른 시작의 일부 단계에서는 리소스 그룹에 **ADFTutorialResourceGroup**이라는 이름을 사용한다고 가정합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
4. **버전**에 **V2(미리 보기)**를 선택합니다.
5. 데이터 팩터리의 **위치** 를 선택합니다. 현재 **미국 동부** 지역에서만 V2 데이터 팩터리를 만들 수 있습니다. 단, 데이터 팩터리에서 사용되는 계산 및 데이터 저장소는 다른 지역에 있을 수 있습니다. 
6. **대시보드에 고정**을 선택합니다.     
7. **만들기**를 클릭합니다.
      
      > [!IMPORTANT]
      > 데이터 팩터리 인스턴스를 만들려면 구독/리소스 그룹 수준에서 [데이터 팩터리 참여자](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) 역할의 구성원이어야 합니다.
      > 
      > 데이터 팩터리의 이름은 나중에 DNS 이름으로 표시되므로 공개적으로 등록될 수도 있습니다.             
3. 대시보드에서 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다. 

    ![데이터 팩터리 배포 중 타일](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
1. 만들기가 완료되면 이미지와 같이 **Data Factory** 블레이드가 표시됩니다.
   
   ![데이터 팩터리 홈페이지](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)


## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인은 Azure Blob 저장소의 한 위치에서 다른 위치로 데이터를 복사합니다. [자습서](tutorial-copy-data-dot-net.md)를 통해 더 많은 시나리오에서의 데이터 팩터리 사용에 관해 알아보세요. 
