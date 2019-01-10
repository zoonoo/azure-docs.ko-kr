---
title: Azure Stack 저장소 서비스 주체 자격 증명 키 자격 증명 모음에 | Microsoft Docs
description: 키 자격 증명 모음 Azure Stack에서 서비스 주체 자격 증명을 저장 하는 방법에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2019
ms.author: sethm
ms.openlocfilehash: 570c1adc2f4615e78cbe5656c13b0e22b863baf7
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192521"
---
# <a name="store-service-principal-credentials-in-key-vault"></a>Key Vault에 서비스 주체 자격 증명 저장

서비스 주체 만들기 및 배포 하기 전에 인증 자격 증명을 사용 하 여 일반적으로 Azure Stack에서 응용 프로그램 개발에 필요 합니다. 그러나 종종 서비스 주체에 대 한 저장된 된 자격 증명 위치가 됩니다. 이 문서에서는 서비스 주체를 만들고 나중에 검색에 대 한 Azure Key Vault에 값을 저장 하는 방법을 설명 합니다.

Key Vault에 대 한 자세한 내용은 참조 하세요. [이 문서에서는](azure-stack-key-vault-intro.md)합니다.

## <a name="prerequisites"></a>필수 조건

- 구독에서 Azure Key Vault 서비스를 포함 하는 제품입니다.
- PowerShell은 Azure Stack과 함께 사용 하 여 구성 됩니다.

## <a name="key-vault-in-azure-stack"></a>Azure Stack의 주요 자격 증명 모음

암호화 키를 보호 하기 위해 Azure Stack에서 Key Vault를 통해 및 클라우드 응용 프로그램 및 서비스는 암호를 사용 합니다. Key Vault를 사용 하 여 키 및 암호를 암호화할 수 있습니다.

Key vault를 만들려면 다음이 단계를 수행 합니다.

1. Azure Stack 포털에 로그인 합니다.

2. 대시보드에서 선택 **+ 리소스 만들기**, 한 다음 **보안 + Id**를 선택 하 고 **키 자격 증명 모음입니다.**

   ![주요 자격 증명 모음 만들기](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. 에 **Key Vault 만들기** 창 할당을 **이름** 자격 증명 모음에 대 한 합니다. 자격 증명 모음 이름은 영숫자 및 하이픈 (-) 문자만 포함할 수 있습니다. 숫자로 시작 하지 않아야 합니다.

4. 사용 가능한 구독 목록에서 구독을 선택합니다.

5. 기존 리소스 그룹을 선택 하거나 새로 만듭니다.

6. 가격 책정 계층을 선택 합니다.

7. 기존 액세스 정책 중 하나를 선택 하거나 새로 만듭니다. 액세스 정책은 사용 하면이 자격 증명이 모음을 사용 하 여 작업을 수행 하는 사용자, 응용 프로그램 또는 보안 그룹에 대 한 권한을 부여할 수 있습니다.

8. 필요에 따라 기능에 대 한 액세스를 사용 하도록 설정 하는 고급 액세스 정책을 선택 합니다.

9. 선택 된 설정을 구성한 후 **확인**를 선택한 후 **만들기**합니다. 키 자격 증명 모음 배포가 시작 됩니다.

## <a name="create-a-service-principal"></a>서비스 주체 만들기

1. Azure portal 통해 Azure 계정에 로그인 합니다.

2. 선택 **Azure Active Directory**, 한 다음 **앱 등록**, 한 다음 **추가**합니다.

3. 애플리케이션에 대한 이름 및 URL을 제공합니다. 만들려는 애플리케이션 유형으로 **웹앱/API** 또는 **네이티브**를 선택합니다. 값을 설정한 후 **만들기**를 선택합니다.

4. 선택 **Active Directory**, 한 다음 **앱 등록**, 응용 프로그램을 선택 합니다.

5. **응용 프로그램 ID**를 복사하고 응용 프로그램 코드에 저장합니다. 샘플 응용 프로그램의 응용 프로그램 사용 **클라이언트 ID** 참조할 때 합니다 **응용 프로그램 ID**합니다.

6. 인증 키를 생성하려면 **키**를 선택합니다.

7. 키 및 기간에 대 한 설명을 제공 합니다.

8. **저장**을 선택합니다.

9. 복사 합니다 **키** 는 사용할 수 클릭 한 후 **저장**합니다.

## <a name="store-the-service-principal-inside-key-vault"></a>Key Vault 내에서 서비스 주체를 저장 합니다.

1. Azure Stack에 대 한 사용자 포털에 로그인 하 고 이전에 만든 키 자격 증명 모음 선택을 선택 합니다 **비밀** 타일입니다.

2. 에 **비밀** 창 **생성/가져오기**합니다.

3. 에 **암호를 만듭니다** 창의 옵션 선택 목록에서 **수동**합니다.

4. 입력 **응용 프로그램 ID** 넌 트 키에 대 한 이름으로 서비스 주체에서 복사 합니다. 키 이름에는 영숫자 및 하이픈 (-) 문자만 포함할 수 있습니다.

5. 서비스 주체에에서 키의 값을 **값** 탭 합니다.

6. 선택 **서비스 주체** 에 대 한 합니다 **콘텐츠 형식**합니다.

7. 설정 된 **활성화 날짜** 및 **만료일** 넌 트 키에 대 한 값입니다.

8. 선택 **만들기** 배포를 시작 합니다.

암호를 성공적으로 만들어지면 서비스 주체 정보를 거기에 저장 됩니다. 언제 든 지 아래에서 선택할 수 있습니다 **비밀**, 및 확인 하거나 해당 속성을 수정 합니다. 속성 섹션을 리소스 URI (Uniform Identifier) 외부 응용 프로그램이이 비밀에 액세스 하는 데 사용 되는 비밀 식별자를 포함 합니다.

## <a name="next-steps"></a>다음 단계

- [서비스 주체를 사용 합니다.](azure-stack-create-service-principals.md)
- [포털에서 Azure Stack에서 Key Vault를 관리 합니다.](azure-stack-key-vault-manage-portal.md)  
- [PowerShell을 사용 하 여 Azure Stack에서 Key Vault를 관리 합니다.](azure-stack-key-vault-manage-powershell.md)