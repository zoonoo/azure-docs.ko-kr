---
title: 서비스로 Azure Stack 유효성 검사에는 Microsoft의 소프트웨어 업데이트의 유효성을 검사 | Microsoft Docs
description: 서비스 유효성 검사를 사용 하 여 Microsoft의 소프트웨어 업데이트의 유효성을 검사 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 8e0009bf0fc34d3e0d22755d93d941b85db62ffd
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334468"
---
# <a name="validate-software-updates-from-microsoft"></a>Microsoft의 소프트웨어 업데이트의 유효성을 검사합니다

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft은 Azure Stack 소프트웨어 업데이트를 주기적으로 해제 됩니다. 이러한 업데이트는 해당 솔루션에 대 한 업데이트의 유효성을 검사 하 고 Microsoft에 피드백을 제공할 수 있도록 공개적으로 제공 되 전에 Azure Stack 공동 엔지니어링 파트너에 게 제공 됩니다.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>월별 업데이트를 적용 합니다.

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>워크플로 만들기

Update 유효성 검사와 같은 워크플로 사용 하 여 **패키지 유효성 검사**합니다. 지침을 따르세요 [패키지 유효성 검사 워크플로 만드는](azure-stack-vaas-validate-oem-package.md#create-a-package-validation-workflow)합니다.

## <a name="run-tests"></a>테스트 실행

Update 유효성 검사와 같은 워크플로 사용 하 여 **패키지 유효성 검사**합니다. 지침을 따르세요 [실행 패키지 유효성 검사 테스트](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests)합니다.

패키지 업데이트 유효성 검사에 대 한 서명 요청할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

- [모니터링 및 VaaS 포털에서 테스트를 관리 합니다.](azure-stack-vaas-monitor-test.md)
