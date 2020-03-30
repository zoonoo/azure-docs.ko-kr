---
title: 공용 네트워크 액세스 거부 - Azure 포털 - MariaDB용 Azure 데이터베이스
description: MariaDB용 Azure 데이터베이스에 대한 Azure 포털을 사용하여 공용 네트워크 액세스 거부를 구성하는 방법에 대해 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 87b6033e486e9009573436628d0183c8a022aced
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375240"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Azure 포털을 사용하여 MariaDB에 대한 Azure 데이터베이스의 공용 네트워크 액세스 거부

이 문서에서는 모든 공용 구성을 거부하고 개인 끝점을 통한 연결만 허용하여 네트워크 보안을 더욱 강화하도록 MariaDB 서버에 대한 Azure Database를 구성하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 완료하려면 다음이 필요합니다.

* [MariaDB에 대한 Azure 데이터베이스](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>공용 네트워크 액세스 거부 설정

다음 단계에 따라 MariaDB 서버 공용 네트워크 액세스 거부를 설정합니다.

1. Azure [포털에서](https://portal.azure.com/)MariaDB 서버에 대한 기존 Azure 데이터베이스를 선택합니다.

1. MariaDB 서버 페이지에서 **설정**에서 **연결 보안을** 클릭하여 연결 보안 구성 페이지를 엽니다.

1. 공용 네트워크 액세스 거부에서 **예를** 선택하여 MariaDB 서버에 대한 공개 액세스를 거부합니다.

    ![MariaDB 거부 네트워크 액세스를 위한 Azure 데이터베이스](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. **저장**을 클릭하여 변경 내용을 저장합니다.

1. 연결 보안 설정이 성공적으로 활성화되었다는 알림이 표시됩니다.

    ![MariaDB 거부 네트워크 액세스 성공을 위한 Azure 데이터베이스](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>다음 단계

[메트릭에 대한 경고를 만드는 방법에](howto-alert-metric.md)대해 알아봅니다.