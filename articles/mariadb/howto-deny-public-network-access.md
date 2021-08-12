---
title: 공용 네트워크 액세스 거부 - Azure Portal - Azure Database for MariaDB
description: Azure Database for MariaDB에 Azure Portal을 사용하여 공용 네트워크 액세스 거부를 구성하는 방법 알아보기
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 3117ebfd258c72bb97432871c2ea74d30c52f669
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98665075"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MariaDB에서 공용 네트워크 액세스 거부

이 문서에서는 모든 공용 구성을 거부하고 프라이빗 엔드포인트를 통한 연결만 허용하여 네트워크 보안을 더욱 강화하도록 Azure Database for MariaDB 서버를 구성하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

* [Azure Database for MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>공용 네트워크 액세스 거부 설정

다음 단계를 수행하여 MariaDB 서버에서 공용 네트워크 액세스 거부를 설정합니다.

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for MariaDB 서버를 선택합니다.

1. MariaDB 서버 페이지의 **설정** 에서 **연결 보안** 을 클릭하여 연결 보안 구성 페이지를 엽니다.

1. 공용 네트워크 액세스 거부에서 **예** 를 선택하여 MariaDB 서버에 대한 공용 액세스 거부를 사용하도록 설정합니다.

    ![Azure Database for MariaDB 네트워크 액세스 거부](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. **저장** 을 클릭하여 변경 내용을 저장합니다.

1. 연결 보안 설정이 성공적으로 활성화되었는지 확인하는 알림이 나타납니다.

    ![Azure Database for MariaDB 네트워크 액세스 성공 거부](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>다음 단계

[메트릭에 대한 경고를 만드는 방법](howto-alert-metric.md)에 대해 알아봅니다.