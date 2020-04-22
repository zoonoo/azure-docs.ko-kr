---
title: Automation 계정 문제 해결
description: Azure 계정으로 문제를 해결하고 해결하는 방법을 알아봅니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 7b5e7171ac679384966e9dce79425cd1fa881c53
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679392"
---
# <a name="troubleshoot-the-automation-account"></a>자동화 계정 문제 해결

이 문서에서는 자동화 계정을 사용할 때 발생할 수 있는 문제에 대한 해결 방법을 설명합니다. 다음 섹션에서는 특정 오류 메시지와 각 오류에 대한 가능한 해결 을 강조 표시합니다. 자동화 계정에 대한 일반 정보는 [Azure 계정 만들기를](../automation-quickstart-create-account.md)참조하십시오.

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>시나리오: 구독에 대한 자동화 리소스 공급자를 등록할 수 없음

### <a name="issue"></a>문제

자동화 계정에서 관리 솔루션으로 작업할 때 다음과 같은 오류가 발생합니다.

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>원인

자동화 리소스 공급자가 구독에 등록되지 않았습니다.

### <a name="resolution"></a>해결 방법

자동화 리소스 공급자를 등록하려면 Azure 포털에서 다음 단계를 따르십시오.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다.

2. **구독으로** 이동하여 구독 페이지에서 구독을 선택합니다.   

3. **설정에서** **리소스 공급자를**선택합니다.

4. 리소스 공급자 목록에서 **Microsoft.Automation** 리소스 공급자가 등록되어 있는지 확인합니다.

5. 공급자가 나열되지 않은 경우 [리소스 공급자 등록에 대한 해결 오류에](/azure/azure-resource-manager/resource-manager-register-provider-errors)설명된 대로 등록합니다.

## <a name="next-steps"></a>다음 단계

위에 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 시도해 보십시오.

* Azure 포럼을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/forums/)
* [@AzureSupport](https://twitter.com/azuresupport)Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.