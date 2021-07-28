---
title: 관리 ID의 알려진 문제 - Azure Active Directory
description: Azure 리소스에 대한 관리 ID 관련 알려진 문제
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 8d2b6323a15595e57e7e89c6a83f9f718422e1d7
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226925"
---
# <a name="known-issues-with-managed-identities"></a>관리 ID의 알려진 문제

이 문서에서는 관리 ID와 관련된 몇 가지 문제와 해결 방법을 설명합니다. 관리 ID에 대한 일반적인 질문은 [질문과 대답](managed-identities-faq.md) 문서에 설명되어 있습니다.
## <a name="vm-fails-to-start-after-being-moved"></a>이동 후 VM이 시작되지 않음 

실행 중인 상태의 VM을 리소스 그룹 또는 구독에서 이동하는 경우 이동 중에도 계속 실행됩니다. 그러나 이동 후 중지했다가 다시 시작하면 VM이 시작되지 않습니다. 이 문제가 발생하는 이유는 VM이 Azure 리소스에 대한 관리 ID에 대한 참조를 업데이트하지 않고 이전 리소스 그룹의 ID를 계속 가리키기 때문입니다.

**해결 방법** 
 
Azure 리소스에 대한 관리 ID의 올바른 값을 가져올 수 있도록 VM에서 업데이트를 트리거합니다. VM 속성 변경을 수행하여 Azure 리소스에 대한 관리 ID에 대한 참조를 업데이트할 수 있습니다. 예를 들어 다음 명령을 사용하여 VM에 새 태그 값을 설정할 수 있습니다.

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
이 명령은 VM에 값이 1인 새 태그 "fixVM"을 설정합니다. 
 
이 속성을 설정하면 VM은 올바른 Azure 리소스에 대한 관리 ID 리소스 URI로 업데이트됩니다. 그리고 나면 VM을 시작할 수 있습니다. 
 
VM이 시작되면 다음 명령을 사용하여 태그를 제거할 수 있습니다.

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="transferring-a-subscription-between-azure-ad-directories"></a>Azure AD 디렉터리 간에 구독 전송

구독이 다른 디렉터리로 이동/전송되면 관리 ID는 업데이트되지 않습니다. 결과적으로, 존재하는 시스템에서 할당하거나 사용자가 할당한 관리 ID가 모두 손상됩니다. 

다른 디렉터리로 이동한 구독의 관리 ID에 대한 해결 방법:

 - 시스템에서 할당된 관리 ID: 비활성화하거나 재활성화합니다. 
 - 사용자 할당 관리 ID: 삭제한 후 다시 만들어서 필요한 리소스(예: 가상 머신)에 다시 연결합니다.

자세한 내용은 [다른 Azure AD 디렉터리로 Azure 구독 양도](../../role-based-access-control/transfer-subscription.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

[관리 ID를 지원하는 서비스](services-support-managed-identities.md) 및 [질문과 대답](managed-identities-faq.md)을 나열하는 문서를 검토할 수 있습니다.
