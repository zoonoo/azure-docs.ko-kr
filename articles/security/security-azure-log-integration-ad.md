---
title: "AZLOG(Azure 로그 통합) 및 Active Directory 감사 로그 | Microsoft Docs"
description: "Azure 로그 통합 서비스를 설치하고 Azure 감사 로그의 로그를 통합하는 방법에 대해 알아봅니다."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/08/2017
ms.author: barclayn
ms.custom: azlog
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 9a4fa6ea1784ab0247659d5498c6fe3ae30355c4
ms.contentlocale: ko-kr
ms.lasthandoff: 08/09/2017

---

#<a name="integrate-azure-active-directory-audit-logs"></a>Azure Active Directory 감사 로그 통합

Azure Active Directory 감사 이벤트를 통해 Azure Active Directory에서 발생한 권한 있는 작업을 식별할 수 있습니다. [Azure Active Directory 감사 보고서 이벤트](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md)를 검토하여 추적할 수 있는 이벤트 유형을 확인할 수 있습니다.

>[!NOTE]
이 문서의 단계를 시도하기 전에 먼저 [시작하기](security-azure-log-integration-get-started.md) 문서를 검토하고 해당 단계를 완료해야 합니다.

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Azure Active Directory 감사 로그 통합 단계

1. 명령 프롬프트를 열고 **c:\Program Files\Microsoft Azure Log Integration**으로 cd합니다.
2. 명령 실행:

 ``azlog createazureid``

 이 명령은 사용자에게 Azure 로그인을 요구합니다. 그런 다음 로그인한 사용자가 관리자, 공동 관리자 또는 소유자인 Azure 구독을 호스트하는 Azure AD 테넌트에 Azure Active Directory 서비스 주체를 만듭니다. 로그인한 사용자가 Azure AD 테넌트의 게스트 사용자이면 명령이 실패합니다. Azure에 대한 인증은 Azure Active Directory(AD)를 통해 수행됩니다. Azlog 로그 통합에 대한 서비스 주체를 만들면 Azure 구독을 읽을 수 있는 Azure AD ID가 생성됩니다.

3. tenantID를 제공하는 명령을 실행합니다. 이 명령을 실행하려면 테넌트 관리자 역할의 구성원이어야 합니다.

``Azlog.exe authorizedirectoryreader tenantId``

예제

``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

다음 폴더에서 Azure Active Directory 감사 로그 JSON 파일을 만들었는지 확인합니다.

* **C:\Users\azlog\AzureActiveDirectoryJson**
* **C:\Users\azlog\AzureActiveDirectoryJsonLD**

이 문서에서 설명된 단계를 진행하는 방법에 대한 비디오를 보려면 아래 내용을 참조하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


>[!NOTE]
SIEM으로 JSON 파일에서 정보를 가져오는것과 관련한 특정 지침은 해당 SIEM 공급업체에 문의해야 합니다.

[Azure 로그 통합 MSDN 포럼](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration)을 통해 커뮤니티의 지원을 받을 수 있습니다. 이 포럼은 Azure 로그 통합을 최대한 활용하는 방법에 대한 질문, 답변, 팁, 요령 등 서로 지원할 수 있는 기능을 AzLog 커뮤니티에 제공합니다. 또한 Azure 로그 통합 팀이 이 포럼을 모니터링하며 가능한 한 언제든지 도움을 드릴 것입니다.

[지원 요청](../azure-supportability/how-to-create-azure-support-request.md)을 열 수도 있습니다. 지원 요청을 열려면 지원을 요청하는 서비스로 **로그 통합**을 선택합니다.

## <a name="next-steps"></a>다음 단계
Azure 로그 통합에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 로그에 대한 Microsoft Azure 로그 통합](https://www.microsoft.com/download/details.aspx?id=53324) – Azure 로그 통합에 대한 세부 정보, 시스템 요구 사항 및 설치 지침을 다운로드할 수 있습니다.
* [Azure 로그 통합 소개](security-azure-log-integration-overview.md) - 이 문서에서는 Azure 로그 통합, 주요 기능 및 작동 원리를 소개합니다.
* [파트너 구성 단계](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – 이 블로그 게시물에서는 Splunk, HP ArcSight, IBM QRadar 등의 파트너 솔루션과 함께 작동하도록 Azure 로그 통합을 구성하는 방법을 보여줍니다.
* [Azure 로그 통합 FAQ(질문과 대답)](security-azure-log-integration-faq.md) - 이 FAQ는 Azure 로그 통합에 대한 질문에 답변합니다.
* [보안 센터 경고를 Azure 로그 통합과 통합](../security-center/security-center-integrating-alerts-with-log-integration.md) - 이 문서에서는 Azure 진단 및 Azure 감사 로그에 수집된 가상 컴퓨터 보안 이벤트와 함께 보안 센터 경고를 로그 분석 또는 SIEM 솔루션과 동기화하는 방법을 보여줍니다.
* [Azure 진단 및 Azure 감사 로그를 위한 새 기능](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – 이 블로그 게시물에서는 Azure 리소스 운영에 대한 정보 수집에 도움이 되는 Azure 감사 로그 및 기타 기능에 대해 소개합니다.

