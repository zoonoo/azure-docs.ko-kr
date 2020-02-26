---
title: Azure ATP 데이터를 Azure 센티널에 연결 | Microsoft Docs
description: Azure ATP 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 387d04cbbb125006efcc4efc53a02015fe3f5919
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588589"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Azure ATP (Advanced Threat Protection)에서 데이터 연결

> [!IMPORTANT]
> Azure 센티널의 Azure Advanced Threat Protection 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

한 번의 클릭으로 [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) 에서 azure 센티널로 로그를 스트리밍할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 전역 관리자 또는 보안 관리자 권한이 있는 사용자
- Azure ATP의 미리 보기 고객 이어야 하 고 Azure ATP와 Microsoft Cloud App Security 간의 통합을 사용 하도록 설정 해야 합니다. 자세한 내용은 [Azure Advanced Protection 통합](https://docs.microsoft.com/cloud-app-security/aatp-integration)을 참조 하세요.

## <a name="connect-to-azure-atp"></a>Azure ATP에 연결

Azure ATP 미리 보기 버전이 [네트워크에서 사용 하도록 설정](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)되었는지 확인 합니다.
Azure ATP가 배포 되 고 데이터를 수집 의심 스러운 경고를 Azure 센티널로 쉽게 스트리밍할 수 있습니다. 경고가 Azure 센티널로 스트리밍을 시작 하는 데 최대 24 시간이 걸릴 수 있습니다.


1. Azure ATP를 azure 센티널에 연결 하려면 먼저 Azure ATP와 Microsoft Cloud App Security 간의 통합을 사용 하도록 설정 해야 합니다. 이 작업을 수행 하는 방법에 대 한 자세한 내용은 [Azure Advanced Threat Protection 통합](https://docs.microsoft.com/cloud-app-security/aatp-integration)을 참조 하세요.

1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음 **Azure Advanced Threat Protection (미리 보기)** 타일을 클릭 합니다.

1. Azure ATP의 경고가 Azure 센티널에서 자동으로 인시던트를 자동으로 생성할지 여부를 선택할 수 있습니다. **인시던트 만들기**에서 **사용**을 선택하여 연결된 보안 서비스에서 생성된 경고에서 인시던트가 자동으로 생성되는 기본 분석 규칙을 사용하도록 설정합니다. 그런 다음, **Analytics** 및 **활성 규칙**에서 이 규칙을 편집할 수 있습니다.

1. **연결**을 클릭합니다.

1. Azure ATP 경고에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **Securityalert**를 검색 합니다.

> [!NOTE]
> 경고가 30KB 보다 큰 경우 Azure 센티널은 경고에서 엔터티 필드를 표시 하는 것을 중지 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 azure 센티널에 Azure Advanced Threat Protection을 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

