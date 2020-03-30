---
title: Azure 정책과의 통합 - Azure 일괄 처리 | 마이크로 소프트 문서
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77618374"
---
# <a name="integration-with-azure-policy"></a>Azure 정책과의 통합

Azure Policy는 Azure에서 해당 리소스가 회사 표준 및 서비스 수준 계약을 준수하도록 리소스에 대한 규칙을 적용하는 정책을 만들고 할당하고 관리하는 데 사용하는 서비스입니다. Azure Policy는 할당한 정책을 준수하지 않는지 리소스를 평가합니다. 

Azure Batch에는 정책 준수를 관리하는 데 도움이 되는 두 가지 기본 제공 확장이 있습니다. 

|**이름**...|   **설명**|**효과**|  **버전**|    **원본**
|----------------|----------|----------|----------------|---------------|
|배치 계정의 진단 로그를 사용하도록 설정해야 합니다.|   진단 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다.|AuditIfNotExists, 사용 안 함|  2.0.0|  GitHub|
|Batch 계정에서 메트릭 경고 규칙을 구성해야 함| 필요한 메트릭을 사용할 수 있도록 설정하는 Batch 계정에서 메트릭 경고 규칙 구성 감사|   AuditIfNotExists, 사용 안 함| 1.0.0|  GitHub|

정책 정의는 충족해야 하는 조건을 설명합니다. 조건은 리소스 속성을 필요한 값과 비교합니다. 리소스 속성 필드는 미리 정의된 별칭을 사용하여 액세스됩니다. 속성 별칭을 사용하여 리소스 형식의 특정 속성에 액세스합니다. 별칭을 사용하면 리소스의 속성에 허용되는 값이나 조건을 제한할 수 있습니다. 각 별칭은 주어진 리소스 유형에 대해 서로 다른 API 버전의 경로에 매핑됩니다. 정책 평가 중에 정책 엔진은 해당 API 버전에 대한 속성 경로를 가져옵니다.

Batch에 필요한 리소스에는 계정, 계산 노드, 풀, 작업 및 작업이 포함됩니다. 따라서 속성 별칭을 사용하여 이러한 리소스에 대한 특정 속성에 액세스합니다. [별칭에](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases) 대해 자세히 알아보기

현재 별칭을 알고 리소스 및 정책을 검토하려면 Visual Studio 코드에 대한 Azure 정책 확장을 사용합니다. Visual Studio 코드에서 지원하는 모든 플랫폼에 설치할 수 있습니다. 이 지원에는 Windows, Linux 및 macOS가 포함됩니다. [설치 지침을](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode)참조하십시오.



