---
title: TLS 구성-Azure Portal Azure Database for PostgreSQL 단일 서버
description: Azure Database for PostgreSQL 단일 서버에 대 한 Azure Portal를 사용 하 여 TLS 구성을 설정 하는 방법을 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 049b30689c86881ce89d4fbcd54175ee1ee5497e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375110"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql---single-server-using-azure-portal"></a>Azure Database for PostgreSQL에서 Azure Portal를 사용 하 여 단일 서버에 TLS 설정 구성

이 문서에서는 들어오는 모든 연결에 대 한 최소 TLS 버전을 적용 하도록 Azure Database for PostgreSQL 단일 서버를 구성 하 여 네트워크 보안을 개선 하는 방법을 설명 합니다.

이제 고객은 Azure Database for PostgreSQL 단일 서버에 연결 하기 위해 TLS 버전을 적용할 수 있습니다. 이제 고객은 데이터베이스 서버에 대 한 최소 TLS 버전을 설정 하도록 선택할 수 있습니다. 예를 들어 최소 TLS 설정 버전을 TLS 1.0로 설정 하면 서버에서 TLS 1.0, 1.1 및 1.2 +를 사용 하는 클라이언트의 연결을 허용 한다는 의미입니다. 또는이를 1.2으로 설정 하면 TLS 1.2을 사용 하는 클라이언트의 연결만 허용 하 고 TLS 1.0 및 TLS 1.1을 사용한 모든 연결이 거부 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 완료하려면 다음이 필요합니다.

* [Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL-단일 서버에 대 한 TLS 구성 설정

PostgreSQL 단일 서버 최소 TLS 버전을 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for PostgreSQL 단일 서버를 선택 합니다.

1.  Azure Database for PostgreSQL-단일 서버 페이지의 **설정**에서 **연결 보안** 을 클릭 하 여 연결 보안 구성 페이지를 엽니다.

1. **최소 tls 버전**에서 **1.2** 을 선택 하 여 PostgreSQL 단일 서버에 대해 TLS 1.2 보다 작은 tls 버전의 연결을 거부 합니다.

    ![단일 서버 TLS 구성 Azure Database for PostgreSQL](./media/howto-tls-configurations/setting-tls-value.png)

1. **저장**을 클릭하여 변경 내용을 저장합니다.

1. 연결 보안 설정이 성공적으로 설정 되었는지 확인 하는 알림이 나타납니다.

    ![Azure Database for PostgreSQL-단일 서버 TLS 구성 성공](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>다음 단계

[메트릭에 대 한 경고를 만드는 방법](howto-alert-on-metric.md)에 대해 알아봅니다.
