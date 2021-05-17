---
title: Azure Data Catalog 문제 해결 방법
description: 이 문서에서는 Azure Data Catalog 리소스에 일반적인 문제를 해결하는 방법을 설명합니다.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 201a7d1fce00323ef3f8eec1bdd415c4064d49ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674991"
---
# <a name="troubleshooting-azure-data-catalog"></a>Azure Data Catalog 문제 해결

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

이 문서에서는 Azure Data Catalog 리소스에 일반적인 문제를 해결하는 방법을 설명합니다. 

## <a name="functionality-limitations"></a>기능 제한 사항

Azure Data Catalog를 사용할 때 제한되는 기능은 다음과 같습니다.

- **게스트 역할** 유형의 계정은 지원되지 않습니다. 게스트 계정은 Azure Data Catalog 사용자로 추가할 수 없으며, 게스트 사용자는 [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com)에서 포털을 사용할 수 없습니다.

- Azure Resource Manager 템플릿 또는 Azure PowerShell 명령을 사용하여 Azure Data Catalog 리소스 만들기는 지원되지 않습니다.

- Azure 테넌트 간에 Azure Data Catalog 리소스를 이동할 수 없습니다.

## <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory 정책 구성

Azure Data Catalog 포털에 로그인할 수 있는 상황이 발생할 수 있지만, 데이터 원본 등록 도구에 로그인을 시도할 때 로그인하지 않도록 하는 오류 메시지가 발생합니다. 이 오류는 회사 네트워크에 있거나 회사 네트워크 외부에서 연결 시 발생할 수 있습니다.

등록 도구가 *폼 인증* 을 사용하여 Azure Active Directory에 대한 사용자 로그인의 유효성 검사를 수행합니다. 로그인이 성공하려면 Azure Active Directory 관리자가 *전역 인증 정책* 에서 폼 인증을 사용할 수 있도록 해야 합니다.

전역 인증 정책을 사용하면 다음 이미지에 나와 있는 것처럼 인트라넷 및 엑스트라넷 연결에 대해 개별 인증을 사용하도록 설정할 수 있습니다. 연결되어 있는 네트워크에 대해 폼 인증을 사용할 수 없는 경우 로그인 오류가 발생할 수 있습니다.

 ![Azure Active Directory 전역 인증 정책](./media/troubleshoot-policy-configuration/global-auth-policy.png)

자세한 내용은 [인증 정책 구성](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11))을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Data Catalog 만들기](data-catalog-get-started.md)
