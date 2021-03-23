---
title: Azure Synapse 연결 설정
description: Azure Synapse Analytics에서 연결 설정을 구성하는 방법을 설명하는 문서
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 03/15/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: e0d8a8e3320b49b6fbe3e8ab66c0b4569fac9afd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587936"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Azure Synapse Analytics 연결 설정

이 문서에서는 Azure Synapse Analytics의 연결 설정 및 해당하는 경우 이를 구성하는 방법을 설명합니다.


## <a name="connection-policy"></a>연결 정책
Azure Synapse Analytics의 Synapse SQL에 대한 연결 정책이 *기본값* 으로 설정되어 있습니다. Azure Synapse Analytics에서는 이를 변경할 수 없습니다. [여기](../../azure-sql/database/connectivity-architecture.md#connection-policy)에서 Azure Synapse Analytics의 Synapse SQL 연결에 영향을 주는 방법에 대해 자세히 알아볼 수 있습니다. 

## <a name="minimal-tls-version"></a>최소 TLS 버전
Azure Synapse Analytics의 Synapse SQL은 모든 TLS 버전을 사용하는 연결을 허용합니다. Azure Synapse Analytics에서는 Synapse SQL에 대한 최소 TLS 버전을 설정할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[Azure Synapse 작업 영역](./synapse-workspace-ip-firewall.md) 만들기