---
title: Azure Cosmos Emulator 다운로드 및 릴리스 정보
description: Azure Cosmos Emulator 릴리스 정보를 확인하고 다운로드합니다.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 5985d0d82341c76993ee91b8dff6927edd1ed8b4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332144"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>로컬 개발 및 테스트에 Azure Cosmos Emulator 사용

이 문서에서는 Azure Cosmos Emulator 릴리스 정보와 각 릴리스의 기능 업데이트 목록을 알아봅니다. 또한 다운로드 및 사용할 수 있는 에뮬레이터의 최신 버전도 나와 있습니다.

## <a name="download"></a>다운로드

| | |
|---------|---------|
|**MSI 다운로드**|[Microsoft 다운로드 센터](https://aka.ms/cosmosdb-emulator)|
|**시작**|[Azure Cosmos 에뮬레이터로 로컬에서 개발](local-emulator.md)|

## <a name="release-notes"></a>릴리스 정보

### <a name="243"></a>2.4.3

- 기본적으로 MongoDB 서비스 시작은 사용하지 않도록 설정되어 있습니다. SQL 엔드포인트만 기본값으로 사용됩니다. 사용자는 에뮬레이터의 "/EnableMongoDbEndpoint" 명령줄 옵션을 사용하여 수동으로 엔드포인트를 시작해야 합니다. 이제 Gremlin, Cassandra 및 Table 등 모든 기타 서비스 엔드포인트와 마찬가지입니다.
- “/AllowNetworkAccess”를 시작할 때 Gremlin, Cassandra 및 Table 엔드포인트가 외부 클라이언트의 요청을 제대로 처리하지 않는 에뮬레이터 버그를 수정했습니다.
- 방화벽 규칙 설정에 직접 연결 포트를 추가합니다.

### <a name="240"></a>2.4.0

- Pulse 클라이언트와 같은 네트워크 모니터링 앱이 호스트 컴퓨터에 있는 경우 시작하지 못하는 에뮬레이터 문제를 해결했습니다.
