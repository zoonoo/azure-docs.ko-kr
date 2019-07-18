---
title: Azure Firewall 서비스 태그 개요
description: 이 문서는 Azure Firewall 서비스 태그의 개요입니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 6/27/2019
ms.author: victorh
ms.openlocfilehash: d0ac36e415c056dffc9c75d00968ff74c2156e63
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450163"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall 서비스 태그

서비스 태그는 보안 규칙 생성에 대한 복잡성을 최소화할 수 있는 IP 주소 접두사의 그룹을 나타냅니다. 고유한 서비스 태그를 직접 만들거나 태그 내에 포함된 IP 주소를 지정할 수 없습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

Azure Firewall 서비스 태그는 네트워크 규칙 대상 필드에 사용할 수 있습니다. 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다.

## <a name="supported-service-tags"></a>지원되는 서비스 태그

참조 [보안 그룹](../virtual-network/security-overview.md#service-tags) Azure 방화벽 네트워크 규칙에 사용할 수 있는 서비스 태그의 목록에 대 한 합니다.

## <a name="next-steps"></a>다음 단계

Azure Firewall 규칙에 대한 자세한 내용은 [Azure Firewall 규칙 처리 논리](rule-processing.md)를 참조하세요.