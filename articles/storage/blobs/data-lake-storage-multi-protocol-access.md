---
title: Azure Data Lake Storage에서 멀티 프로토콜 액세스 Microsoft Docs
description: Azure Data Lake Storage Gen2에서 Blob Api를 사용 하는 응용 프로그램 및 Blob Api를 사용 합니다.
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: normesta
ms.openlocfilehash: f384fb738fe719b8e622e8d61502e6acba2bbf31
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314356"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Azure Data Lake Storage에 대 한 다중 프로토콜 액세스

Blob Api는 이제 계층 구조가 있는 계정으로 작동 합니다. 이는 모든 Blob 저장소 기능 뿐만 아니라 도구, 응용 프로그램 및 서비스의 전체 에코 시스템을 계층 구조 네임 스페이스를 갖는 계정으로 잠금 해제 합니다.

최근 까지는 개체 저장소와 분석 저장소에 대 한 별도의 저장소 솔루션을 유지 해야 했을 수 있습니다. Azure Data Lake Storage Gen2는 에코 시스템 지원이 제한 되어 있기 때문입니다. 진단 로깅과 같은 Blob service 기능에 대 한 제한 된 액세스 권한도 있었습니다. 조각화 된 저장소 솔루션은 다양 한 시나리오를 수행 하기 위해 계정 간에 데이터를 이동 해야 하기 때문에 유지 관리가 어렵습니다. 더 이상이 작업을 수행할 필요가 없습니다.

> [!NOTE]
> Data Lake Storage에 대 한 멀티 프로토콜 액세스는 공개 미리 보기 상태 이며 **미국 서 부 2** 및 **미국 서 부** 지역 에서만 사용할 수 있습니다. 제한 사항을 검토 하려면 [알려진 문제](data-lake-storage-known-issues.md) 문서를 참조 하세요. 미리 보기에 등록 하려면 [이 페이지](https://aka.ms/blobinteropsignup)를 참조 하세요.

## <a name="use-the-entire-ecosystem-of-applications-tools-and-services"></a>응용 프로그램, 도구 및 서비스의 전체 에코 시스템 사용

Data Lake Storage에 대 한 다중 프로토콜 액세스 미리 보기에 등록 하는 경우 도구, 응용 프로그램 및 서비스의 전체 에코 시스템을 사용 하 여 모든 데이터를 사용할 수 있습니다. 여기에는 [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction), [IOT Hub](https://docs.microsoft.com/azure/iot-hub/), [Power BI](https://docs.microsoft.com/power-bi/desktop-data-sources)등의 Azure 서비스가 포함 됩니다. 

또한 타사 도구 및 응용 프로그램도 포함 됩니다. 계층 구조 네임 스페이스를 수정할 필요 없이 계정으로 지정할 수 있습니다. Blob Api는 이제 계층 네임 스페이스가 있는 계정의 데이터에서 작동할 수 있으므로 이러한 응용 프로그램은 Blob Api를 호출 하는 경우에도 *그대로* 작동 합니다.

> [!NOTE]
> 제한 사항을 검토 하려면 [알려진 문제](data-lake-storage-known-issues.md) 문서를 참조 하세요.

## <a name="use-all-blob-storage-features"></a>모든 Blob storage 기능 사용

[진단 로깅](../common/storage-analytics-logging.md), [액세스 계층](storage-blob-storage-tiers.md)및 [blob 저장소 수명 주기 관리 정책과](storage-lifecycle-management-concepts.md) 같은 blob storage 기능은 이제 계층 구조가 있는 계정으로 작동 합니다. 따라서 이러한 중요 한 기능에 대 한 액세스를 끊어질 하지 않고 blob storage 계정에서 계층적 네임 스페이스를 사용 하도록 설정할 수 있습니다. 

> [!NOTE]
> 제한 사항을 검토 하려면 [알려진 문제](data-lake-storage-known-issues.md) 문서를 참조 하세요.

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Data lake storage의 멀티 프로토콜 액세스 작동 방식

Blob Api 및 Data Lake Storage Gen2 Api는 계층적 네임 스페이스를 포함 하는 저장소 계정의 동일한 데이터에서 작동할 수 있습니다. Data Lake Storage Gen2는 계층 구조 네임 스페이스를 통해 Blob Api를 라우팅합니다. 따라서 첫 번째 클래스 디렉터리 작업 및 POSIX 규격 Acl (access control 목록)의 이점을 얻을 수 있습니다. 

![Data Lake Storage 개념에 대 한 다중 프로토콜 액세스](./media/data-lake-storage-interop/interop-concept.png) 

기존 도구와 Blob API를 사용 하는 응용 프로그램은 이러한 혜택을 자동으로 얻습니다. 개발자는이를 수정할 필요가 없습니다. Data Lake Storage Gen2는 도구 및 응용 프로그램에서 데이터에 액세스 하는 데 사용 하는 프로토콜에 관계 없이 디렉터리 및 파일 수준 Acl을 일관 되 게 적용 합니다.   

## <a name="next-steps"></a>다음 단계

[알려진 문제](data-lake-storage-known-issues.md) 를 참조 하세요.




