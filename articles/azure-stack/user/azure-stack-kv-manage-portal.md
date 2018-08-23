---
title: 포털을 사용 하 여 Azure Stack에서 Key Vault 관리 | Microsoft Docs
description: 포털을 사용 하 여 Azure Stack에서 Key Vault를 관리 하는 방법 알아보기
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: sethm
ms.openlocfilehash: 91035f84d02810d838127ecf6a2f6424ef5df6cf
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139624"
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>포털을 사용 하 여 Azure Stack에서 Key Vault를 관리 합니다.

Azure Stack 포털을 사용 하 여 Azure Stack의 주요 자격 증명 모음을 관리할 수 있습니다. 이 문서에서는 만들기 및 Azure Stack에서 key vault 관리를 시작 합니다.

## <a name="prerequisites"></a>필수 조건

Azure Key Vault 서비스를 포함 하는 제품을 구독 해야 합니다.

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

1. 에 로그인 합니다 [사용자 포털](https://portal.local.azurestack.external)합니다.

2. 대시보드에서 선택 **새로 만들기** > **보안 + Id** > **Key Vault**합니다.

    ![Key Vault 화면](media/azure-stack-kv-manage-portal/image1.png)

3. 에 **Key Vault 만들기** 창 할당을 **이름** 자격 증명 모음에 대 한 합니다. 자격 증명 모음 이름은 영숫자 및 특수 문자 하이픈 (-)만 포함할 수 있습니다. 숫자로 시작 해서는 안 됩니다.

4. 선택 된 **구독** 사용 가능한 구독 목록에서. Key Vault 서비스를 제공 하는 모든 구독 드롭다운 목록에 표시 됩니다.

5. 기존 선택 **리소스 그룹** 하거나 새로 만듭니다.

6. 선택 된 **가격 책정 계층**합니다.
    >[!NOTE]
    > Azure Stack 개발 키트 지원에서 자격 증명 모음 키 **표준** Sku만 합니다.

7. 기존 중 하나를 선택 **액세스 정책** 하거나 새로 만듭니다. 액세스 정책은 사용 하면이 자격 증명이 모음을 사용 하 여 작업을 수행 하는 사용자, 응용 프로그램 또는 보안 그룹에 대 한 권한을 부여할 수 있습니다.

8. 필요한 경우는 **고급 액세스 정책** 기능에 액세스할 수 있도록 합니다. 예를 들어: Resource Manager 템플릿 배포 및 볼륨 암호화에 대 한 Azure Disk Encryption에 대 한 액세스에 대 한 배포용 가상 머신 (Vm).

9. 선택 된 설정을 구성한 후 **확인**를 선택한 후 **만들기**합니다. 이 키 자격 증명 모음 배포를 시작합니다.

## <a name="manage-keys-and-secrets"></a>키 및 비밀 관리

자격 증명 모음을 만든 후 다음 단계를 사용 하 여를 만들고 키 및 자격 증명 모음 내에서 비밀을 관리 합니다.

### <a name="create-a-key"></a>키 만들기

1. 에 로그인 합니다 [사용자 포털](https://portal.local.azurestack.external)합니다.

2. 대시보드에서 선택 **모든 리소스**, 앞에서 만든 키 자격 증명 모음을 선택 하 고 다음을 선택 합니다 **키** 바둑판식으로 배열 합니다.

3. 에 **키** 창 **추가**합니다.

4. 에 **키를 만듭니다** 창의 목록에서 **옵션**, 키를 만드는 데 사용할 방법을 선택 합니다. 할 수 있습니다 **생성** 새 키를 **업로드** 기존 키를 사용할지 **백업 복원** 키의 백업을 선택 합니다.

5. 입력 한 **이름을** 넌 트 키에 대 한 합니다. 키 이름에는 영숫자 및 특수 문자 하이픈 (-) 포함 될 수 있습니다.

6. 필요에 따라 구성 합니다 **활성화 날짜 설정** 및 **만료 날짜 설정** 넌 트 키에 대 한 값입니다.

7. 선택 **만들기** 배포를 시작 합니다.

키를 성공적으로 만들어지면 아래를 선택할 수 있습니다 **키** 를 보거나 해당 속성을 수정 합니다. 속성 섹션을 포함 합니다 **키 식별자**, 되는 리소스 URI (Uniform Identifier)이이 키에 액세스 하려면 외부 응용 프로그램을 사용 하는 합니다. 이 키에 대 한 작업을 최소화 하려면 구성 설정을 **허용 된 작업**합니다.

![키 URI](media/azure-stack-kv-manage-portal/image4.png)

### <a name="create-a-secret"></a>비밀 만들기

1. 에 로그인 합니다 [사용자 포털](https://portal.local.azurestack.external)합니다.
2. 대시보드에서 선택 **모든 리소스**, 앞에서 만든 키 자격 증명 모음을 선택 하 고 다음을 선택 합니다 **비밀** 타일입니다.

3. 아래 **비밀**를 선택 **추가**합니다.

4. 아래 **암호를 만듭니다**, 목록에서 **업로드 옵션**, 암호를 만들려고 할 수 있는 옵션을 선택 합니다. 암호를 만들 수 있습니다 **수동으로** 비밀 또는 업로드에 대 한 값을 입력 하는 경우를 **인증서** 로컬 컴퓨터에서.

5. 입력 한 **이름을** 비밀에 대 한 합니다. 비밀 이름은 영숫자 문자 및 특수 문자가 하이픈 (-)를 포함할 수 있습니다.

6. 필요에 따라 지정 합니다 **콘텐츠 형식**, 및 구성에 대 한 값 **활성화 날짜 설정** 및 **만료 날짜 설정** 비밀에 대 한 합니다.

7. 선택 **만들기** 배포를 시작 합니다.

암호를 성공적으로 만들어지면 아래를 선택할 수 있습니다 **비밀** 를 보거나 해당 속성을 수정 합니다. 합니다 **비밀 식별자** 외부 응용 프로그램이이 비밀에 액세스 하는 데 사용할 수 있는 URI입니다.

![URI 암호](media/azure-stack-kv-manage-portal/image5.png)

## <a name="next-steps"></a>다음 단계

* [Key Vault에 저장 된 암호를 검색 하 여 VM 배포](azure-stack-kv-deploy-vm-with-secret.md)
* [Key Vault에 저장 된 인증서를 사용 하 여 VM 배포](azure-stack-kv-push-secret-into-vm.md)
