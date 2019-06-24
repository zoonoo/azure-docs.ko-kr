---
title: Azure Data Catalog를 해결 하는 방법
description: 이 문서에서는 Azure 데이터 카탈로그 리소스에 대 한 일반적인 문제 해결 문제를 설명 합니다.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 06/13/2019
ms.openlocfilehash: ed74e90e5e8ed55b75968f51cb50e6a1b4cdd75d
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203499"
---
# <a name="troubleshooting-azure-data-catalog"></a>Azure Data Catalog를 문제 해결

이 문서에서는 Azure 데이터 카탈로그 리소스에 대 한 일반적인 문제 해결 문제를 설명 합니다. 

## <a name="functionality-limitations"></a>기능 제한 사항

Azure Data Catalog를 사용 하면 다음 기능이 제한 됩니다.

- 형식 사용 하 여 계정을 **게스트 역할** 지원 되지 않습니다. Azure Data Catalog의 사용자가 게스트 계정을 추가할 수 없습니다 및 게스트 사용자에 게 www.azuredatacatalog.com에서 포털을 사용할 수 없습니다.

- Azure Resource Manager 템플릿 또는 Azure PowerShell 명령을 사용 하 여 Azure 데이터 카탈로그 리소스를 만들 수 없습니다.

- Azure 데이터 카탈로그 리소스는 Azure 테 넌 트 간에 이동할 수 없습니다.

## <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory 정책 구성

Azure Data Catalog 포털에 로그인할 수 있는 상황이 발생할 수 있지만, 데이터 원본 등록 도구에 로그인을 시도할 때 로그인하지 않도록 하는 오류 메시지가 발생합니다. 이 오류는 회사 네트워크에 있는 경우 또는 회사 네트워크 외부에서 연결 하는 경우에 발생할 수 있습니다.

등록 도구가 *폼 인증* 을 사용하여 Azure Active Directory에 대한 사용자 로그인의 유효성 검사를 수행합니다. 로그인이 성공하려면 Azure Active Directory 관리자가 *전역 인증 정책*에서 폼 인증을 사용할 수 있도록 해야 합니다.

전역 인증 정책을 사용하면 다음 이미지에 나와 있는 것처럼 인트라넷 및 엑스트라넷 연결에 대해 개별 인증을 사용하도록 설정할 수 있습니다. 폼 인증 연결 되어 있는 네트워크에 사용할 수 없는 경우 로그인 오류가 발생할 수 있습니다.

 ![Azure Active Directory 전역 인증 정책](./media/troubleshoot-policy-configuration/global-auth-policy.png)

자세한 내용은 [인증 정책 구성](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11))을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 카탈로그를 만들려면](data-catalog-get-started.md)
