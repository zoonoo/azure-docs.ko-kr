---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 20ef9a4f30aafee562096e584c4b80fef236b062
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2021
ms.locfileid: "108749731"
---
Key Vault는 각 항목에서 고유한 사용 권한 세트를 특정 보안 주체에 부여하는 최대 1,024개의 액세스 정책 항목을 지원합니다. 이러한 제한 때문에 개별 사용자자보다는 가능한 한 사용자 그룹에 액세스 정책을 할당하는 것이 좋습니다. 그룹을 사용하면 조직의 여러 사용자에 대한 사용 권한을 보다 쉽게 관리할 수 있습니다. 자세한 내용은 [Azure Active Directory 그룹을 사용하여 앱 및 리소스 액세스 관리](../articles/active-directory/fundamentals/active-directory-manage-groups.md)를 참조하세요.

Key Vault 액세스 제어에 대한 자세한 내용은 [Azure Key Vault 보안 기능: ID 및 액세스 관리](../articles/key-vault/general/security-features.md#identity-management)를 참조하세요.