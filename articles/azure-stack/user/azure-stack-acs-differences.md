---
title: "Azure 스택 저장소: 차이점과 고려 사항"
description: "Azure 스택 배포 고려 사항 함께 Azure 스택 저장소 및 Azure 저장소 간의 차이점을 이해 합니다."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
ms.reviwer: xiaofmao
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/08/2017
ms.author: jeffgilb
ms.openlocfilehash: 1dc099fa234e217b682c88f2214fe271c916eec2
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2017
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure 스택 저장소: 차이점과 고려 사항

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 저장소는 Microsoft Azure 스택에서 저장소 클라우드 서비스의 집합입니다. Azure 스택 저장소는 blob, 테이블, 큐 및 Azure에 일관 된 의미 체계를 포함 하는 계정 관리 기능을 제공 합니다.

이 문서는 Azure 저장소에서 알려진된 Azure 스택 저장소 차이점을 요약 합니다. 또한 Azure 스택을 배포할 때 유의 해야 할 기타 고려 사항을 요약 되어 있습니다. Azure 스택와 Azure 간의 대략적인 차이 대 한 자세한 내용은 [고려 사항 키](azure-stack-considerations.md) 항목입니다.

## <a name="cheat-sheet-storage-differences"></a>치트 시트: 저장소의 차이

| 기능 | Azure (global) | Azure Stack |
| --- | --- | --- |
|File Storage|지원 클라우드 기반 SMB 파일 공유|아직 지원 되지 않음
|휴지 상태의 데이터에 대한 Azure Storage 서비스 암호화|256 비트 AES 암호화|아직 지원 되지 않음
|Storage 계정 유형|범용 및 Azure Blob 저장소 계정|범용만
|복제 옵션|로컬 중복 저장소, 지역 중복 저장소, 읽기 액세스 지역 중복 저장소 및 영역 중복 저장소|로컬 중복 저장소
|Premium Storage|완전하게 지원|성능 제한이 있지만 제공 될 수 있습니다 또는 보장
|관리 디스크|지 원하는 표준 및 프리미엄|아직 지원 되지 않음
|Blob 이름|1, 024 자 (2, 048 바이트)|880 문자 (1,760 바이트)
|블록 blob의 최대 크기|4.75 TB (100 MB X 50, 000 블록)|50,000 X 4MB(약 195GB)
|페이지 blob 스냅숏 복사본|지원 되는 실행 중인 VM에 연결 된 백업 Azure 관리 되지 않는 VM 디스크|아직 지원 되지 않음
|페이지 blob 증분 스냅숏 복사본|Premium 및 지원 되는 표준 Azure 페이지 blob|아직 지원 되지 않음
|페이지 blob의 최대 크기|8 TB|1TB
|페이지 blob 페이지 크기|512바이트|4KB
|테이블 파티션 키와 행 키 크기|1, 024 자 (2, 048 바이트)|400 문자 (800 바이트)

### <a name="metrics"></a>메트릭
저장소 메트릭 사용 하 여 몇 가지 차이점이 있습니다.
* 저장소 메트릭의 트랜잭션 데이터를 내부 또는 외부 네트워크 대역폭 달라 지지 않습니다.
* 저장소 메트릭에서 트랜잭션 데이터는 탑재 된 디스크에 대 한 가상 컴퓨터 액세스를 포함 되지 않습니다.

## <a name="api-version"></a>API 버전
Azure 스택 저장소와 다음과 같은 버전이 지원 됩니다.

* Azure 저장소 데이터 서비스: [REST API 버전 2015-04-05](https://docs.microsoft.com/rest/api/storageservices/Version-2015-04-05?redirectedfrom=MSDN)
* Azure 저장소 관리 서비스: [2015-05-01-미리 보기, 2015-06-15 및 2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN) 

## <a name="next-steps"></a>다음 단계

* [Azure 스택 저장소 개발 도구 시작](azure-stack-storage-dev.md)
* [Azure 스택 저장소 소개](azure-stack-storage-overview.md)

