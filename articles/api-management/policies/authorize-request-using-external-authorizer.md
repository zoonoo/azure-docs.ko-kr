---
title: Azure API 관리 정책 샘플 - 외부 권한 부여자를 사용하여 요청 인증 | Microsoft Docs
description: Azure API 관리 정책 샘플 - 사용자 지정 또는 레거시 인증/권한 부여 논리를 캡슐화하는 외부 권한 부여자를 사용하여 요청을 인증하는 방법을 설명합니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: cce2376c440c233d81198d903709867c5fdca7ec
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34840694"
---
# <a name="authorize-requests-using-external-authorizer"></a>외부 권한 부여자를 사용하여 요청 인증

이 문서에서는 사용자 지정 인증/권한 부여 논리를 캡슐화하는 외부 권한 부여자를 사용하여 API 액세스를 보호하는 방법을 보여 주는 Azure API 관리 정책 샘플이 나와 있습니다. 정책 코드를 설정하거나 편집하려면 [정책 설정 또는 편집](../set-edit-policies.md)에 설명된 단계를 따릅니다. 다른 예제를 보려면 [정책 샘플](../policy-samples.md)을 참조하세요.

## <a name="policy"></a>정책

코드를 **인바운드** 블록에 붙여넣습니다.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>다음 단계

APIM 정책에 대해 자세히 알아보기:

+ [액세스 제한 정책](../api-management-access-restriction-policies.md)
+ [정책 샘플](../policy-samples.md)