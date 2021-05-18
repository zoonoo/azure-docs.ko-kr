---
title: TLS 구성 - Azure Portal - Azure Database for PostgreSQL - Single 서버
description: Azure Database for PostgreSQL Single 서버에 Azure Portal을 사용하여 TLS 구성을 설정하는 방법을 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 25be6b3c4e3172fc8ee14b97fd890b5948c284ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93242367"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql-single---server-using-azure-portal"></a>Azure Portal을 사용하여 Azure Database for PostgreSQL Single - 서버에서 TLS 설정 구성

이 문서에서는 연결에 허용된 최소 TLS 버전을 적용하고 적용된 최소 TLS 버전보다 더 낮은 TLS 버전의 모든 연결을 거부하여 네트워크 보안을 강화하도록 Azure Database for PostgreSQL을 구성하는 방법을 설명합니다.

Azure Database for PostgreSQL에 연결하는 데 TLS 버전을 적용할 수 있습니다. 이제 고객은 데이터베이스 서버에 최소 TLS 버전을 설정할 수 있습니다. 예를 들어 최소 TLS 설정 버전을 TLS 1.0으로 설정하면 서버에서 TLS 1.0, 1.1 및 1.2 이상을 사용하는 클라이언트의 연결을 허용한다는 의미입니다. 대신, 최소 TLS 버전을 1.2 이상으로 설정하면 TLS 1.2을 사용하는 클라이언트로부터의 연결만 허용하고 TLS 1.0 및 TLS 1.1을 사용하는 모든 연결은 거부됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 완료하려면 다음이 필요합니다.

* [Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single 서버에 TLS 구성 설정

PostgreSQL 최소 TLS 버전을 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for PostgreSQL을 선택합니다.

1.  Azure Database for PostgreSQL - Single 서버 페이지의 **설정** 에서 **연결 보안** 을 클릭하여 연결 보안 구성 페이지를 엽니다.

1. **최소 TLS 버전** 에서 PostgreSQL Single 서버용 TLS 1.2보다 낮은 TLS 버전을 이용한 연결을 거부하려면 **1.2** 를 선택합니다.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="Azure Database for PostgreSQL Single - 서버에 TLS 구성":::

1. **저장** 을 클릭하여 변경 내용을 저장합니다.

1. 연결 보안 설정이 성공적으로 활성화되었는지 확인하는 알림이 나타납니다.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Azure Database for PostgreSQL - Single 서버 TLS 구성 성공":::

## <a name="next-steps"></a>다음 단계

[메트릭에 대한 경고를 만드는 방법](howto-alert-on-metric.md)에 대해 알아봅니다.