---
title: Azure 정책과 통합-Azure Batch | Microsoft Docs
description: ''
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/24/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 9a306457f838fc79d12be3217d96cc8fb25c9c1b
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618374"
---
# <a name="integration-with-azure-policy"></a>Azure Policy와 통합

Azure Policy은 리소스에 대 한 규칙을 적용 하 여 회사 표준 및 서비스 수준 계약을 준수 하도록 하는 정책을 만들고 할당 하 고 관리 하는 데 사용 하는 Azure의 서비스입니다. Azure Policy는 사용자가 할당 한 정책과 호환 되지 않는 리소스를 평가 합니다. 

Azure Batch에는 정책 준수를 관리 하는 데 도움이 되는 두 가지 기본 제공 확장이 있습니다. 

|**이름**...|   **설명**|**효과 (s)**|  **버전**|    **원본**
|----------------|----------|----------|----------------|---------------|
|배치 계정의 진단 로그를 사용하도록 설정해야 합니다.|   진단 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다.|AuditIfNotExists, 사용 안 함|  2.0.0|  GitHub|
|Batch 계정에서 메트릭 경고 규칙을 구성 해야 합니다.| 필요한 메트릭을 사용할 수 있도록 설정하는 Batch 계정에서 메트릭 경고 규칙 구성 감사|   AuditIfNotExists, 사용 안 함| 1.0.0|  GitHub|

정책 정의에서 충족 해야 하는 조건을 설명 합니다. 조건은 리소스 속성을 필요한 값과 비교 합니다. 미리 정의 된 별칭을 사용 하 여 리소스 속성 필드에 액세스 합니다. 속성 별칭을 사용 하 여 리소스 형식의 특정 속성에 액세스할 수 있습니다. 별칭을 사용하면 리소스의 속성에 허용되는 값이나 조건을 제한할 수 있습니다. 각 별칭은 주어진 리소스 유형에 대해 서로 다른 API 버전의 경로에 매핑됩니다. 정책 평가 중에 정책 엔진은 해당 API 버전에 대한 속성 경로를 가져옵니다.

Batch에 필요한 리소스에는 계정, 계산 노드, 풀, 작업 및 태스크가 포함 됩니다. 따라서 속성 별칭을 사용 하 여 이러한 리소스의 특정 속성에 액세스할 수 있습니다. [별칭](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases) 에 대 한 자세한 정보

현재 별칭을 알고 있는지 확인 하 고 리소스 및 정책을 검토 하려면 Visual Studio Code에 대 한 Azure 정책 확장을 사용 합니다. Visual Studio Code에서 지 원하는 모든 플랫폼에 설치할 수 있습니다. 이 지원에는 Windows, Linux 및 macOS가 포함 됩니다. [설치 지침](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode)을 참조 하세요.



