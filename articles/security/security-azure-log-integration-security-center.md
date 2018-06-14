---
title: Azure 로그 통합 및 Security Center | Microsoft Docs
description: 로그 통합과 함께 사용하여 Azure Security Center의 경고를 받는 방법을 알아봅니다.
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/29/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 9acce21d544a43adcd0c0983771c02c6bb39caec
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
ms.locfileid: "29138481"
---
# <a name="how-to-get-your-security-center-alerts-in-azure-log-integration"></a>Azure 로그 통합에서 Security Center 경고를 받는 방법

이 문서에서는 Azure 로그 통합 서비스가 Azure Security Center에서 생성된 보안 경고 정보를 가져오는 데 필요한 단계를 제공합니다. 이 문서의 단계를 수행하기 전에 [시작](security-azure-log-integration-get-started.md) 문서의 단계를 완료해야 합니다.

## <a name="detailed-steps"></a>자세한 단계

다음 단계는 필수 Azure Active Directory 서비스 주체를 만들고 서비스 주체 읽기 권한을 구독에 할당합니다.
1. 명령 프롬프트를 열고 **c:\Program Files\Microsoft Azure Log Integration**으로 이동합니다.
2. ``azlog createazureid`` 명령 실행

    이 명령은 사용자에게 Azure 로그인을 요구합니다. 그런 다음 로그인한 사용자가 관리자, 공동 관리자 또는 소유자인 Azure 구독을 호스트하는 Azure AD 테넌트에 [Azure Active Directory 서비스 주체](../active-directory/develop/active-directory-application-objects.md) 를 만듭니다. 로그인한 사용자가 Azure AD 테넌트의 게스트 사용자이면 명령이 실패합니다. Azure에 대한 인증은 Azure Active Directory(AD)를 통해 수행됩니다. Azlog 통합에 대한 서비스 주체를 만들면 Azure 구독을 읽을 수 있는 Azure AD ID가 생성됩니다.

3. 다음으로 구독에 대한 읽기 권한자 액세스 권한을 방금 만든 서비스 주체에 할당하는 명령을 실행합니다. SubscriptionID를 지정하지 않으면 이 명령은 사용자가 액세스 권한을 갖고 있는 모든 구독에 서비스 주체 읽기 관리자 역할을 할당하려고 시도합니다. </br></br>
``azlog authorize <SubscriptionID>`` </br> 예제: </br>
``azlog authorize 0ee55555-0bc4-4a32-a4e8-c29980000000``

    >[!NOTE]
    ```createazureid``` 명령을 실행한 직후 authorize 명령을 실행하면 경고가 표시될 수 있습니다. Azure AD 계정이 생성되는 시점과 계정이 사용 가능하게 되는 시점 사이에 약간의 대기 시간에 있습니다. ```createazureid``` 명령을 실행하고 약 60초 대기한 후 authorize 명령을 실행하면 이러한 경고가 표시되지 않습니다.

4. 다음 폴더에서 감사 로그 JSON 파일이 있는지 확인하세요.
 * **c:\Users\azlog\AzureResourceManagerJson**
 * **c:\Users\azlog\AzureResourceManagerJsonLD** </br></br>
5. 다음 폴더에서 보안 센터 경고가 있는지 확인하세요.</br></br>
 * **c:\Users\azlog\AzureSecurityCenterJson**
 * **c:\Users\azlog\AzureSecurityCenterJsonLD** </br></br>

설치 및 구성 중에 문제가 발생하면 [지원 요청](/azure-supportability/how-to-create-azure-support-request.md)을 열고 **로그 통합**을 지원을 요청하는 서비스로 선택합니다.

## <a name="next-steps"></a>다음 단계
Azure 로그 통합에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 로그에 대한 Microsoft Azure 로그 통합](https://www.microsoft.com/download/details.aspx?id=53324) – Azure 로그 통합에 대한 세부 정보, 시스템 요구 사항 및 설치 지침을 다운로드할 수 있습니다.
* [Azure 로그 통합 소개](security-azure-log-integration-overview.md) - 이 문서에서는 Azure 로그 통합, 주요 기능 및 작동 원리를 소개합니다.
* [파트너 구성 단계](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – 이 블로그 게시물에서는 Splunk, HP ArcSight, IBM QRadar 등의 파트너 솔루션과 함께 작동하도록 Azure 로그 통합을 구성하는 방법을 보여줍니다.
* [Azure 로그 통합 FAQ(질문과 대답)](security-azure-log-integration-faq.md) - 이 FAQ는 Azure 로그 통합에 대한 질문에 답변합니다.
* [Azure 진단 및 Azure 감사 로그를 위한 새 기능](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – 이 블로그 게시물에서는 Azure 리소스 운영에 대한 정보 수집에 도움이 되는 Azure 감사 로그 및 기타 기능에 대해 소개합니다.
