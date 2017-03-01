---
title: "위협 감지 - Azure SQL Database | Microsoft Docs"
description: "위협 감지는 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 감지합니다."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: v-romcal
ms.assetid: b50d232a-4225-46ed-91e7-75288f55ee84
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 07/10/2016
ms.author: ronmat; ronitr
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: eadaa3e7a279b6b92da1d0c026c3002297dfd298
ms.lasthandoff: 02/17/2017


---
# <a name="sql-database-threat-detection"></a>SQL Database 위협 감지

위협 감지는 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 감지합니다.  위협 감지는 현재 미리 보기가 제공됩니다.

## <a name="overview"></a>개요

위협 감지는 비정상적인 활동에 대한 보안 경고를 제공하여 잠재적인 위협이 발생하면 고객이 이를 감지하고 대응할 수 있도록 하는 새로운 차원의 보안을 제공합니다.  사용자는 데이터베이스의 데이터를 액세스, 침범 또는 악용하려는 시도로 인해 의심스러운 이벤트가 발생했는지를 판단하기 위해서 [SQL Database 감사](sql-database-auditing.md)를 사용하여 의심스러운 이벤트를 살펴볼 수 있습니다.
위협 감지는 보안 전문가가 되거나 고급 보안 모니터링 시스템을 관리할 필요 없이 데이터베이스에 대한 잠재적인 위협에 간단하게 대처할 수 있도록 합니다.

예를 들어 위협 감지는 잠재적인 SQL 삽입 시도를 나타내는 비정상적인 데이터베이스 활동을 감지합니다. SQL 삽입은 데이터 기반 응용 프로그램 공격에 사용되는 인터넷 상의 일반적인 웹 응용 프로그램 보안 문제 중 하나입니다. 공격자는 데이터베이스의 데이터를 침범하거나 수정하기 위해 응용 프로그램의 취약성을 이용하여 악의적인 SQL 문을 응용 프로그램 항목 필드에 삽입합니다.

## <a name="next-steps"></a>다음 단계

* 위협 감지를 구성하고 관리하려면 [Azure Portal에서 위협 감지 구성 및 관리](sql-database-threat-detection-portal.md)를 참조하세요.

