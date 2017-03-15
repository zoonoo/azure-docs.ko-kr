---
title: "지역 간 마이그레이션에 대한 Azure Data Lake Store 지침 | Microsoft Docs"
description: "지역 간 마이그레이션에 대한 Azure Data Lake Store 지침"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/27/2017
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: 3c83a76c589178d5a2a01ed4edd208e42fb2e83e
ms.openlocfilehash: 785f97a28c060f88a0e0f3a5aa9cf6555ed2b5a4
ms.lasthandoff: 02/22/2017


---
# <a name="guidance-to-migrate-azure-data-lake-store-across-regions"></a>지역 간 Azure Data Lake Store를 마이그레이션하는 지침

Azure Data Lake Store를 새 지역에서 사용할 수 있기에 지역을 활용하는 일회성 마이그레이션을 수행하기 위해 결정할 수 있습니다.  마이그레이션을 계획하고 수행할 때 고려해야 할 사항에 대한 몇 가지 지침입니다.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **두 개의 다른 지역의 Azure Data Lake Store 계정** 만드는 방법에 대한 지침은 [Azure Data Lake Store 시작](data-lake-store-get-started-portal.md)을 참조하세요.
* **Azure Data Factory**.  자세히 알아보려면 [Azure Data Factory 소개](../data-factory/data-factory-introduction.md)를 참조하세요.


## <a name="guidance-for-migration"></a>마이그레이션에 대한 지침

먼저 Azure Data Lake Store에서 데이터를 쓰기, 읽기, 또는 처리하는 응용 프로그램에 적합한 마이그레이션 전략을 식별하는 것이 좋습니다. 전략을 선택할 때 응용 프로그램의 가용성 요구 사항(예: 가동 중지 시간)을 고려해야 합니다. 예를 들어 가장 간단한 방법은 데이터를 "리프트 앤 시프트"하는 것입니다.  즉, 모든 데이터가 새 지역에 복사되는 동안 이전 지역에서 응용 프로그램을 일시 중지합니다.  복사 프로세스가 완료되면 새 지역에서 응용 프로그램을 다시 시작하고 이전 Azure Data Lake Store 계정을 삭제할 수 있습니다.  그러나 이렇게 마이그레이션하는 동안 가동이 중지됩니다.

또는 가동 중지 시간을 줄이기 위해 새 지역에서 새 데이터를 즉시 수집하기 시작하고 필요한 최소 데이터를 모은 즉시 새 지역에서 응용 프로그램을 실행하기 시작할 수 있습니다.  반면 새 지역에서는 이전 Azure Data Lake Store 계정에서 새 Azure Data Lake Store 계정으로 오래된 데이터를 복사할 수 있습니다.  이렇게 하면 가동 중지 시간이 없이 새 지역으로 전환할 수 있습니다.  오래된 데이터를 모두 복사했다면 이전 ADLS 계정을 삭제할 수 있습니다.

마이그레이션을 계획할 때 고려해야 할 다른 중요한 세부 정보는 다음과 같습니다.

* **데이터의 볼륨** - 데이터의 볼륨(GB, 파일 및 폴더 등의 수)은 이동에 필요한 시간 및 리소스에 영향을 줍니다.

* **Azure Data Lake Store 계정 이름** - 새 지역에 있는 새 계정 이름은 전역적으로 고유해야 합니다. 예를 들어, contosoeastus2.azuredatalakestore.net이 미국 동부 2에 있는 이전 저장소 계정의 이름인 경우 북유럽에 있는 저장소 계정의 이름은 contosonortheu.azuredatalakestore.net으로 지정할 수 있습니다

* **도구 선택** - Azure Data Lake Store 파일을 복사하는 경우 [Azure Data Factory 복사 작업](../data-factory/data-factory-azure-datalake-connector.md)을 사용하는 것이 좋습니다.   Azure Data Factory는 데이터를 이동할 때 고성능 및 안정성을 지원합니다.  Azure Data Factory가 폴더 계층 구조와 파일의 내용만을 복사한다는 점을 고려해야 합니다. 적용한 모든 ACL(액세스 제어 목록)을 새 계정에 수동으로 적용해야 합니다.  [Azure Data Factory 성능 조정 지침](../data-factory/data-factory-copy-activity-performance.md)은 최상의 시나리오를 위한 성능 목표에 참조하기 좋습니다.  짧은 기간에 데이터를 복사하려는 경우 추가 클라우드 데이터 이동 단위를 사용해야 합니다.  ADLCopy와 같은 기타 도구는 지역 간에 데이터를 복사하도록 지원하지 않습니다.  

* **[대역폭 요금](https://azure.microsoft.com/en-us/pricing/details/bandwidth/) ** Azure 지역 외부에서 데이터를 전송하기 때문에 요금이 적용될 수 있습니다.

* **데이터에 대한 ACL** - 파일 및 폴더에 ACL을 적용하여 새 지역에서 데이터를 보호합니다.  이 지침은 [여기](data-lake-store-secure-data.md)에 있습니다.  ACL을 업데이트하고 조정할 수 있는 기회로써 이 마이그레이션을 수행하는 것이 좋습니다.  그러나 비슷한 설정을 사용하려는 경우 포털, [PowerShell cmdlet](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.datalakestore/v3.1.0/get-azurermdatalakestoreitempermission) 또는 SDK을 통해 파일에 적용된 ACL을 볼 수 있습니다.  

* **분석 서비스의 위치** - 최상의 성능을 위해 Data Lake Analytics 또는 HDInsight와 같은 분석 서비스를 데이터와 동일한 지역에 함께 배치해야 합니다.  

## <a name="see-also"></a>참고 항목
* [Azure 데이터 레이크 저장소 개요](data-lake-store-overview.md)

