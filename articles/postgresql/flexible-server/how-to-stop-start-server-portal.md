---
title: 중지/시작-Azure Portal-유연한 서버 Azure Database for PostgreSQL
description: 이 문서에서는 Azure Portal를 통해 Azure Database for PostgreSQL에서 작업을 중지/시작 하는 방법을 설명 합니다.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 4c393e0048a0058ebe0fbf2b0ee65f6ae2e184c5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938854"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview"></a>Azure Database for PostgreSQL 유연한 서버 중지/시작 (미리 보기)

> [!IMPORTANT]
> Azure Database for PostgreSQL-유연한 서버는 현재 미리 보기 상태입니다.

이 문서에서는 유연한 서버를 중지 하 고 시작 하는 단계별 지침을 제공 합니다.

## <a name="pre-requisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

-   Azure Database for PostgreSQL 유연한 서버가 있어야 합니다.

## <a name="stop-a-running-server"></a>실행 중인 서버 중지

1.  [Azure Portal](https://portal.azure.com/)에서 중지 하려는 유연한 서버를 선택 합니다.

2.  **개요** 페이지의 도구 모음에서 **중지** 단추를 클릭 합니다.

> [!NOTE]
> 서버를 중지 한 후에는 유연한 서버에서 다른 관리 작업을 사용할 수 없습니다.

중지 된 서버는 7 일 후에 자동으로 다시 시작 됩니다. 보류 중인 유지 관리 업데이트는 다음에 서버를 시작할 때 적용 됩니다.

## <a name="start-a-stopped-server"></a>중지 된 서버 시작

1.  [Azure Portal](https://portal.azure.com/)에서 시작 하려는 유연한 서버를 선택 합니다.

2.  **개요** 페이지의 도구 모음에서 **시작** 단추를 클릭 합니다.

> [!NOTE]
> 서버를 시작한 후에는 이제 유연한 서버에서 모든 관리 작업을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Database for PostgreSQL 유연한 서버에서 계산 및 저장소 옵션](./concepts-compute-storage.md)에 대해 자세히 알아보세요.
