---
title: Azure 데이터 레이크 스토리지에 대한 다중 프로토콜 액세스 | 마이크로 소프트 문서
description: Azure 데이터 레이크 저장소 Gen2에서 Blob API를 사용하는 Blob API 및 응용 프로그램을 사용합니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914861"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Azure Data Lake Storage에 대한 다중 프로토콜 액세스

Blob API는 이제 계층적 네임스페이스가 있는 계정으로 작동합니다. 이렇게 하면 계층적 네임스페이스가 있는 계정에 도구, 응용 프로그램 및 서비스의 에코시스템뿐만 아니라 여러 Blob 저장소 기능이 잠금 해제됩니다.

최근까지는 개체 저장소 및 분석 저장소를 위한 별도의 저장소 솔루션을 유지 관리해야 했을 수 있습니다. Azure 데이터 레이크 스토리지 Gen2에 대한 에코시스템 지원이 제한적이기 때문입니다. 또한 진단 로깅과 같은 Blob 서비스 기능에 대한 액세스가 제한되었습니다. 조각난 저장소 솔루션은 다양한 시나리오를 수행하기 위해 계정 간에 데이터를 이동해야 하기 때문에 유지 관리하기 가 어렵습니다. 더 이상 그렇게 할 필요가 없습니다.

Data Lake Storage에서 다중 프로토콜 액세스를 통해 도구, 응용 프로그램 및 서비스의 에코시스템을 사용하여 데이터로 작업할 수 있습니다. 여기에는 타사 도구 및 응용 프로그램도 포함됩니다. 계층적 네임스페이스가 있는 계정을 수정할 필요 없이 가리킬 수 있습니다. Blob API는 이제 계층적 네임스페이스가 있는 계정의 데이터에서 작동할 수 있으므로 이러한 응용 프로그램은 Blob API를 호출하는 경우에도 *작동합니다.*

[진단 로깅,](../common/storage-analytics-logging.md) [액세스 계층](storage-blob-storage-tiers.md)및 [Blob 저장소 수명 주기 관리 정책과](storage-lifecycle-management-concepts.md) 같은 Blob 저장소 기능은 이제 계층적 네임스페이스가 있는 계정에서 작동합니다. 따라서 이러한 중요한 기능에 대한 액세스 권한을 잃지 않고 Blob Storage 계정에 계층적 네임스페이스를 활성화할 수 있습니다. 

> [!NOTE]
> Data Lake Storage에 대한 다중 프로토콜 액세스는 일반적으로 사용할 수 있으며 모든 지역에서 사용할 수 있습니다. 다중 프로토콜 액세스로 활성화된 일부 Azure 서비스 또는 Blob 저장소 기능은 미리 보기상태로 유지됩니다.  이 문서에서는 Blob 저장소 기능 및 Azure 서비스 통합에 대한 현재 지원을 요약합니다. 
>
> [Azure 데이터 레이크 스토리지 Gen2에서 사용할 수 있는 Blob 저장소 기능](data-lake-storage-supported-blob-storage-features.md)
>
>[Azure 데이터 레이크 저장소 Gen2를 지원하는 Azure 서비스](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>데이터 레이크 스토리지에 대한 다중 프로토콜 액세스 작동 방식

Blob API 및 데이터 레이크 스토리지 Gen2 API는 계층적 네임스페이스가 있는 저장소 계정의 동일한 데이터에서 작동할 수 있습니다. Data Lake Storage Gen2는 계층적 네임스페이스를 통해 Blob API를 라우팅하여 일류 디렉터리 작업 및 POSIX 호환 액세스 제어 목록(ACL)의 이점을 얻을 수 있습니다. 

![데이터 레이크 스토리지 개념에 대한 다중 프로토콜 액세스](./media/data-lake-storage-interop/interop-concept.png) 

Blob API를 사용하는 기존 도구 및 응용 프로그램은 이러한 이점을 자동으로 얻습니다. 개발자는 수정할 필요가 없습니다. Data Lake Storage Gen2는 도구와 응용 프로그램이 데이터에 액세스하는 데 사용하는 프로토콜에 관계없이 디렉터리 및 파일 수준 ACL을 일관되게 적용합니다. 

## <a name="see-also"></a>참조

- [Azure 데이터 레이크 스토리지 Gen2에서 사용할 수 있는 Blob 저장소 기능](data-lake-storage-supported-blob-storage-features.md)
- [Azure 데이터 레이크 저장소 Gen2를 지원하는 Azure 서비스](data-lake-storage-supported-azure-services.md)
- [Azure 데이터 레이크 스토리지 Gen2를 지원하는 오픈 소스 플랫폼](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage Gen2에서 알려진 문제](data-lake-storage-known-issues.md)




