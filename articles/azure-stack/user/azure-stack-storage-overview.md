---
title: Azure Stack 저장소 소개
description: Azure Stack 저장소에 대해 알아보기
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d31066e52e8c8f7856cce5651ae7e491029f328b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57782465"
---
# <a name="introduction-to-azure-stack-storage"></a>Azure Stack 저장소 소개

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

## <a name="overview"></a>개요

Azure Stack 저장소는 Blob, 테이블 및 Azure Storage 서비스와 일치 하는 큐를 포함 하는 클라우드 저장소 서비스의 집합.

## <a name="azure-stack-storage-services"></a>Azure Stack 저장소 서비스

Azure Stack 저장소는 다음 세 가지 서비스를 제공합니다.

- **Blob Storage**

    Blob storage는 구조화 되지 않은 개체 데이터를 저장합니다. Blob은 문서, 미디어 파일 또는 애플리케이션 설치 프로그램 등 모든 종류의 텍스트 또는 이진 데이터일 수 있습니다.

- **Table Storage**

    테이블 저장소는 구조화된 데이터 세트를 저장합니다. 테이블 저장소는 신속한 개발과 대량 데이터에 대한 빠른 액세스를 가능하게 하는 NoSQL 키-특성 데이터 저장소입니다.

- **Queue Storage**

    큐 저장소는 워크플로 처리 및 클라우드 서비스 구성 요소 사이의 통신을 위한 안정적인 메시징을 제공합니다.

Azure Stack 저장소 계정은 Azure Stack 저장소에서 서비스에 대 한 액세스를 제공 하는 보안 계정입니다. 저장소 계정은 저장소 리소스에 고유한 네임스페이스를 제공합니다. 다음 다이어그램은 저장소 계정에 Azure Stack 저장소 리소스 간의 관계를 보여줍니다.

![Azure Stack 저장소 개요](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Blob 저장소

클라우드에 저장 하는 많은 양의 구조화 되지 않은 개체 데이터를 사용 하 여 사용자에 대 한 blob 저장소는 효과적이 고 확장 가능한 솔루션을 제공 합니다. 와 같은 콘텐츠를 저장할 blob 저장소를 사용할 수 있습니다.

- 문서
- 사진, 비디오, 음악, 블로그 등의 소셜 데이터
- 파일, 컴퓨터, 데이터베이스 및 디바이스의 Backup
- 웹 애플리케이션의 이미지 및 텍스트
- 클라우드 애플리케이션의 구성 데이터
- 로그 및 기타 대규모 데이터 세트와 같은 빅 데이터

모든 Blob은 컨테이너로 구성됩니다. 컨테이너를 통해 유용하게 개체 그룹에 보안 정책을 할당할 수도 있습니다. 저장소 계정 컨테이너를 개수에 관계 없이 포함할 수 있습니다 하 고 컨테이너에 blob storage 계정 제한까지 개수에 관계 없이 포함 될 수 있습니다.

Blob storage는 세 가지 유형의 blob 제공합니다.

- **블록 blob**

    블록 blob는 클라우드 개체를 저장 및 스트리밍에 대 한 액세스에 최적화 된 및 문서, 미디어 파일, 백업 등을 저장 하는 데 적합 합니다.

- **추가 blob**

    추가 Blob은 블록 Blob과 유사하지만 추가 작업에 최적화되어 있습니다. 새 블록을 끝에 추가해야만 추가 Blob을 업데이트할 수 있습니다. 추가 Blob은 Blob 끝에만 새 데이터를 써야 하는 로깅과 같은 시나리오에 적합합니다.

- **페이지 blob**

    페이지 blob은 IaaS 디스크를 나타내기 위해 최적화 되어 쓰고 임의 지 원하는 최대 1TB 크기입니다. Azure Stack virtual machine을 IaaS 디스크는 페이지 blob으로 저장 된 VHD를 연결 합니다.

### <a name="table-storage"></a>테이블 저장소

최신 애플리케이션은 이전 세대 소프트웨어가 요구하는 것보다 확장성과 유연성이 더 높은 데이터 저장소를 요구하는 경우가 많습니다. 테이블 스토리지는 가용성이 높고 확장성이 큰 스토리지를 제공하므로, 애플리케이션이 사용자 요구에 맞게 자동으로 확장할 수 있습니다. Table storage는 Microsoft의 NoSQL 키/특성 저장소-전통적인 관계형 데이터베이스와 차이가, 스키마 없이 디자인 합니다. 스키마 없는 데이터 저장소 덕분에 애플리케이션의 요구 사항이 변화함에 따라 데이터를 쉽게 적응시킬 수 있습니다. 테이블 저장소는 쉽게 사용할 수 있어, 개발자가 신속하게 애플리케이션을 만들 수 있습니다.

테이블 저장소는 키-특성 저장소, 즉, 테이블의 모든 값 형식의 속성 이름으로 저장 됩니다. 이 속성 이름은 선택 조건을 필터링하고 지정하는 데 사용할 수 있습니다. 속성 모음과 해당 값은 함께 엔터티를 구성합니다. 테이블 저장소는 스키마 이므로 두 엔터티가 같은 테이블의 여러 컬렉션 속성에 포함 될 수 있습니다 하 고 이러한 속성은 다른 유형일 수 있습니다.

웹 응용 프로그램, 주소록, 장치 정보 및 다른 유형의 서비스에 필요한 메타 데이터에 대 한 사용자 데이터와 같은 유연한 데이터 집합을 저장할 테이블 저장소를 사용할 수 있습니다. 오늘날의 인터넷 기반 응용 프로그램에 대 한 테이블 저장소와 같은 NoSQL 데이터베이스는 전통적인 관계형 데이터베이스 대신 널리 사용을 제공합니다.

저장소 계정에는 임의 개수의 테이블을 포함할 수 있습니다 및 테이블 엔터티를 저장소 계정의 용량 제한까지 개수에 관계 없이 포함할 수 있습니다.

### <a name="queue-storage"></a>큐 저장소

규모를 고려하여 애플리케이션을 디자인할 때는 애플리케이션 구성 요소를 개별적으로 확장할 수 있도록 각 구성 요소를 분리하는 경우가 많습니다. Queue storage는 클라우드, 데스크톱, 온-프레미스 서버를 또는 모바일 장치에서에서 실행 중인 응용 프로그램 구성 요소 간의 비동기 통신을 위한 안정적인 메시징 솔루션을 제공 합니다. Queue storage는 또한 비동기 작업 관리와 프로세스 워크플로 작성을 지원합니다.

저장소 계정은 큐 개수에 관계 없이 포함 될 수 있습니다 하 고 큐에 메시지를 저장소 계정의 용량 제한까지 개수에 관계 없이 포함 될 수 있습니다. 개별 메시지는 크기가 최대 64KB일 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [일관 된 azure storage: 차이점 및 고려 사항](azure-stack-acs-differences.md)

- Azure Storage에 대 한 자세한 내용은를 참조 하세요. [Microsoft Azure Storage 소개](../../storage/common/storage-introduction.md)
