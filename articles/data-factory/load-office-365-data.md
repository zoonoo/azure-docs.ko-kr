---
title: Azure Data Factory를 사용하여 Office 365에서 데이터 로드
description: Azure Data Factory를 사용하여 Office 365에서 데이터 복사
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: 3422176ed89b7f575c11cc40e5be8420da0018b0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415807"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Office 365에서 데이터 로드

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Data Factory를 사용하여 _Office 365에서 Azure Blob Storage로 데이터를 로드_하는 방법을 설명합니다. Azure Data Lake Gen1 또는 Gen2로 데이터를 복사할 때도 이와 유사한 단계를 따를 수 있습니다. Office 365에서 데이터를 복사하는 작업과 관련된 일반 정보는 [Office 365 커넥터 문서](connector-office-365.md)를 참조하세요.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. 왼쪽 메뉴에서 > 리소스**분석** > **데이터 팩터리** **만들기를**선택합니다. 
   
   !["새로 만들기" 창에서 데이터 팩터리 선택](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **새 데이터 팩터리** 페이지에서 다음 그림에 표시된 필드의 값을 제공합니다.
      
   ![새 데이터 팩터리 페이지](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **이름**: Azure 데이터 팩터리의 전역 고유 이름을 입력합니다. "데이터 팩터리 이름 *LoadFromOffice365Demo을* 사용할 수 없습니다"라는 오류가 발생하면 데이터 팩터리의 다른 이름을 입력합니다. 예를 들어 _**yourname**_**LoadFromOffice365Demo**라는 이름을 사용할 수 있습니다. 데이터 팩터리를 다시 만들어 봅니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
    * **구독**: 데이터 팩터리를 만들 Azure 구독을 선택합니다. 
    * **리소스 그룹**: 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 옵션을 선택하고 리소스 그룹의 이름을 입력합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요.  
    * **버전**: **V2**를 선택합니다.
    * **위치**: 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소가 다른 위치 및 지역에 있어도 됩니다. 이러한 데이터 저장소는 Azure Data Lake Store, Azure Storage, Azure SQL Database 등을 포함합니다.

3. **만들기**를 선택합니다.
4. 만들기가 완료되면 데이터 팩터리로 이동합니다. 다음 그림과 같이 **데이터 팩터리** 홈페이지가 표시됩니다.
   
   ![데이터 팩터리 홈페이지](./media/load-office-365-data/data-factory-home-page.png)

5. **작성 및 모니터링** 타일을 선택하여 별도의 탭에서 데이터 통합 애플리케이션을 시작합니다.

## <a name="create-a-pipeline"></a>파이프라인 만들기

1. "시작해 보자" 페이지에서 **파이프라인 만들기를**선택합니다.
 
    ![파이프라인 만들기](./media/load-office-365-data/create-pipeline-entry.png)

2. 파이프라인의 **일반 탭에서** 파이프라인 **이름에** 대해 "Copy파이프라인"을 입력합니다.

3. 활동 도구 상자 > 이동 및 변환 범주에서 도구 상자의 **활동 복사**를 끌어서 파이프라인 디자이너 화면에 놓습니다. 활동 이름으로 "CopyFromOffice365ToBlob"를 지정합니다.

### <a name="configure-source"></a>원본 구성

1. 파이프라인 &gt; **원본 탭**으로 이동한 다음, **+ 새로 만들기**를 클릭하여 원본 데이터 세트를 만듭니다. 

2. 새 데이터 집합 창에서 **Office 365를**선택한 다음 **계속을**선택합니다.
 
3. 이제 복사 활동 구성 탭에 있습니다. Office 365 데이터 집합 옆에 있는 **편집** 단추를 클릭하여 데이터 구성을 계속합니다.

    ![Office 365 데이터 세트 일반 정보 구성](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. Office 365 데이터 세트의 새 탭이 열립니다. 속성 창 아래쪽의 **일반 탭에서** 이름에 대해 "SourceOffice365Dataset"을 입력합니다.
 
5. 속성 창의 **연결 탭으로** 이동합니다. 연결된 서비스 텍스트 상자 옆에 있는 **+ 새로 만들기**를 클릭합니다.

6. 새 연결된 서비스 창에서 "Office365LinkedService"를 이름으로 입력하고 서비스 주체 ID 및 서비스 주체 키를 입력한 다음 연결을 테스트하고 연결된 서비스를 배포하기 위해 **만들기를** 선택합니다.

    ![새 Office 365 연결된 서비스](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. 연결된 서비스를 만든 후에 데이터 세트 설정으로 돌아갑니다. **테이블**옆에 있는 아래쪽 화살표를 선택하여 사용 가능한 Office 365 데이터 집합 목록을 확장하고 "BasicDataSet_v0를 선택합니다. 드롭다운 목록에서 Message_v0"

    ![Office 365 데이터 세트 테이블 구성](./media/load-office-365-data/edit-dataset.png)

8. 이제 **파이프라인** > **소스 탭으로** 돌아가 Office 365 데이터 추출에 대한 추가 속성을 계속 구성합니다.  사용자 범위 및 사용자 범위 필터는 Office 365에서 추출하려는 데이터를 제한하도록 정의할 수 있는 선택적 조건자입니다. 이러한 설정을 구성하는 방법은 [Office 365 데이터 집합 속성](https://docs.microsoft.com/azure/data-factory/connector-office-365#dataset-properties) 섹션을 참조하십시오.

9. 날짜 필터 중 하나를 선택하고 시작 시간 및 종료 시간 값을 제공해야 합니다.

10. **스키마 가져오기** 탭을 클릭하여 메시지 데이터 집합에 대한 스키마를 가져옵니다.

    ![Office 365 데이터 세트 스키마 구성](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>싱크 구성

1. 파이프라인 &gt; **싱크 탭**으로 이동한 다음, **+ 새로 만들기**를 선택하여 싱크 데이터 세트를 만듭니다.
 
2. 새 데이터 집합 창에서 Office 365에서 복사할 때 지원되는 대상만 선택됩니다. **Azure Blob 저장소를**선택하고 이진 형식을 선택한 다음 **계속을**선택합니다.  이 자습서에서는 Office 365 데이터를 Azure Blob Storage에 복사합니다.

3. Azure Blob Storage 데이터 집합 옆의 **편집** 단추를 클릭하여 데이터 구성을 계속합니다.

4. 속성 창의 **일반 탭**에서 이름에 "OutputBlobDataset"를 입력합니다.

5. 속성 창의 **연결 탭으로** 이동합니다. **연결된 서비스** 텍스트 상자 옆에 있는 + 새로 만들기를 선택합니다.

6. 새 연결된 서비스 창에서 "AzureStorageLinkedService"를 이름으로 입력하고 인증 방법의 드롭다운 목록에서 "서비스 주체"를 선택하고 서비스 끝점, 테넌트, 서비스 주체 ID 및 서비스 주체 키를 입력한 다음 저장을 선택하여 연결된 서비스를 배포합니다.  Azure Blob Storage용 서비스 주체 인증을 설정하는 방법은 [여기](connector-azure-blob-storage.md#service-principal-authentication)를 참조하세요.

    ![새 Blob 연결된 서비스](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>파이프라인 유효성 검사

파이프라인에 대한 유효성을 검사하려면 도구 모음에서 **유효성 검사**를 선택합니다.

오른쪽 위에서 코드를 클릭하여 파이프라인과 연결된 JSON 코드를 확인할 수도 있습니다.

## <a name="publish-the-pipeline"></a>파이프라인 게시

위쪽 도구 모음에서 **모두 게시**를 선택합니다. 이 작업은 사용자가 만든 엔터티(데이터 세트 및 파이프라인)를 Data Factory에 게시합니다.

![변경 내용 게시](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>수동으로 파이프라인 트리거

도구 모음에서 **트리거 추가**를 선택한 다음, **지금 트리거**를 선택합니다. 파이프라인 실행 페이지에서 **마침**을 선택합니다. 

## <a name="monitor-the-pipeline"></a>파이프라인 모니터링

왼쪽의 **모니터** 탭으로 이동합니다. 수동 트리거로 트리거되는 파이프라인 실행이 표시됩니다. **작업** 열의 링크를 사용하여 활동 세부 정보를 보고 파이프라인을 다시 실행할 수 있습니다.

![파이프라인 모니터링](./media/load-office-365-data/pipeline-status.png) 

파이프라인 실행과 연결된 활동 실행을 확인하려면 작업 열에서 **활동 실행 보기** 링크를 선택합니다. 이 예에서는 활동이 하나뿐이므로 목록에 하나의 항목만 표시됩니다. 복사 활동에 대한 자세한 내용은 작업 열에서 **세부 정보** 링크(안경 아이콘)를 선택합니다.

![작업 모니터링](./media/load-office-365-data/activity-status.png) 

이 컨텍스트에 대한 데이터를 처음 요청하는 경우(액세스 중인 데이터 테이블, 로드되는 데이터, 로드되는 데이터 및 데이터 액세스 요청을 하는 사용자 ID의 조합)는 복사 활동 상태가 **진행 중이며**작업 에서 "세부 정보" 링크를 클릭할 때만 **RequesetingConsent**로 상태가 표시됩니다.  데이터 액세스 승인자 그룹의 구성원이 Privileged Access Management에서 요청을 승인해야 데이터 추출을 진행할 수 있습니다.

_동의 요청 상태:_
![활동 실행 세부 정보 - 동의 요청](./media/load-office-365-data/activity-details-request-consent.png) 

_데이터 추출 중 상태:_

![활동 실행 세부 정보 - 데이터 추출](./media/load-office-365-data/activity-details-extract-data.png) 

동의가 제공되면 데이터 추출이 계속되고 얼마 후 파이프라인 실행이 성공한 것으로 표시됩니다.

![파이프라인 모니터링 - 성공](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

이제 대상 Azure Blob 저장소로 이동하여 Office 365 데이터가 바이너리 형식으로 추출되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

Azure SQL Data Warehouse 지원에 대한 자세한 내용은 다음 문서를 참조하세요. 

> [!div class="nextstepaction"]
>[Office 365 커넥터](connector-office-365.md)