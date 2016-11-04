
---
title: 엔터프라이즈 통합 팩에서 인증서 사용 | Microsoft Docs
description: 엔터프라이즈 통합 팩 및 Logic Apps에서 인증서를 사용하는 방법 알아보기
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: deonhe

---
# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>인증서 및 엔터프라이즈 통합 팩에 대해 알아보기
## <a name="overview"></a>개요
엔터프라이즈 통합에서는 인증서를 사용하여 B2B 통신을 보호합니다. 엔터프라이즈 통합 앱에서 두 가지 종류의 인증서를 사용할 수 있습니다.

* 공용 인증서는 CA(인증 기관)에서 구입해야 합니다
* 개인 인증서는 직접 발행할 수 있습니다. 이러한 인증서를 자체 서명된 인증서라고도 합니다.

## <a name="what-are-certificates?"></a>인증서란?
인증서는 전자 통신에서 참가자의 ID를 확인하고 전자 통신을 보호하는 디지털 문서입니다.

## <a name="why-use-certificates?"></a>인증서를 사용하는 이유
경우에 따라 B2B 통신을 기밀로 유지해야 합니다. 엔터프라이즈 통합에서는 인증서를 사용하여 다음 두 가지 방법으로 이러한 통신을 보호합니다.

* 메시지의 콘텐츠를 암호화하여
* 메시지에 디지털로 서명하여  

## <a name="how-do-you-upload-certificates?"></a>인증서 업로드 방법
### <a name="public-certificates"></a>공용 인증서
B2B 기능이 포함된 논리 앱에서 *공용 인증서* 를 사용하려면 먼저 통합 계정에 인증서를 업로드해야 합니다. B2B 기능이 포함된 논리 앱에서 *자체 서명된 인증서*를 사용하려면 먼저 인증서를 [Azure Key Vault](../key-vault/key-vault-get-started.md "주요 자격 증명 모음에 대해 알아보기")에 업로드해야 합니다.

인증서를 업로드한 후에는 사용자가 만드는 [규약](app-service-logic-enterprise-integration-agreements.md) 에서 해당 속성을 정의할 때 인증서를 사용하여 B2B 메시지를 안전하게 보호할 수 있습니다.  

Azure Portal에 로그인한 후 통합 계정에 공용 인증서를 업로드하는 자세한 단계는 다음과 같습니다.

1. **찾아보기**를 선택합니다.  
    ![찾아보기 선택](./media/app-service-logic-enterprise-integration-overview/overview-1.png)  
2. 필터 검색 상자에 **통합**을 입력한 다음 결과 목록에서 **통합 계정**을 선택합니다.     
    ![통합 계정 선택](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. 인증서를 추가할 통합 계정을 선택합니다.  
    ![인증서를 추가할 통합 계정 선택](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. **인증서** 타일을 선택합니다.  
   ![인증서 타일 선택](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)
5. 열린 **인증서** 블레이드에서 **추가** 단추를 선택합니다.
    ![추가 단추 선택](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. 인증서에 **이름**을 입력한 다음 인증서 유형을 선택합니다. (이 예제에서는 공용 인증서 유형을 사용했습니다.) 그런 다음 **인증서** 텍스트 상자의 오른쪽에서 폴더 아이콘을 선택합니다.
7. 파일 선택기가 열리면 통합 계정에 업로드하려는 인증서 파일을 선택합니다.
8. 인증서를 선택한 다음 파일 선택기에서 **확인** 을 선택합니다. 그러면 인증서의 유효성을 검사하고 통합 계정에 업로드합니다.
9. 마지막으로, 다시 **인증서 추가** 블레이드로 돌아와서 **확인** 단추를 선택합니다.  
    ![확인 단추 선택](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)  
10. 약 1분 후에 인증서 업로드가 완료되었음을 알리는 알림이 표시됩니다.
11. **인증서** 타일을 선택합니다. 새로 추가된 인증서가 보일 것입니다.  
    ![새 인증서가 보임](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)  

### <a name="private-certificates"></a>개인 인증서
다음 단계를 수행하여 통합 계정에 개인 인증서를 업로드할 수 있습니다.  

1. [개인 키를 Key Vault에 업로드](../key-vault/key-vault-get-started.md "주요 자격 증명 모음에 대해 알아보기")에 업로드해야 합니다.  
   
   > [!TIP]
   > Azure App Service의 Logic Apps 기능이 Key Vault에서 작업을 수행할 수 있도록 권한을 부여해야 합니다. 다음 PowerShell 명령을 사용하여 Logic Apps 서비스 주체에 대한 액세스 권한을 부여할 수 있습니다. `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  
   > 
   > 
2. 개인 인증서를 만듭니다.  
3. 개인 인증서를 통합 계정에 업로드합니다.

위의 단계를 완료했으면 개인 인증서를 사용하여 규약을 만들 수 있습니다.

Azure Portal에 로그인한 후 통합 계정에 개인 인증서를 업로드하는 자세한 단계는 다음과 같습니다.  

1. **찾아보기**를 선택합니다.  
    ![개인 인증서를 통합 계정에 업로드](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. 필터 검색 상자에 **통합**을 입력한 다음 결과 목록에서 **통합 계정**을 선택합니다.     
    ![통합 계정 선택](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. 인증서를 추가할 통합 계정을 선택합니다.  
    ![인증서를 추가할 통합 계정 선택](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. **인증서** 타일을 선택합니다.  
    ![인증서 타일 선택](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)  
5. 열린 **인증서** 블레이드에서 **추가** 단추를 선택합니다.
    ![추가 단추 선택](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. 인증서에 **이름**을 입력한 다음 인증서 유형을 선택합니다. (이 예제에서는 공용 인증서 유형을 사용했습니다.) 그런 다음 **인증서** 텍스트 상자의 오른쪽에서 폴더 아이콘을 선택합니다.
7. 파일 선택기가 열리면 통합 계정에 업로드하려는 인증서 파일을 선택합니다.
8. 인증서를 선택한 후에 파일 선택기에서 **확인** 을 선택합니다. 이 작업은 인증서의 유효성을 검사하고 인증서를 통합 계정에 업로드합니다.
9. 마지막으로, 다시 **인증서 추가** 블레이드로 돌아와서 **확인** 단추를 선택합니다.  
    ![인증서 추가](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)  
10. 약 1분 후에 인증서 업로드가 완료되었음을 알리는 알림이 표시됩니다.
11. **인증서** 타일을 선택합니다. 새로 추가된 인증서가 보일 것입니다.
    ![새 인증서가 보임](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)  

인증서를 업로드한 후에는 사용자가 [규약](app-service-logic-enterprise-integration-agreements.md)에서 해당 속성을 정의할 때 인증서를 사용하여 B2B 메시지를 안전하게 보호할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
* [B2B 기능을 사용하는 논리 앱 만들기](app-service-logic-enterprise-integration-b2b.md)  
* [B2B 규약 만들기](app-service-logic-enterprise-integration-agreements.md)  
* [Key Vault에 대해 자세히 알아보기](../key-vault/key-vault-get-started.md "주요 자격 증명 모음에 대해 알아보기")  

<!--HONumber=Oct16_HO2-->


