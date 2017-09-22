---
title: "Azure Active Directory용 MSI(관리 서비스 ID)의 알려진 문제"
description: "Azure Active Directory용 MSI(관리 서비스 ID)의 알려진 문제에 대해 설명합니다."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 09/14/2017
ms.author: skwan
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 6fb8317f33ec8c36af8553466665fb2088c49527
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---

# <a name="known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Azure Active Directory용 MSI(관리 서비스 ID)의 알려진 문제

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

## <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Azure Portal에 구성 블레이드가 표시되지 않음

VM에 VM 구성 블레이드가 표시되지 않는 경우 해당 지역의 Portal에서 MSI가 아직 사용하도록 설정되지 않은 것입니다.  나중에 다시 확인하세요.  [PowerShell](msi-qs-configure-powershell-windows-vm.md) 또는 [Azure CLI](msi-qs-configure-cli-windows-vm.md)를 사용하여 VM에 대해 MSI를 사용하도록 설정할 수도 있습니다.

## <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>액세스 제어(IAM) 블레이드에서 가상 컴퓨터 액세스 권한을 할당할 수 없음

Azure Portal의 **액세스 제어(IAM)** > **권한 추가**에서 **다음에 대한 액세스 할당:**의 선택 항목으로 **가상 컴퓨터**가 표시되지 않는 경우 해당 지역의 Portal에서 관리 서비스 ID가 아직 사용하도록 설정되지 않은 것입니다. 나중에 다시 확인하세요.  MSI의 서비스 주체를 검색하여 역할 할당용으로 관리 서비스 ID를 선택할 수는 있습니다.  **선택** 필드에 VM 이름을 입력하면 서비스 주체가 검색 결과에 표시됩니다.

## <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>VM이 리소스 그룹 또는 구독에서 이동한 후 시작되지 않음

실행 중인 상태의 VM을 이동하면 이동 중에도 VM은 계속 실행됩니다. 그러나 이동 후 중지했다가 다시 시작하면 VM이 시작되지 않습니다. 이 문제가 발생하는 이유는 VM이 MSI ID에 대한 참조를 업데이트하지 않고 이전 리소스 그룹의 ID를 계속 가리키기 때문입니다.

**해결 방법** 
 
MSI의 올바른 값을 가져올 수 있도록 VM에서 업데이트를 트리거합니다. VM 속성 변경을 수행하여 MSI ID에 대한 참조를 업데이트할 수 있습니다. 예를 들어 다음 명령을 사용하여 VM에 새 태그 값을 설정할 수 있습니다.

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
이 명령은 VM에 값이 1인 새 태그 "fixVM"을 설정합니다. 
 
이 속성을 설정하면 VM은 올바른 MSI 리소스 URI로 업데이트됩니다. 그리고 나면 VM을 시작할 수 있습니다. 
 
VM이 시작되면 다음 명령을 사용하여 태그를 제거할 수 있습니다.

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>MSI는 ADAL(Active Directory Authentication Library) 또는 MSAL(Microsoft Authentication Library)에서 작동하나요?

아니요, MSI는 ADAL 또는 MSAL과 아직 통합되지 않았습니다.

