---
title: Azure Cosmos Emulator 다운로드 및 릴리스 정보
description: 다양한 버전에 대한 Azure Cosmos 에뮬레이터 릴리스 정보를 가져오고 정보를 다운로드합니다.
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: 4dffe169908d0dd3effa4e46140b5f6696805a3e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77168639"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos Emulator - 릴리스 정보 및 다운로드 정보

이 문서에서는 Azure Cosmos Emulator 릴리스 정보와 각 릴리스의 기능 업데이트 목록을 알아봅니다. 또한 다운로드 및 사용할 수 있는 에뮬레이터의 최신 버전도 나와 있습니다.

## <a name="download"></a>다운로드

| | |
|---------|---------|
|**MSI 다운로드**|[Microsoft 다운로드 센터](https://aka.ms/cosmosdb-emulator)|
|**시작**|[Azure Cosmos 에뮬레이터로 로컬에서 개발](local-emulator.md)|

## <a name="release-notes"></a>릴리스 정보

### <a name="291"></a>2.9.1

- 이 릴리스는 쿼리 API 지원의 몇 가지 문제를 해결하고 Windows Server 2012와 같은 이전 OS와의 호환성을 복원합니다.

### <a name="290"></a>2.9.0

- 이 릴리스에는 일관성을 일관된 접두사로 설정하고 사용자 및 사용 권한의 최대 제한을 늘리는 옵션이 추가되었습니다.

### <a name="272"></a>2.7.2

- 이 릴리스에는 MongoDB 버전 3.6 서버 지원이 Cosmos 에뮬레이터에 추가되었습니다. 3\.6 버전의 서비스를 대상으로 하는 MongoDB 엔드포인트를 시작하려면 "/EnableMongoDBEndpoint=3.6" 옵션을 사용하여 관리자 명령줄에서 에뮬레이터를 시작합니다.

### <a name="270"></a>2.7.0

- 이 릴리스에서는 .NET 코어 또는 x86 .NET 기반 클라이언트를 사용할 때 사용자가 에뮬레이터에서 SQL API 계정에 대해 쿼리를 실행할 수 없도록 하는 회귀를 수정합니다.

### <a name="246"></a>2.4.6

- 이 릴리스에서는 [Azure Cosmos 에뮬레이터로 로컬에서 개발](local-emulator.md)에 설명된 예외가 포함된 2019년 7월 현재 Azure Cosmos 서비스의 기능과 패리티를 제공합니다. 또한 직접 모드 연결을 사용하는 SDK 클라이언트에 대한 명령줄 및 내부 IP 주소 재정의를 통해 호출될 때 에뮬레이터 종료와 관련된 몇 가지 버그를 수정합니다.

### <a name="243"></a>2.4.3

- 기본적으로 MongoDB 서비스 시작은 사용하지 않도록 설정되어 있습니다. SQL 엔드포인트만 기본값으로 사용됩니다. 사용자는 에뮬레이터의 "/EnableMongoDbEndpoint" 명령줄 옵션을 사용하여 수동으로 엔드포인트를 시작해야 합니다. 이제 Gremlin, Cassandra 및 Table 등 모든 기타 서비스 엔드포인트와 마찬가지입니다.
- “/AllowNetworkAccess”를 시작할 때 Gremlin, Cassandra 및 Table 엔드포인트가 외부 클라이언트의 요청을 제대로 처리하지 않는 에뮬레이터 버그를 수정했습니다.
- 방화벽 규칙 설정에 직접 연결 포트를 추가합니다.

### <a name="240"></a>2.4.0

- Pulse 클라이언트와 같은 네트워크 모니터링 앱이 호스트 컴퓨터에 있는 경우 시작하지 못하는 에뮬레이터 문제를 해결했습니다.
