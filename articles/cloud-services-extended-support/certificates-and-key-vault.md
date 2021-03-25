---
title: Azure Cloud Services(추가 지원)에서 인증서 저장 및 사용
description: Azure Cloud Services에서 인증서를 저장 하 고 사용 하는 프로세스 (확장 된 지원)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4d771e77fcca05b090e5d47d70ae93ece8f79e3e
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865706"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>Azure Cloud Services에서 인증서 사용 (확장 지원)

Key Vault는 Cloud Services(추가 지원)에 연결된 인증서를 저장하는 데 사용됩니다. 키 자격 증명 모음은 [Azure Portal](../key-vault/general/quick-create-portal.md) 및 [PowerShell](../key-vault/general/quick-create-powershell.md)을 통해 만들 수 있습니다. Key Vault에 인증서를 추가한 다음 서비스 구성 파일에서 인증서 지문을 참조 합니다. 또한 Cloud Services(추가 지원) 리소스가 Key Vault에서 비밀로 저장된 인증서를 검색할 수 있도록 Key Vault를 적절한 권한에 사용하도록 설정해야 합니다.  

## <a name="upload-a-certificate-to-key-vault"></a>Key Vault에 인증서 업로드 

1.  Azure Portal에 로그인 하 여 Key Vault 이동 합니다. Key Vault 설정 되어 있지 않은 경우 동일한 창에서 하나를 만들도록 선택할 수 있습니다.

2. **액세스 정책** 선택

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="이미지는 key vault 블레이드에서 액세스 정책 선택을 보여 줍니다.":::

3. 액세스 정책에 다음 속성이 포함 되어 있는지 확인 합니다.
    - **배포를 위해 Azure Virtual Machines에 대 한 액세스 사용**

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="이미지 Azure Portal의 액세스 정책 창을 표시 합니다.":::
 
4.  **인증서** 선택 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="이미지는 Azure Portal의 key vault 블레이드 정책 창에서 인증서 옵션을 선택 하는 것을 보여 줍니다.":::

5. **생성/가져오기** 선택

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="생성/가져오기 옵션을 선택 하는 이미지가 표시 됩니다.":::

4.  인증서 업로드를 완료 하는 데 필요한 정보를 입력 합니다. 인증서가에 있어야 **합니다. PFX** 형식입니다.

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="이미지 Azure Portal의 가져오기 창을 보여 줍니다.":::

5.  서비스 구성 (.cscfg) 파일에서 역할에 인증서 세부 정보를 추가 합니다. Azure Portal 인증서의 지문이 서비스 구성 (.cscfg) 파일의 지문과 일치 하는지 확인 합니다. 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```
6.  ARM 템플릿을 통해 배포 하는 경우 비밀 식별자로 레이블이 지정 된 key vault의 인증서로 이동 하 여 certificateUrl을 찾을 수 있습니다.

    :::image type="content" source="media/certs-and-key-vault-6.png" alt-text="이미지 키 자격 증명 모음에 비밀 식별자 필드를 표시 합니다.":::

## <a name="next-steps"></a>다음 단계 
- Cloud Services (확장 지원)에 대 한 [배포 필수 구성 요소](deploy-prerequisite.md) 를 검토 합니다.
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.md)을 검토합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.
