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
ms.openlocfilehash: 9e69b4e9279f9147c2ee13d42a42aec0c5a15d96
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744477"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>Azure Cloud Services에서 인증서 사용 (확장 지원)

Key Vault은 Cloud Services (확장 지원)에 연결 된 인증서를 저장 하는 데 사용 됩니다. 키 자격 증명 모음은 [Azure Portal](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal) 및 [PowerShell](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell)을 통해 만들 수 있습니다. Key Vault에 인증서를 추가한 다음 서비스 구성 파일에서 인증서 지문을 참조 합니다. 또한 Cloud Services (확장 된 지원) 리소스가 Key Vault에서 암호로 저장 된 인증서를 검색할 수 있도록 적절 한 사용 권한에 대 한 Key Vault를 사용 하도록 설정 해야 합니다.  

## <a name="upload-a-certificate-to-key-vault"></a>Key Vault에 인증서 업로드 

1.  Azure Portal에 로그인 하 여 Key Vault 이동 합니다. Key Vault 설정 되어 있지 않은 경우 동일한 창에서 하나를 만들도록 선택할 수 있습니다.

2. **액세스 정책** 선택

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="이미지는 key vault 블레이드에서 액세스 정책 선택을 보여 줍니다.":::

3. 액세스 정책에 다음 속성이 포함 되어 있는지 확인 합니다.
    - **배포를 위해 Azure Virtual Machines에 대 한 액세스 사용**
    - **템플릿 배포 Azure Resource Manager에 대 한 액세스 사용** 

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="이미지 Azure Portal의 액세스 정책 창을 표시 합니다.":::
 
4.  **인증서** 선택 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="이미지는 Azure Portal의 key vault 블레이드 정책 창에서 인증서 옵션을 선택 하는 것을 보여 줍니다.":::

5. **생성/가져오기** 선택

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="생성/가져오기 옵션을 선택 하는 이미지가 표시 됩니다.":::

4.  인증서 업로드를 완료 하는 데 필요한 정보를 입력 합니다. 

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="이미지 Azure Portal의 가져오기 창을 보여 줍니다.":::

5.  서비스 구성 (.cscfg) 파일에서 역할에 인증서 세부 정보를 추가 합니다. Azure Portal 인증서의 지문이 서비스 구성 (.cscfg) 파일의 지문과 일치 하는지 확인 합니다. 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```

## <a name="next-steps"></a>다음 단계 
- Cloud Services (확장 지원)에 대 한 [배포 필수 구성 요소](deploy-prerequisite.md) 를 검토 합니다.
- Cloud Services (확장 지원)에 대 한 질문과 [대답](faq.md) 을 검토 합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용 하 여 클라우드 서비스 (확장 지원)를 배포 합니다.