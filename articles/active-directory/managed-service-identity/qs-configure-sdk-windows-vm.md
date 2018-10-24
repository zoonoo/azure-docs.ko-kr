---
title: Azure SDK를 사용하여 Azure 리소스에 대한 관리 ID로 VM 구성
description: Azure SDK를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID를 구성 및 사용하는 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: daveba
ms.openlocfilehash: 80368c6e6e879478df98d5c0740d60fa59703f2d
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338388"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Azure SDK를 사용하여 Azure 리소스에 대한 관리 ID로 VM 구성

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 리소스에 대한 관리 ID는 Azure AD(Active Directory)에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure SDK를 사용하여 Azure VM의 Azure 리소스에 대한 관리 ID를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Azure 리소스 지원에 대한 관리 ID가 있는 Azure ADK 

Azure는 일련의 [Azure SDK](https://azure.microsoft.com/downloads)를 통해 여러 프로그래밍 플랫폼을 지원합니다. 일부는 Azure 리소스에 대한 관리 ID를 지원하도록 업데이트되었으며 사용법을 보여주는 예제를 제공합니다. 지원이 추가됨에 따라 이 목록이 업데이트됩니다.

| SDK) | 샘플 |
| --- | ------ | 
| .NET   | [Azure 리소스에 대한 관리 ID가 설정된 VM에서 리소스 관리](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| 자바   | [Azure 리소스에 대한 관리 ID가 설정된 VM에서 저장소 관리](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [시스템 할당 관리 ID가 설정된 VM 만들기](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [시스템 할당 관리 ID가 설정된 VM 만들기](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [시스템 할당 ID가 설정된 Azure VM 만들기](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>다음 단계

- Azure Portal, PowerShell, CLI 및 리소스 템플릿을 사용하는 방법을 알아보려면 **Azure VM에 대한 ID 구성** 아래에서 관련된 문서를 참조하세요.
