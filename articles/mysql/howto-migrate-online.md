---
title: Azure Database for MySQL로 최소 가동 중지 시간 마이그레이션
description: 이 문서에서는 Azure Database Migration Service를 사용하여 Azure Database for MySQL로 MySQL 데이터베이스의 최소 가동 중지 시간 마이그레이션을 수행하는 방법을 설명합니다.
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: cb2e1a34748875de9c7f8dd59be27c408ec82818
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090334"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Azure Database for MySQL로 최소 가동 중지 시간 마이그레이션
새로 도입된 [Azure Database Migration Service](https://aka.ms/get-dms)(DMS)에 대한 **지속적인 동기화 기능**을 사용하여 최소 가동 중지 시간으로 Azure Database for MySQL로 MySQL 마이그레이션을 수행할 수 있습니다. 이 기능은 응용 프로그램에서 발생하는 가동 중지 시간을 제한합니다.

## <a name="overview"></a>개요
DMS는 Azure Database for MySQL에 대한 온-프레미스 초기 로드를 수행한 다음, 응용 프로그램이 실행되는 동안 계속해서 모든 새 트랜잭션을 Azure와 동기화합니다. 데이터가 Azure 쪽 대상을 처리한 후 잠시(최소 가동 중지 시간)동안 응용 프로그램을 중지하고, 대상을 처리하기 위해 데이터의 마지막 일괄 처리(응용 프로그램을 중지한 때부터 응용 프로그램이 모든 새 트래픽을 효과적으로 사용할 수 없을 때까지)를 기다린 다음, Azure를 가리키도록 연결 문자열을 업데이트합니다. 완료하면 응용 프로그램이 Azure에 게시됩니다!

![Azure Database Migration Service와 지속적인 동기화](./media/howto-migrate-online/ContinuousSync.png)

- MySQL 원본의 DMS 마이그레이션은 현재 공개 미리 보기로 제공됩니다. 서비스를 사용해 MySQL 워크로드를 마이그레이션해 보려면 포털로 이동하세요. 사용자 피드백은 서비스를 개선하는 데 더 없이 유용합니다.

## <a name="next-steps"></a>다음 단계
- Azure Database for MySQL에 MySQL 앱을 마이그레이션하는 방법을 보여주는 데모가 포함된 [MySQL/PostgreSQL 앱을 Azure 관리 서비스에 간편하게 마이그레이션하기](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201) 비디오를 시청합니다.
- [DMS를 사용하여 Azure Database for MySQL로 온라인 MySQL 마이그레이션](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online) 자습서를 참조하세요.
