---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 036bcca08af001a517c28b52277c07d44d636381
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513241"
---
|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS에서 모든 리소스가 API 앱에 액세스하도록 허용해서는 안 됩니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |CORS(교차 원본 리소스 공유)는 API 앱에 액세스하는 모든 도메인을 허용하지 않아야 합니다. 필요한 도메인만 API 앱과 상호 작용할 수 있도록 허용합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |
|[CORS에서 모든 리소스가 함수 앱에 액세스하도록 허용해서는 안 됩니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |CORS(교차 원본 리소스 공유)는 함수 앱에 액세스하는 모든 도메인을 허용하지 않아야 합니다. 필요한 도메인만 함수 앱과 상호 작용할 수 있도록 허용합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |
|[CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용해서는 안 됩니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |CORS(교차 원본 리소스 공유)는 웹 애플리케이션에 액세스하는 모든 도메인을 허용하지 않아야 합니다. 필요한 도메인만 웹앱과 상호 작용할 수 있도록 허용합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[웹앱에서 '클라이언트 인증서(들어오는 클라이언트 인증서)'가 '켜기'로 설정되어 있는지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |클라이언트 인증서는 앱이 들어오는 요청에 대한 인증서를 요청하도록 허용합니다. 유효한 인증서가 있는 클라이언트만 앱에 연결할 수 있습니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[API Apps에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |원격 디버깅에 API 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[함수 앱에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |원격 디버깅에 함수 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[웹 애플리케이션에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |원격 디버깅에 웹 애플리케이션에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
