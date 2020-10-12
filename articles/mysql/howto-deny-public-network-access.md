---
title: 공용 네트워크 액세스 거부-Azure Portal-Azure Database for MySQL
description: Azure Database for MySQL에 대 한 Azure Portal를 사용 하 여 공용 네트워크 액세스 거부를 구성 하는 방법을 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: eb5c4e4c4dfb73b2f7c9dc9f2629296790790885
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90896148"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for MySQL에서 공용 네트워크 액세스 거부

이 문서에서는 모든 공용 구성을 거부 하도록 Azure Database for MySQL 서버를 구성 하 고 개인 끝점을 통해서만 연결을 허용 하 여 네트워크 보안을 강화 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

* [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>공용 네트워크 액세스 거부 설정

MySQL server Deny 공용 네트워크 액세스를 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for MySQL 서버를 선택 합니다.

1. MySQL server 페이지의 **설정**에서 **연결 보안** 을 클릭 하 여 연결 보안 구성 페이지를 엽니다.

1. **공용 네트워크 액세스 거부**에서 **예** 를 선택 하 여 MySQL server에 대 한 공용 액세스 거부를 사용 하도록 설정 합니다.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG" alt-text="네트워크 액세스 거부 Azure Database for MySQL":::

1. **저장**을 클릭하여 변경 내용을 저장합니다.

1. 연결 보안 설정이 성공적으로 설정 되었는지 확인 하는 알림이 나타납니다.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png" alt-text="네트워크 액세스 거부 Azure Database for MySQL":::

## <a name="next-steps"></a>다음 단계

[메트릭에 대 한 경고를 만드는 방법](howto-alert-on-metric.md)에 대해 알아봅니다.