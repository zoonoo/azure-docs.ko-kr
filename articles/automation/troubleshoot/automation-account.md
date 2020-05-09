---
title: Azure Automation 계정 문제 해결
description: Azure 계정 문제를 해결 하 고 문제를 해결 하는 방법을 알아봅니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 6a7defdaa9b4b0f0b3580a3ac6b1a0487434a97c
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864133"
---
# <a name="troubleshoot-an-azure-automation-account"></a>Azure Automation 계정 문제 해결

이 문서에서는 Azure Automation 계정을 사용할 때 발생할 수 있는 문제에 대 한 해결 방법을 설명 합니다. Automation 계정에 대 한 일반 정보는 [Azure 계정 만들기](../automation-quickstart-create-account.md)를 참조 하세요.

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>시나리오: 구독에 대 한 자동화 리소스 공급자를 등록할 수 없습니다.

### <a name="issue"></a>문제

Automation 계정에서 관리 솔루션을 사용 하는 경우 다음과 같은 오류가 발생 합니다.

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>원인

자동화 리소스 공급자가 구독에 등록 되어 있지 않습니다.

### <a name="resolution"></a>해결 방법

자동화 리소스 공급자를 등록 하려면 Azure Portal에서 다음 단계를 수행 합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다.

2. **구독**으로 이동 하 여 구독을 선택 합니다.   

3. **설정**아래에서 **리소스 공급자**를 선택 합니다.

4. 리소스 공급자 목록에서 **Microsoft Automation** 리소스 공급자가 등록 되어 있는지 확인 합니다.

5. 공급자가 나열 되지 않은 경우 [리소스 공급자 등록 오류 해결](/azure/azure-resource-manager/resource-manager-register-provider-errors)에서 설명한 대로 등록 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 문제를 해결 하지 못하는 경우 추가 지원을 위해 다음 채널 중 하나를 시도해 보세요.

* Azure [포럼](https://azure.microsoft.com/support/forums/)을 통해 azure 전문가 로부터 답변을 받으세요.
* 을 사용 [@AzureSupport](https://twitter.com/azuresupport)하 여 연결 합니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하는 공식 Microsoft Azure 계정입니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동 하 여 **지원 받기**를 선택 합니다.