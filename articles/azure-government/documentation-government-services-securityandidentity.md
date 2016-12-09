---
title: "Azure Government 보안 및 ID | Microsoft 문서"
description: "Azure Government용 응용 프로그램 개발에 대한 지침 및 기능 비교를 제공합니다."
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: e2fe7983-5870-43e9-ae01-2d45d3102c8a
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/12/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: cd01170c3c0a3f62024de3357d342af1f4f90c6c
ms.openlocfilehash: 27d447e8e3c336bbce2e1ca81d2c7c413b0360fc


---
# <a name="azure-government-security-identity"></a>Azure Government 보안 및 ID
## <a name="key-vault"></a>Key Vault
이 서비스에 대한 자세한 내용 및 사용 방법은 [Azure Key Vault 공개 문서](../key-vault/index.md)를 참조하세요.

### <a name="data-considerations"></a>데이터 고려 사항
다음 정보는 Azure 주요 자격 증명 모음에 대한 Azure Government 경계를 식별합니다.

| 규제된/제어된 데이터 허용됨 | 규제된/제어된 데이터 허용되지 않음 |
| --- | --- |
| Azure 주요 자격 증명 모음으로 암호화된 모든 데이터는 규제된/제어된 데이터를 포함할 수 있습니다. |Azure 주요 자격 증명 모음 메타데이터는 내보내기 제어된 데이터를 포함하도록 허용되지 않습니다. 이 메타데이터는 주요 자격 증명 모음을 생성 및 유지 관리하는 경우 입력된 모든 구성 데이터를 포함합니다.  다음 필드에 규제된/제어된 데이터인 리소스 그룹 이름, 주요 자격 증명 모음 이름, 구독 이름을 입력하지 마세요. |

주요 자격 증명 모음은 일반적으로 Azure Government에서 사용할 수 있습니다. 공용에서 마찬가지로 확장명이 없으므로 PowerShell 및 CLI를 통해서만 주요 자격 증명 모음을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트는 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.




<!--HONumber=Nov16_HO3-->


