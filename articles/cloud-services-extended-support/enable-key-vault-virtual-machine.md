---
title: Azure Cloud Services에서 Key Vault VM 확장 적용(확장 지원)
description: Cloud Services에 대해 KeyVault VM 확장 사용(확장 지원)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: shisriva
ms.author: shisriva
ms.reviewer: gachandw
ms.date: 05/12/2021
ms.custom: ''
ms.openlocfilehash: 27a25cbff54acda871530daca455045e5519c936
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110104906"
---
# <a name="apply-the-key-vault-vm-extension-to-azure-cloud-services-extended-support"></a>Azure Cloud Services에 Key Vault VM 확장 적용(확장 지원)

## <a name="what-is-the-key-vault-vm-extension"></a>키 자격 증명 모음 VM 확장은 무엇인가요?
Key Vault VM 확장은 Azure Key Vault에 저장된 인증서의 자동 새로 고침을 제공합니다. 특히 확장은 키 자격 증명 모음에 저장된 인증서에 대해 관찰된 목록을 모니터링하고, 변경 내용이 검색되면 해당 인증서를 검색 및 설치합니다. 자세한 내용은 [Windows용 키 자격 증명 모음 VM 확장](../virtual-machines/extensions/key-vault-windows.md)을 참조하세요.

## <a name="whats-new-in-the-key-vault-vm-extension"></a>키 자격 증명 모음 VM 확장의 새로운 기능
이제 Azure Cloud Services(확장 지원) 플랫폼에서 Key Vault VM 확장이 지원되어 엔드투엔드 인증서를 관리할 수 있습니다. 이제 확장은 구성된 키 자격 증명 모음에서 미리 정의된 폴링 간격으로 인증서를 끌어와서 서비스에서 사용할 수 있도록 설치할 수 있습니다. 

## <a name="how-can-i-leverage-the-key-vault-vm-extension"></a>키 자격 증명 모음 VM 확장을 활용하려면 어떻게 해야 하나요?
다음 자습서는 자격 증명 모음에서 확장을 인증하는 데 도움이 되는 AAD에서 토큰을 가져오기 위해 먼저 자격 증명 모음에서 부트스트랩 인증서를 만들어 PaaSV1 서비스에 키 자격 증명 모음 VM 확장을 설치하는 방법을 보여 줍니다. 인증 프로세스를 설정하고 확장을 설치한 후에는 모든 최신 인증서를 정기적으로 폴링 간격에 따라 자동으로 끌어옵니다. 


## <a name="prerequisites"></a>필수 구성 요소 
Azure Key Vault VM 확장을 사용하려면 Azure Active Directory 테넌트가 있어야 합니다. 새 Active Directory 테넌트 설정에 대한 자세한 내용은 [AAD 테넌트 설정](../active-directory/develop/quickstart-create-new-tenant.md)을 참조하세요.

## <a name="enable-the-azure-key-vault-vm-extension"></a>Azure Key Vault VM 확장을 사용하도록 설정

1. 자격 증명 모음에서 [인증서를 생성](../key-vault/certificates/create-certificate-signing-request.md)하고 해당 인증서에 대한 .cer을 다운로드합니다.

2. [Azure Portal](https://portal.azure.com)에서 **앱 등록** 으로 이동합니다.
    
    :::image type="content" source="media/app-registration-0.jpg" alt-text="포털에서 선택한 앱 등록을 보여 줍니다.":::
    

3. 앱 등록 페이지에서 페이지 왼쪽 상단에 있는 **새 등록** 을 선택합니다.
    
    :::image type="content" source="media/app-registration-1.png" alt-text="Azure Portal에서 앱 등록을 표시합니다.":::

4. 다음 페이지에서 양식을 채우고 앱 만들기를 완료할 수 있습니다.

5. 인증서의 .cer을 Azure Active Directory 앱 포털에 업로드합니다.

    - 선택적으로 [키 자격 증명 모음 Event Grid 알림 기능](https://azure.microsoft.com/updates/azure-key-vault-event-grid-integration-is-now-available/)을 활용하여 인증서를 업로드할 수도 있습니다.  

6. Key Vault Azure Active Directory 앱 비밀 목록/get 권한을 부여합니다.
    - RBAC 미리 보기를 사용하는 경우 생성한 AAD 앱의 이름을 검색하여 키 자격 증명 모음 비밀 사용자(미리 보기) 역할에 할당합니다.
    - 자격 증명 모음 액세스 정책을 사용하는 경우 생성한 AAD 앱에 **Secret-Get** 권한을 할당합니다. 자세한 내용은 [액세스 정책 할당](../key-vault/general/assign-access-policy-portal.md)을 참조하세요.

7. 아래와 같이 ARM 템플릿을 사용하여 첫 번째 단계에서 만든 인증서의 첫 번째 버전과 키 자격 증명 모음 VM 확장을 설치합니다.

    ```json
        {
       "osProfile":{
          "secrets":[
             {
                "sourceVault":{
                   "id":"[parameters('sourceVaultValue')]"
                },
                "vaultCertificates":[
                   {
                      "certificateUrl":"[parameters('bootstrpCertificateUrlValue')]"
                   }
                ]
             }
          ]
       }{
          "name":"KVVMExtensionForPaaS",
          "properties":{
             "type":"KeyVaultForPaaS",
             "autoUpgradeMinorVersion":true,
             "typeHandlerVersion":"1.0",
             "publisher":"Microsoft.Azure.KeyVault",
             "settings":{
                "secretsManagementSettings":{
                   "pollingIntervalInS":"3600",
                   "certificateStoreName":"My",
                   "certificateStoreLocation":"LocalMachine",
                   "linkOnRenewal":false,
                   "requireInitialSync":false, 
                   "observedCertificates":"[parameters('keyVaultObservedCertificates']"
                },
                "authenticationSettings":{
                   "clientId":"Your AAD app ID",
                   "clientCertificateSubjectName":"Your boot strap certificate subject name [Do not include the 'CN=' in the subject name]"
                }
             }
          }
       }
    ```
    다음과 같이 ServiceDefinition.csdef에서 부트스트랩 인증서에 대한 인증서 저장소를 지정해야 할 수 있습니다.
    
    ```xml
        <Certificates>
                 <Certificate name="bootstrapcert" storeLocation="LocalMachine" storeName="My" />
        </Certificates> 
    ```

## <a name="next-steps"></a>다음 단계
[클라우드 서비스(확장 지원)에서 모니터링을 사용하도록 설정하여 배포를 더욱 개선합니다.](enable-alerts.md)