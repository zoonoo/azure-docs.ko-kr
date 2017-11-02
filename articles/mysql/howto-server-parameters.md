---
title: "MySQL용 Azure Database에서 서버 매개 변수 구성 방법 | Microsoft Docs"
description: "이 문서에서는 Azure Portal을 사용하여 Azure Database for MySQL에서 사용 가능한 서버 매개 변수를 구성하는 방법을 설명합니다."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/10/2017
ms.openlocfilehash: 06c7f9f6bd49ebfaf03b04cb6e30b963593bfb35
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2017
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MySQL에서 서버 매개 변수 구성 방법

MySQL용 Azure Database는 일부 서버 매개 변수 구성을 지원합니다. 이 항목에서는 Azure Portal을 사용하여 이러한 매개 변수를 구성하는 방법을 설명합니다. 일부 서버 매개 변수를 조정할 수 있습니다. 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Azure Portal의 서버 매개 변수로 이동
1. Azure Portal에 로그인한 다음 Azure Database for MySQL 서버를 찾습니다.
2. **설정** 섹션에서 **서버 매개 변수**를 클릭하여 Azure Database for MySQL에 대한 서버 매개 변수 페이지를 엽니다.
3. 조정해야 하는 설정을 모두 찾습니다. **설명** 열을 검토하여 용도 및 허용되는 값을 이해합니다. 
4. **저장**을 클릭하여 변경 내용을 저장합니다.

![Azure Portal 서버 매개 변수 블레이드](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>구성 가능한 서버 매개 변수 목록

지원되는 서버 매개 변수 목록은 계속 확장됩니다. Azure Portal의 서버 매개 변수 탭을 사용하여 정의를 가져오고 응용 프로그램 요구 사항에 따라 서버 매개 변수를 구성합니다. 

## <a name="nonconfigurable-server-parameters"></a>구성 불가능한 서버 매개 변수

다음 매개 변수는 구성이 가능하지 않으며 [가격 책정 계층](concepts-service-tiers.md)과 연결되어 있습니다. 

| **가격 책정 계층** | **InnoDB 버퍼 풀(MB)** | **최대 연결** |
| :------------------------ | :-------- | :----------- |
| 기본 50 | 1024 | 50 | 
| 기본 100  | 2560 | 100 | 
| 표준 100 | 2560 | 200 | 
| 표준 200 | 5120 | 400 | 
| 표준 400 | 10240 | 800 | 
| 표준 800 | 20480 | 1600 |

버전 [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) 및 [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html)에 대한 기타 서버 매개 변수 기본값

## <a name="next-steps"></a>다음 단계
- [Azure Database for MySQL에 대한 연결 라이브러리](concepts-connection-libraries.md).
