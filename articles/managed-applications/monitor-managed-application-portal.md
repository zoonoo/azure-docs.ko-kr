---
title: Azure Portal을 사용하여 관리되는 애플리케이션 모니터링 | Microsoft Docs
description: Azure Portal을 사용하여 관리되는 애플리케이션의 가용성과 경고를 모니터링하는 방법을 설명합니다.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 754d2a246a86585e9f05f8a070c51e158f73affd
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342598"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>관리되는 애플리케이션의 배포된 인스턴스 모니터링

관리되는 애플리케이션을 Azure 구독에 배포한 후에는 애플리케이션의 상태를 확인할 수 있습니다. 이 문서에서는 상태 확인을 위한 Azure Portal의 옵션에 대해 설명합니다. 관리되는 애플리케이션에서 리소스의 가용성을 모니터링할 수 있으며, 경고를 설정하고 확인할 수도 있습니다.

## <a name="view-resource-health"></a>리소스 상태 보기

1. 관리되는 애플리케이션 인스턴스를 선택합니다.

   ![관리되는 애플리케이션 선택](./media/monitor-managed-application-portal/select-managed-application.png)

1. **Resource Health**를 선택합니다.

   ![Resource Health 선택](./media/monitor-managed-application-portal/select-resource-health.png)

1. 관리되는 애플리케이션에서 리소스의 가용성을 확인합니다.

   ![리소스 상태 보기](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>경고 보기

1. **경고**를 선택합니다.

   ![경고 선택](./media/monitor-managed-application-portal/select-alerts.png)

1. 경고 규칙을 구성한 경우 발생한 경고 관련 정보가 표시됩니다.

   ![경고 보기](./media/monitor-managed-application-portal/view-alerts.png)

1. 경고 규칙을 추가하려면 **+ 새 경고 규칙**을 선택합니다.

   ![경고 만들기](./media/monitor-managed-application-portal/create-new-alert.png)

관리되는 애플리케이션 인스턴스 또는 관리되는 애플리케이션의 리소스에 대해 경고를 만들 수 있습니다. 경고 만들기에 대한 자세한 내용은 [Microsoft Azure의 경고 개요](../azure-monitor/platform/alerts-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 관리되는 애플리케이션 예제는 [Azure 관리되는 애플리케이션의 샘플 프로젝트](sample-projects.md)를 참조하세요.
* 관리되는 애플리케이션을 배포하려면 [Azure Portal을 통해 서비스 카탈로그 앱 배포](deploy-service-catalog-quickstart.md)를 참조하세요.