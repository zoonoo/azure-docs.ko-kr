---
title: "Azure Portal에서 활동 보고서를 찾는 방법 | Microsoft Docs"
description: "Azure Portal에서 활동 보고서를 찾는 방법을 알아봅니다."
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/17/2017
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: afd48a77faeeab6d5a8730934345cb7000a59831
ms.openlocfilehash: e72e49140aaad55a6e66a9f331ce7fde20b8577c
ms.lasthandoff: 02/23/2017


---
# <a name="how-to-find-activity-reports-in-the-azure-portal"></a>Azure Portal에서 활동 보고서를 찾는 방법

Azure 클래식 포털에서 Azure Portal로 마이그레이션하면 새로운 형태의 Azure Active Directory 활동 로그를 볼 수 있습니다. 몇 달 전에 작업 중인 리소스 컨텍스트로 활동 로그를 제공하는 방법을 설명하는 [블로그 게시물](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/)을 릴리스했습니다. 이 문서에서는 Azure 클래식 포털의 기존 보고서가 어떻게 Azure Portal의 새로운 환경으로 전환되었는지를 설명합니다.

## <a name="what-is-new"></a>새로워진 사항

Azure 클래식 포털의 보고서는 다음과 같은 다양한 범주로 분할됩니다.

1.    보안 보고서
2.    활동 보고서
3.    통합 앱 보고서

### <a name="activity-and-integrated-app-reports"></a>활동 및 통합 앱 보고서

Azure Portal의 컨텍스트 기반 보고 방식으로 전환하면서 기존 보고서는 하나의 기본 API가 데이터를 제공하는 단일 보기로 병합되었습니다. 

이 보기는 **Azure Active Directory** 블레이드의 **활동** 섹션에 있는 **감사 로그** 아래에서 찾을 수 있습니다.


![감사 로그](./media/active-directory-reporting-migration/482.png "감사 로그")








다음은 이 보기에 통합된 보고서입니다.

-    감사 보고서

-     암호 재설정 활동

-     암호 재설정 등록 활동

-     셀프 서비스 그룹 활동

-     Office&365; 그룹 이름 변경

-     계정 프로비전 활동

-     암호 롤오버 상태
-     계정 프로비전 오류


개선된 응용 프로그램 사용 현황 보고서가 **로그인**이라는 보기에 포함되었습니다. 이 보기는 **Azure Active Directory** 블레이드의 **활동** 섹션에서 찾을 수 있습니다.


![감사 로그](./media/active-directory-reporting-migration/483.png "감사 로그")

이 보기에는 응용 프로그램 사용 정보를 가져올 수 있는 모든 사용자의 로그인이 포함되어 있습니다. **엔터프라이즈 응용 프로그램** 개요 화면을 통해서도 애플리케이션 사용 정보를 찾을 수 있습니다.

![감사 로그](./media/active-directory-reporting-migration/484.png "감사 로그")



## <a name="how-can-i-access-a-specific-report-in-this-single-view"></a>이 단일 보기에서 특정 보고서에 액세스하려면 어떻게 해야 하나요?

### <a name="audit-logs"></a>감사 로그

많은 고객들의 핵심적인 요청 중 하나는 Azure AD 내의 활동 로그에 액세스하기 위한 여러 필터 옵션이었습니다. 이를 대신해서 원하는 데이터를 필터링할 수 있는 고급 필터링 메커니즘을 제공하고 있습니다. 제공된 필터 중 하나를 **활동 범주**라고 합니다. 이 필터를 사용하면 Azure AD에서 제공하는 다양한 유형의 활동 로그가 나열됩니다. 원하는 범주를 선택하여 원하는 내용으로 결과를 좁힐 수 있습니다. 

예를 들어 **셀프 서비스 암호 재설정** 관련 활동만 보려면 **셀프 서비스 암호 관리** 범주를 선택할 수 있습니다. 볼 수 있는 범주는 작업 중인 리소스 컨텍스트에 해당합니다.  


![감사 로그](./media/active-directory-reporting-migration/06.png "감사 로그")

 
현재 제공되는 다양한 범주는 다음과 같습니다.

- 핵심 디렉터리

- 셀프 서비스 암호 관리

- 셀프 서비스 그룹 관리

- 계정 프로비전

### <a name="application-usage"></a>응용 프로그램 사용 현황

**활동 -> 로그인** 보기를 통해 모든 앱 또는 단일 앱에 대한 응용 프로그램 사용 현황을 볼 수 있습니다. 아래와 같이 이 보기는 모든 응용 프로그램 또는 단일 응용 프로그램에 대해 존재합니다. 결과의 범위를 좁히려면 **사용자 이름** 또는 **응용 프로그램 이름**을 필터링할 수 있습니다.
 

![감사 로그](./media/active-directory-reporting-migration/07.png "감사 로그")


### <a name="security-reports"></a>보안 보고서

Azure Active Directory에서 검색하고 보고할 수 있는 모든 보안 관련 위험 이벤트 보기의 전체 개요를 제공하도록 보안 보고서가 통합되었습니다. 전체 개요는 [Azure Active Directory 위험 이벤트](active-directory-identity-protection-risk-events.md)를 참조하세요.  
이 항목에서는 [Azure AD 비정상 활동 보고서](active-directory-identity-protection-risk-events.md#azure-ad-anomalous-activity-reports) 섹션에서 Azure Active Directory 비정상 활동 보고서가 Azure AD의 위험 이벤트에 매핑되는 방법에 대한 개요를 확인할 수 있습니다.

Azure Portal에서 **Azure Active Directory** 블레이드의 **보안** 섹션에서 검색된 위험 이벤트에 대한 보고서에 액세스할 수 있습니다. 검색된 위험 이벤트는 다음과 같은 보고서 내에서 추적됩니다.   

- 위험에 노출된 사용자
- 위험한 로그인 


![감사 로그](./media/active-directory-reporting-migration/04.png "감사 로그")

이러한 보고서에 대한 자세한 내용은 다음을 참조하세요.

- [Azure Active Directory 포털 – 미리 보기의 위험에 노출된 사용자 보안 보고서](active-directory-reporting-security-user-at-risk.md)
- [Azure Active Directory 포털 – 미리 보기의 위험한 로그인 보고서](active-directory-reporting-security-risky-sign-ins.md)






## <a name="activity-reports-in-azure-classic-portal-versus-azure-portal"></a>Azure 클래식 포털 및 Azure Portal의 활동 보고서

이 섹션에서는 Azure 클래식 포털의 기존 보고서와 Azure Portal에서 이 정보를 가져오는 방법을 보여 줍니다.


모든 감사된 데이터에 대한 진입점은 **Azure Active Directory** 블레이드의 **활동** 섹션에 있는 **감사 로그**입니다.

![감사 로그](./media/active-directory-reporting-migration/61.png "감사 로그")


| Azure 클래식 포털                 | Azure Portal 단계                                                         |
| ---                                  | ---                                                                        |
| 감사 로그                           | **활동 범주**로 **핵심 디렉터리**를 선택합니다.                       |
| 암호 재설정 활동              | **활동 범주**로 **셀프 서비스 암호 관리**를 선택합니다. | 
| 암호 재설정 등록 활동 | **활동 범주**로 **셀프 서비스 암호 관리**를 선택합니다.     |
| 셀프 서비스 그룹 활동         | **활동 범주**로 **셀프 서비스 그룹 관리**를 선택합니다.        |
| 계정 프로비전 활동        | **활동 범주**로 **계정 사용자 프로비전**을 선택합니다.         |
| 암호 롤오버 상태             | **활동 범주**로 **자동 앱 암호 롤오버**를 선택합니다.      |
| 계정 프로비전 오류          | **활동 범주**로 **계정 사용자 프로비전**을 선택합니다.        |
| Office&365; 그룹 이름 변경         | **활동 범주**로 **셀프 서비스 암호 관리**를 **활동 리소스 종류**로 **그룹**을, **활동 원본**으로 **O365 그룹**을 선택합니다.|

 

**응용 프로그램 사용 현황** 보고서에 대한 진입점은 **Azure Active Directory > 엔터프라이즈 응용 프로그램 > 로그인**입니다. 


![감사 로그](./media/active-directory-reporting-migration/199.png "감사 로그")


