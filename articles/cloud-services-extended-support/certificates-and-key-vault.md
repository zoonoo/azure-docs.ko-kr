---
title: Azure Cloud Services(추가 지원)에서 인증서 저장 및 사용
description: Azure Cloud Services에서 인증서를 저장하고 사용하는 프로세스(추가 지원)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 83c455d6a8c952e8f4258dbb90d1d6726e8fcd65
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447916"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>Azure Cloud Services(추가 지원)에서 인증서 사용

Key Vault는 Cloud Services(추가 지원)에 연결된 인증서를 저장하는 데 사용됩니다. 키 자격 증명 모음은 [Azure Portal](../key-vault/general/quick-create-portal.md) 또는 [PowerShell](../key-vault/general/quick-create-powershell.md)을 통해 만들 수 있습니다. 인증서를 Key Vault에 추가한 다음, 서비스 구성 파일에서 인증서 지문을 참조합니다. 또한 Cloud Services(추가 지원) 리소스가 Key Vault에서 비밀로 저장된 인증서를 검색할 수 있도록 Key Vault를 적절한 권한에 사용하도록 설정해야 합니다.  

## <a name="upload-a-certificate-to-key-vault"></a>Key Vault에 인증서를 업로드합니다 

1.  Azure Portal에 로그인하고, Key Vault로 이동합니다. Key Vault 설정되어 있지 않은 경우 동일한 창에서 하나를 만들도록 선택할 수 있습니다.

2. **액세스 정책** 선택

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="이미지는 키 자격 증명 모음 액블레이드에서의 액세스 정책 선택을 보여줍니다.":::

3. 액세스 정책에 다음 속성이 포함되어 있는지 확인합니다.
    - **배포를 위해 Azure Virtual Machines에 대한 액세스 사용**

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="이미지는 Azure Portal의 액세스 정책 창을 표시합니다.":::
 
4.  **인증서** 를 선택합니다 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="이미지는 Azure Portal의 키 자격 증명 모음 블레이드 정책 창에서 인증서 옵션을 선택하는 것을 보여줍니다.":::

5. **생성/가져오기** 를 선택합니다

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="생성/가져오기 옵션을 선택하는 이미지가 표시됩니다":::

4.  인증서 업로드를 완료하는 데 필요한 정보를 입력합니다. 인증서는 **.PFX** 형식이어야 합니다.

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="이미지는 Azure Portal의 가져오기 창을 보여줍니다.":::

5.  서비스 구성(.cscfg) 파일에서 역할에 인증서 세부 정보를 추가합니다. Azure Portal 인증서의 지문이 서비스 구성(.cscfg) 파일의 지문과 일치하는지 확인합니다. 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```
6.  ARM 템플릿을 통한 배포는 certificateUrl은 비밀 식별자로 레이블이 지정된 키 자격 증명 모음의 인증서로 이동하여 확인할 수 있습니다

    :::image type="content" source="media/certs-and-key-vault-6.png" alt-text="이미지는 키 자격 증명 모음에 비밀 식별자 필드를 표시합니다.":::

## <a name="next-steps"></a>다음 단계 
- Cloud Services(추가 지원)에 대한 [배포 필수 구성 요소](deploy-prerequisite.md)를 검토하고 관련 리소스를 만듭니다.
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.yml)을 검토합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.
