---
title: "VM의 Azure SDK에서 사용자 할당 관리 서비스 ID를 사용하는 방법"
description: "VM의 사용자 할당 MSI에서 Azure SDK를 사용하는 코드 샘플입니다."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 808b0357494adac8c8ad7f51e668317e378d290d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="use-azure-sdks-with-a-user-assigned-managed-service-identity-msi"></a>사용자 할당 MSI(관리 서비스 ID)에서 Azure SDK 사용

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

이 문서에서는 사용자 할당 MSI에 해당하는 Azure SDK 지원을 사용하는 방법을 보여 주는 SDK 샘플 목록을 제공합니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

> [!IMPORTANT]
> - 이 문서의 모든 샘플 코드/스크립트에서는 MSI 사용 Virtual Machine에서 클라이언트를 실행한다고 가정합니다. Azure Portal에서 VM "연결" 기능을 사용하여 VM에 원격으로 연결합니다. VM에서 MSI 사용에 대한 자세한 내용은 [Azure CLI를 사용하여 VM MSI(관리 서비스 ID) 구성](msi-qs-configure-cli-windows-vm.md) 또는 (PowerShell, Azure Portal, 템플릿 또는 Azure SDK 사용) 변형 문서 중 하나를 참조하세요. 

## <a name="sdk-code-samples"></a>SDK 코드 샘플

| SDK)             | 코드 샘플 |
| --------------- | ----------- |
| 파이썬          | [MSI를 사용하여 VM 내부에서 쉽게 인증](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [MSI가 활성화된 VM에서 리소스 관리](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>다음 단계

- 라이브러리 다운로드, 문서 등을 비롯한 Azure SDK 리소스의 전체 목록은 [Azure SDK](https://azure.microsoft.com/downloads/)를 참조하세요.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.








