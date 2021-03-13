---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ce13484aa085cef89ba34151824ee843dd3bd90e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "103419899"
---
|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 파일 동기화 개인 링크를 사용 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |표시 된 저장소 동기화 서비스 리소스에 대 한 개인 끝점을 만들면 인터넷에 액세스할 수 있는 공용 끝점을 통해서가 아니라 조직의 네트워크 개인 IP 주소 공간 내에서 저장소 동기화 서비스 리소스의 주소를 지정할 수 있습니다. 전용 끝점을 직접 만드는 경우에는 공용 끝점을 사용 하지 않도록 설정 하지 않습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[개인 끝점을 사용 하 여 Azure 파일 동기화 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |표시 된 저장소 동기화 서비스 리소스에 대해 개인 끝점을 배포 합니다. 이렇게 하면 인터넷에 액세스할 수 있는 공용 끝점을 통해서가 아니라 조직의 네트워크 개인 IP 주소 공간 내에서 저장소 동기화 서비스 리소스의 주소를 지정할 수 있습니다. 하나 이상의 전용 끝점이 있는 경우에는 공용 끝점을 사용 하지 않도록 설정 하지 않습니다. |DeployIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[수정-공용 네트워크 액세스를 사용 하지 않도록 Azure 파일 동기화 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |조직의 정책에 따라 Azure 파일 동기화의 인터넷 액세스 가능 공용 끝점이 사용 하지 않도록 설정 됩니다. 개인 끝점을 통해 저장소 동기화 서비스에 계속 액세스할 수 있습니다. |수정, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[Azure 파일 동기화에 대 한 공용 네트워크 액세스를 사용 하지 않도록 설정 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |공용 끝점을 사용 하지 않도록 설정 하면 조직의 네트워크에서 승인 된 개인 끝점으로 향하는 요청에 대해 저장소 동기화 서비스 리소스에 대 한 액세스를 제한할 수 있습니다. 공용 끝점에 대 한 요청을 허용 하는 것에 대해서는 기본적으로 안전 하지 않지만 규정, 법률 또는 조직 정책 요구 사항을 충족 하기 위해 사용 하지 않도록 설정할 수 있습니다. 리소스의 incomingTrafficPolicy를 AllowVirtualNetworksOnly로 설정 하 여 저장소 동기화 서비스에 대 한 공용 끝점을 사용 하지 않도록 설정할 수 있습니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
