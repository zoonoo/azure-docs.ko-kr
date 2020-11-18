---
title: Azure Firewall 서비스 태그 개요
description: 서비스 태그는 보안 규칙 생성에 대한 복잡성을 최소화할 수 있는 IP 주소 접두사의 그룹을 나타냅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 47377817b62d33e8af79e4a0d2dceb68ba9dbdc5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658650"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall 서비스 태그

서비스 태그는 보안 규칙 생성에 대한 복잡성을 최소화할 수 있는 IP 주소 접두사의 그룹을 나타냅니다. 고유한 서비스 태그를 직접 만들거나 태그 내에 포함된 IP 주소를 지정할 수 없습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

Azure Firewall 서비스 태그는 네트워크 규칙 대상 필드에 사용할 수 있습니다. 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다.

## <a name="supported-service-tags"></a>지원되는 서비스 태그

Azure 방화벽 네트워크 규칙에서 사용할 수 있는 서비스 태그 목록은 [보안 그룹](../virtual-network/network-security-groups-overview.md#service-tags) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure Firewall 규칙에 대한 자세한 내용은 [Azure Firewall 규칙 처리 논리](rule-processing.md)를 참조하세요.