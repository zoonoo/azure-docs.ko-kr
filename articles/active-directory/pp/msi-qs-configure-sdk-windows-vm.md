---
title: "Azure SDK를 사용하여 Azure VM에 사용자 할당 MSI를 구성하는 방법"
description: "Azure SDK를 사용하여 Azure VM에 사용자 할당 MSI(관리 서비스 ID)를 구성하기 위한 단계별 지침을 제공합니다."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 19b6179803b8de4102818c1522b00e6b4881d0f0
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/22/2017
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-sdk"></a>Azure SDK를 사용하여 VM에 사용자 할당 MSI(관리 서비스 ID) 구성

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

관리 서비스 ID는 Azure Active Directory에서 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다. 

이 문서에서는 Azure SDK를 사용하여 Azure VM에 대해 사용자 할당 MSI를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="azure-sdks-with-msi-support"></a>MSI를 지원하는 Azure SDK 

Azure는 일련의 [Azure SDK](https://azure.microsoft.com/downloads)를 통해 여러 프로그래밍 플랫폼을 지원합니다. 일부는 MSI를 지원하도록 업데이트되었으며 사용법을 보여주는 예제를 제공합니다. 지원이 추가됨에 따라 이 목록이 업데이트됩니다.

| SDK) | 샘플 |
| --- | ------ | 
| 파이썬 | [MSI가 활성화된 VM 만들기](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [MSI에서 Azure VM 만들기](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>다음 단계

- “Azure VM에 대한 MSI 구성”에서 관련된 문서를 참조하여 Azure VM에서 사용자 할당 MSI를 구성하는 방법에 대해 알아보세요.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.
