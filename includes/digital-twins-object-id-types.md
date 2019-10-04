---
title: 포함 파일
description: 포함 파일
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: 12d4278171d43fdaf8613a1c91bdbffc269adc56
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949016"
---
`objectIdType`(또는 **개체 식별자 유형**)은 역할에 지정된 ID 유형을 나타냅니다. `DeviceId` 및 `UserDefinedFunctionId` 유형과 별도로, 개체 식별자 유형은 Azure Active Directory 개체의 속성에 해당합니다.

다음 표에는 Azure Digital Twins에서 지원되는 개체 식별자 유형이 나와 있습니다.

| 형식 | 설명 |
| --- | --- |
| UserId | 사용자에게 역할을 할당합니다. |
| DeviceID | 디바이스에 역할을 할당합니다. |
| DomainName | 도메인 이름에 역할을 할당합니다. 지정된 도메인 이름을 가진 각 사용자는 해당 역할의 액세스 권한을 가집니다. |
| TenantId | 테넌트에 역할을 할당합니다. 지정된 Azure AD 테넌트 ID에 속한 각 사용자는 해당 역할의 액세스 권한을 가집니다. |
| ServicePrincipalId | 서비스 주체 개체 ID에 역할을 할당합니다. |
| UserDefinedFunctionId | UDF(사용자 정의 함수)에 역할을 할당합니다. |