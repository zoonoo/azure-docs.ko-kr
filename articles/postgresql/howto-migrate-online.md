---
title: Azure database for PostgreSQL-단일 서버 최소 가동 중지 시간 마이그레이션
description: 이 문서에서는 PostgreSQL-Azure Database Migration Service를 사용 하 여 단일 서버에 대 한 Azure Database에 PostgreSQL 데이터베이스의 최소 가동 중지 시간 마이그레이션을 수행 하는 방법을 설명 합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 93cd390889c023adf1c30a8470e1c2298598439e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "65067523"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Azure database for PostgreSQL-단일 서버 최소 가동 중지 시간 마이그레이션
새로 도입된 [Azure Database Migration Service](https://aka.ms/get-dms)(DMS)에 대한 **지속적인 동기화 기능**을 사용하여 최소 가동 중지 시간으로 Azure Database for PostgreSQL로 PostgreSQL 마이그레이션을 수행할 수 있습니다. 이 기능은 애플리케이션에서 발생하는 가동 중지 시간을 제한합니다.

## <a name="overview"></a>개요
Azure DMS는 Azure Database for PostgreSQL에 대한 온-프레미스 초기 로드를 수행한 다음, 애플리케이션이 실행되는 동안 계속해서 모든 새 트랜잭션을 Azure와 동기화합니다. 데이터가 Azure 쪽 대상을 처리한 후 잠시(최소 가동 중지 시간) 동안 애플리케이션을 중지하고, 대상을 처리하기 위해 데이터의 마지막 일괄 처리(애플리케이션을 중지한 때부터 애플리케이션이 모든 새 트래픽을 효과적으로 사용할 수 없을 때까지)를 기다린 다음, Azure를 가리키도록 연결 문자열을 업데이트합니다. 완료하면 애플리케이션이 Azure에 게시됩니다!

![Azure Database Migration Service와 지속적인 동기화](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>다음 단계
- PostgreSQL 앱을 Azure Database for PostgreSQL로 마이그레이션하는 방법을 보여주는 데모 버전이 포함된 [Microsoft Azure를 사용한 앱 현대화](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102) 비디오를 봅니다.
- [DMS를 사용하여 PostgreSQL을 Azure Database for PostgreSQL로 온라인 마이그레이션](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) 자습서를 참조하세요.
