---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 44bb56296edafaf8cac51ed182a8344a0945ff29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599591"
---
|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure SignalR 서비스는 공용 네트워크 액세스를 사용 하지 않도록 설정 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a9766a-82a5-4747-abb5-650b6dbba6d0) |Azure SignalR Service 리소스의 보안을 강화 하기 위해 공용 인터넷에 노출 되지 않고 개인 끝점 에서만 액세스할 수 있는지 확인 합니다. 에 설명 된 대로 공용 네트워크 액세스 속성을 사용 하지 않도록 설정 [https://aka.ms/asrs/networkacls](https://aka.ms/asrs/networkacls) 합니다. 이 옵션은 Azure IP 범위 외부의 모든 공용 주소 공간에서 액세스를 사용 하지 않도록 설정 하 고 IP 또는 가상 네트워크 기반 방화벽 규칙과 일치 하는 모든 로그인을 거부 합니다. 이로 인해 데이터 누출 위험이 줄어듭니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PublicNetworkAccessDisabled_AuditDeny.json) |
|[Azure SignalR Service는 개인 링크를 사용 하도록 설정 된 SKU를 사용 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F464a1620-21b5-448d-8ce6-d4ac6d1bc49a) |Azure 개인 링크를 사용 하면 공용 IP 주소를 사용 하지 않고 가상 네트워크를 Azure 서비스에 연결 하 여 공용 데이터 유출 위험 으로부터 리소스를 보호할 수 있습니다. 정책은 Azure SignalR Service에 대 한 개인 링크 사용 Sku로 제한 됩니다. 개인 링크에 대 한 자세한 내용은을 (를) 확인 [https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink) 하세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_AllowedSKU_AuditDeny.json) |
|[Azure SignalR Service는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스 대신 개인 끝점을 Azure SignalR Service 리소스에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. 개인 링크에 대 한 자세한 내용은을 (를) 확인 [https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink) 하세요. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
|[Azure SignalR Service 리소스를 수정 하 여 공용 네트워크 액세스 사용 안 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F62a3ae95-8169-403e-a2d2-b82141448092) |Azure SignalR Service 리소스의 보안을 강화 하기 위해 공용 인터넷에 노출 되지 않고 개인 끝점 에서만 액세스할 수 있는지 확인 합니다. 에 설명 된 대로 공용 네트워크 액세스 속성을 사용 하지 않도록 설정 [https://aka.ms/asrs/networkacls](https://aka.ms/asrs/networkacls) 합니다. 이 옵션은 Azure IP 범위 외부의 모든 공용 주소 공간에서 액세스를 사용 하지 않도록 설정 하 고 IP 또는 가상 네트워크 기반 방화벽 규칙과 일치 하는 모든 로그인을 거부 합니다. 이로 인해 데이터 누출 위험이 줄어듭니다. |수정, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PublicNetworkAccessDisabled_Modify.json) |
