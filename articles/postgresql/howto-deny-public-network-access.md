---
title: 퍼블릭 네트워크 액세스 거부 - Azure Portal Azure Database for PostgreSQL - Single 서버
description: Azure Database for PostgreSQL Single 서버에 대해 Azure Portal을 사용하여 퍼블릭 네트워크 액세스 거부를 구성하는 방법을 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: e195c005676df27385e5e00736b04bdb689fafc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727110"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Azure Portal을 사용하여 Azure Database for PostgreSQL Single 서버에서 퍼블릭 네트워크 액세스 거부

이 문서에서는 모든 퍼블릭 구성을 거부하도록 Azure Database for PostgreSQL Single 서버를 구성하고 프라이빗 엔드포인트를 통한 연결만을 허용하여 네트워크 보안을 강화하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

* 범용 또는 메모리 최적화 가격 책정 계층을 사용하는 [Azure Database for PostgreSQL Single 서버](quickstart-create-server-database-portal.md)

## <a name="set-deny-public-network-access"></a>퍼블릭 네트워크 액세스 거부 설정

PostgreSQL Single 서버 퍼블릭 네트워크 액세스 거부를 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for PostgreSQL Single 서버를 선택합니다.

1. PostgreSQL Single 서버 페이지의 **설정** 에서 **연결 보안** 을 클릭하여 연결 보안 구성 페이지를 엽니다.

1. **퍼블릭 네트워크 액세스 거부** 에서 **예** 를 선택하여 PostgreSQL Single 서버에 대한 퍼블릭 액세스 거부를 활성화합니다.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access.PNG" alt-text="Azure Database for PostgreSQL Single 서버 네트워크 액세스 거부":::

1. **저장** 을 클릭하여 변경 내용을 저장합니다.

1. 연결 보안 설정이 성공적으로 활성화되었는지 확인하는 알림이 나타납니다.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access-success.png" alt-text="Azure Database for PostgreSQL Single 서버 네트워크 액세스 거부 성공":::

## <a name="next-steps"></a>다음 단계

[메트릭에 대한 경고를 만드는 방법](howto-alert-on-metric.md)에 대해 알아봅니다.
