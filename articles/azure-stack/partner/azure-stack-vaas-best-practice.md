---
title: Azure Stack 유효성 검사 모범 사례입니다. | Microsoft Docs
description: 이 문서에서는 서비스와 유효성 검사에 대 한 모범 사례에 있습니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: John.Haskin
ms.openlocfilehash: d83eb501bbe685890decb3acbb8116164f4199db
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422663"
---
# <a name="best-practices-for-validation-as-a-service"></a>서비스 유효성 검사에 대 한 모범 사례

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

이 유효성 검사 (VaaS) 서비스에서 리소스를 관리 하는 것에 대 한 모범 사례를 설명 합니다. VaaS 리소스의 개요를 보려면 [유효성 검사 서비스 핵심 개념으로](azure-stack-vaas-key-concepts.md)입니다.

## <a name="solution-management"></a>관리 솔루션

### <a name="naming-convention-for-vaas-solutions"></a>VaaS 솔루션에 대 한 명명 규칙

VaaS에 등록 된 모든 솔루션에 대 한 일관 된 명명 규칙을 사용 합니다. 예를 들어, 솔루션 이름을 생성할 수 있습니다 다음 하드웨어 속성에서 다음과 같습니다.

|제품 이름 | 고유한 하드웨어 요소 1 | 고유한 하드웨어 요소 2 | 솔루션 이름
|---|---|---|---|
필자의 솔루션 XYZ |  모든 플래시 | 내 스위치 X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>새 VaaS 솔루션을 만들어야 하는 경우

SKU는 동일한 하드웨어에 대 한 워크플로 실행 하는 경우 동일한 VaaS 솔루션을 사용 합니다. 하드웨어 SKU 변경 하는 경우에 새 VaaS 솔루션을 만들어야 합니다.

## <a name="workflow-management"></a>워크플로 관리

### <a name="naming-convention-for-vaas-workflows"></a>VaaS 워크플로에 대 한 명명 규칙

모든 VaaS 워크플로 실행에 대 한 일관 된 명명 규칙을 사용 합니다. 예를 들어, 구문을 워크플로 빌드 속성 아래에서 다음과 같이 이름을:

|빌드 번호 (주) | Date | 솔루션 크기 | 워크플로 이름
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한 [서비스 핵심 개념으로 유효성 검사](azure-stack-vaas-key-concepts.md)
