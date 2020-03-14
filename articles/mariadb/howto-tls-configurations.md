---
title: TLS 구성-Azure Portal-Azure Database for MariaDB
description: Azure Database for MariaDB에 대 한 Azure Portal를 사용 하 여 TLS 구성을 설정 하는 방법을 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 063fc82131a66012c7329f590c0fdf2ce4d4da79
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375006"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for MariaDB에서 TLS 설정 구성

이 문서에서는 최소 TLS 버전에 대 한 연결을 적용 하 고 낮은 TLS 버전을 사용 하 여 모든 연결을 거부 하 여 네트워크 보안을 향상 시키기 위해 Azure Database for MariaDB 서버를 구성 하는 방법을 설명 합니다.

이제 고객은 데이터베이스 서버에 대 한 최소 TLS 버전을 설정 하 여 Azure Database for MariaDB에 연결 하는 데 TLS 버전을 적용할 수 있습니다. 예를 들어 최소 TLS 설정 버전을 TLS 1.0로 설정 하면 서버에서 TLS 1.0, 1.1 및 1.2 +를 사용 하는 클라이언트의 연결을 허용 한다는 의미입니다. 또는이를 1.2으로 설정 하면 TLS 1.2을 사용 하는 클라이언트의 연결만 허용 하 고 TLS 1.0 및 TLS 1.1을 사용한 모든 연결이 거부 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 완료하려면 다음이 필요합니다.

* [Azure Database for MariaDB](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>Azure Database for MariaDB에 대 한 TLS 구성 설정

다음 단계를 수행 하 여 MariaDB 서버 최소 TLS 버전을 설정 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for MariaDB 서버를 선택 합니다.

1. MariaDB 서버 페이지의 **설정**에서 **연결 보안** 을 클릭 하 여 연결 보안 구성 페이지를 엽니다.

1. **최소 tls 버전**에서 **1.2** 를 선택 하 여 mariadb 서버에 대 한 TLS 1.2 보다 작은 tls 버전의 연결을 거부 합니다.

    ![Azure Database for MariaDB TLS 구성](./media/howto-tls-configurations/tls-configurations.png)

1. **저장**을 클릭하여 변경 내용을 저장합니다.

1. 연결 보안 설정이 성공적으로 설정 되었는지 확인 하는 알림이 나타납니다.

    ![Azure Database for MariaDB TLS 구성 성공](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>다음 단계

[메트릭에 대 한 경고를 만드는 방법](howto-alert-metric.md)에 대해 알아봅니다.
