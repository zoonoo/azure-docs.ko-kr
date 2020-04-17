---
title: Azure 포털을 사용하여 고객 관리 키 구성
titleSuffix: Cognitive Services
description: Azure 포털을 사용하여 Azure 키 자격 증명 모음을 사용하여 고객 관리 키를 구성하는 방법을 알아봅니다. 고객 관리 키를 사용하면 액세스 컨트롤을 생성, 회전, 비활성화 및 해지할 수 있습니다.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 04a8a8d2520376931f2bb7727c1e751b83f6315f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455260"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Azure 포털을 사용하여 Azure 키 자격 증명 모음을 사용하여 고객 관리 키 구성

Azure 키 자격 증명 모음을 사용하여 고객 관리 키를 저장해야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장하거나 Azure Key Vault API를 사용하여 키를 생성할 수 있습니다. 코그너티브 서비스 리소스와 키 자격 증명 모음은 동일한 리전과 동일한 Azure Active Directory(Azure AD) 테넌트에 있어야 하지만 다른 구독에 있을 수 있습니다. Azure 키 자격 증명 모음에 대한 자세한 내용은 [Azure 키 자격 증명 모음이란 무엇입니까?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

이 문서에서는 Azure [포털을](https://portal.azure.com/)사용하여 고객 관리 키로 Azure 키 자격 증명 모음을 구성하는 방법을 보여 주며 있습니다. Azure 포털을 사용하여 키 자격 증명 모음을 만드는 방법을 알아보려면 [빠른 시작: Azure 포털을 사용하여 Azure 키 자격 증명 모음에서 비밀 설정 및 검색을](../../key-vault/secrets/quick-create-portal.md)참조하세요.

## <a name="configure-azure-key-vault"></a>Azure Key Vault 구성

고객 관리 키를 사용 하려면 키 자격 증명 **모음에** 두 개의 속성을 설정 해야 합니다. **Do Not Purge** 이러한 속성은 기본적으로 활성화되어 있지 않지만 새 키 또는 기존 키 자격 증명 모음에서 PowerShell 또는 Azure CLI를 사용하여 사용할 수 있습니다.

> [!IMPORTANT]
> **소프트 삭제** 및 삭제 **안 함** 속성을 사용하도록 설정하고 키를 삭제하지 않으면 코그너티브 서비스 리소스의 데이터를 복구할 수 없습니다.

기존 키 자격 증명 모음에서 이러한 속성을 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나에서 **연무 및** **지우기 보호 활성화** 라는 섹션을 참조하십시오.

- [PowerShell에서 소프트 삭제를 사용하는 방법](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [CLI와 소프트 삭제를 사용하는 방법](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli).

크기 2048의 RSA 키만 Azure 저장소 암호화를 통해 지원됩니다. 키에 대한 자세한 내용은 Azure [키 볼트 정보 키, 비밀 및 인증서의](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)키 자격 증명 모음 **키를** 참조하십시오.

## <a name="enable-customer-managed-keys"></a>고객 관리 키 사용

Azure 포털에서 고객 관리 키를 활성화하려면 다음 단계를 따르십시오.

1. 코그너티브 서비스 리소스로 이동합니다.
1. 코그너티브 서비스 리소스의 **설정** 블레이드에서 **암호화**를 클릭합니다. 다음 그림과 같이 고객 관리 키 옵션을 **선택합니다.**

    ![고객 관리 키를 선택하는 방법을 보여주는 스크린샷](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>키 지정

고객 관리 키를 사용하도록 설정하면 코그너티브 서비스 리소스와 연결할 키를 지정할 수 있습니다.

### <a name="specify-a-key-as-a-uri"></a>키를 URI로 지정

키를 URI로 지정하려면 다음 단계를 따르십시오.

1. Azure 포털에서 키 URI를 찾으려면 키 자격 증명 모음으로 이동한 다음 **키** 설정을 선택합니다. 원하는 키를 선택한 다음 키를 클릭하여 해당 버전을 봅니다. 해당 버전의 설정을 보려면 키 버전을 선택합니다.
1. URI를 제공하는 **키 식별자** 필드의 값을 복사합니다.

    ![키 볼트 키 URI를 보여주는 스크린샷](../media/cognitive-services-encryption/key-uri-portal.png)

1. 저장소 계정의 **암호화** 설정에서 **주요 URI 입력** 옵션을 선택합니다.
1. 복사한 URI를 **키 URI** 필드에 붙여넣습니다.

   ![키 URI를 입력하는 방법을 보여주는 스크린샷](../media/cognitive-services-encryption/ssecmk2.png)

1. 키 자격 증명 모음이 포함된 구독을 지정합니다.
1. 변경 내용을 저장합니다.

### <a name="specify-a-key-from-a-key-vault"></a>Key Vault에서 키 지정

키 자격 증명 모음에서 키를 지정하려면 먼저 키가 포함된 키 자격 증명 모음이 있는지 확인합니다. 키 자격 증명 모음에서 키를 지정하려면 다음 단계를 따르십시오.

1. **Key Vault에서 선택** 옵션을 선택합니다.
1. 사용하려는 키가 포함된 키 자격 증명 모음을 선택합니다.
1. 키 자격 증명 모음에서 키를 선택합니다.

   ![고객 관리 키 옵션을 보여주는 스크린샷](../media/cognitive-services-encryption/ssecmk3.png)

1. 변경 내용을 저장합니다.

## <a name="update-the-key-version"></a>키 버전 업데이트

키의 새 버전을 만들 때 새 버전을 사용 하 여 인지 서비스 리소스를 업데이트 합니다. 다음 단계를 수행하세요.

1. 코그너티브 서비스 리소스로 이동하여 **암호화** 설정을 표시합니다.
1. 새 키 버전에 대한 URI를 입력합니다. 또는 키 자격 증명 모음과 키를 다시 선택하여 버전을 업데이트할 수 있습니다.
1. 변경 내용을 저장합니다.

## <a name="use-a-different-key"></a>다른 키 사용

암호화에 사용되는 키를 변경하려면 다음 단계를 따르십시오.

1. 코그너티브 서비스 리소스로 이동하여 **암호화** 설정을 표시합니다.
1. 새 키에 대한 URI를 입력합니다. 또는 키 자격 증명 모음을 선택하고 새 키를 선택할 수 있습니다.
1. 변경 내용을 저장합니다.

## <a name="disable-customer-managed-keys"></a>고객 관리 키 비활성화

고객 관리 키를 사용하지 않도록 설정하면 코그너티브 서비스 리소스가 Microsoft 관리 키로 암호화됩니다. 고객 관리 키를 사용하지 않도록 설정하려면 다음 단계를 따르세요.

1. 코그너티브 서비스 리소스로 이동하여 **암호화** 설정을 표시합니다.
1. 사용자 고유의 키 사용 설정 옆의 확인란을 선택 **취소합니다.**

## <a name="next-steps"></a>다음 단계

* [Azure 키 볼트란?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [코그너티브 서비스 고객 관리 키 요청 양식](https://aka.ms/cogsvc-cmk)
* [미사용 데이터의 얼굴 서비스 암호화](../Face/face-encryption-of-data-at-rest.md)
* [미사용 데이터의 QnA 메이커 암호화](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [미사용 데이터의 언어 이해 서비스 암호화](../LUIS/luis-encryption-of-data-at-rest.md)
* [미사용 데이터의 콘텐츠 중재자 암호화](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [미사용 데이터의 번역기 암호화](../translator/translator-encryption-of-data-at-rest.md)
* [미사용 데이터의 개인 설정 암호화](../personalizer/personalizer-encryption-of-data-at-rest.md)
