---
title: TLS 구성 - Azure Portal - Azure Database for MySQL
description: Azure Database for MySQL에 Azure Portal을 사용하여 TLS 구성을 설정하는 방법을 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: c5de8a9f50dd280f8eb3a52ad0bd39a00e58c789
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642121"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MySQL에서 TLS 설정 구성

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

이 문서에서는 연결에 허용된 최소 TLS 버전을 적용하고 적용된 최소 TLS 버전보다 더 낮은 TLS 버전의 모든 연결을 거부하여 네트워크 보안을 강화하도록 Azure Database for MySQL 서버를 구성하는 방법을 설명합니다.

Azure Database for MySQL에 연결하는 데 TLS 버전을 적용할 수 있습니다. 이제 고객은 데이터베이스 서버에 최소 TLS 버전을 설정할 수 있습니다. 예를 들어 이 최소 TLS 버전을 1.0으로 설정하면 TLS 1.0, 1.1 및 1.2를 사용하여 연결 하는 클라이언트를 허용하게 됩니다. 또는 이를 1.2로 설정하면 TLS 1.2+를 사용하여 연결하는 클라이언트만 허용하고 TLS 1.0 및 TLS 1.1을 사용하는 모든 연결은 거부됩니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

* [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>Azure Database for MySQL의 TLS 구성 설정

MySQL 서버 최소 TLS 버전을 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for MySQL 서버를 선택합니다.

1. MySQL 서버 페이지의 **설정** 에서 **연결 보안** 을 클릭하여 연결 보안 구성 페이지를 엽니다.

1. **최소 TLS 버전** 에서 MySQL 서버용 TLS 1.2 보다 낮은 TLS 버전을 이용한 연결을 거부하려면 **1.2** 를 선택합니다.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="Azure Database for MySQL TLS 구성":::

1. **저장** 을 클릭하여 변경 내용을 저장합니다. 

1. 연결 보안 설정이 성공적으로 사용하는 것으로 설정되고 즉시 적용되었다는 알림이 옵니다. 필요하거나 실행된 서버를 **다시 시작** 하지 않아도 됩니다. 변경 내용을 저장한 후에는 TLS 버전이 포털에 설정된 최소 TLS 버전보다 높거나 같은 경우에만 서버의 모든 새 연결이 허용됩니다.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Azure Database for MySQL TLS 구성 성공":::

## <a name="next-steps"></a>다음 단계

- [메트릭에 대한 경고를 만드는 방법](howto-alert-on-metric.md)에 관한 자세한 정보
