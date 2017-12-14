---
title: "Azure Active Directory의 Cloud App Discovery를 사용하여 관리되지 않는 클라우드 응용 프로그램 찾기 | Microsoft Docs"
description: "Cloud App Discovery를 사용한 응용 프로그램 찾기 및 관리, 이점 및 작동 방식에 대한 정보를 제공합니다."
services: active-directory
keywords: "Cloud App Discovery, 응용 프로그램 관리"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 5875cf4cc0938607fa1ff6adf840d513d1fb85bd
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>Cloud App Discovery를 사용하여 관리되지 않는 클라우드 응용 프로그램 찾기
## <a name="summary"></a>요약

Cloud App Discovery는 Azure Active Directory Premium의 기능으로 조직의 사람들이 사용하는 관리되지 않는 클라우드 응용 프로그램을 검색할 수 있도록 합니다. 오늘날 기업에서 IT 부서가 해당 조직 멤버가 일을 위해 사용하고 있는 클라우드 앱을 일부 인지하지 못하는 경우가 있습니다. 관리자가 회사 데이터에 대한 무단 액세스, 데이터 유출 가능성 및 기타 보안 위험에 대해 우려하는 원인을 쉽게 확인합니다. 인식이 부족하여 이러한 보안 위험을 다루는 계획 수립이 어려울 수 있습니다.

> [!TIP] 
> [Microsoft Cloud App Security와의 통합](https://portal.cloudappsecurity.com)으로 향상된 Azure AD(Azure Active Directory)의 Cloud App Discovery에 대한 개선 사항을 확인해 보세요.

**클라우드 앱 검색으로 다음을 할 수 있습니다.**

* 사용되고 있는 클라우드 응용 프로그램을 찾고 사용자 수, 트래픽 볼륨 또는 응용 프로그램에 대한 웹 요청 수로 사용 현황을 측정합니다.
* 응용 프로그램을 사용하는 사용자를 식별합니다.
* 오프라인 분석을 위해 데이터를 내보냅니다.
* IT 제어에서 이러한 응용 프로그램을 가져오고 사용자 관리에 대해 Single Sign-On을 사용합니다.

## <a name="how-it-works"></a>작동 방법
1. 응용 프로그램 사용 에이전트는 사용자의 컴퓨터에 설치됩니다.
2. 에이전트에 의해 캡처되는 응용 프로그램 사용 정보는 암호화 채널로 클라우드 앱 검색 서비스에 보안 전송됩니다.
3. 클라우드 앱 검색 서비스는 데이터를 평가하고 보고서를 생성합니다.

![클라우드 앱 검색 다이어그램](./media/active-directory-cloudappdiscovery/cad01.png)


## <a name="next-steps"></a>다음 단계
* [클라우드 앱 보안 및 개인정보 취급 방침 고려 사항](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [사용자 지정 포트를 사용하는 프록시 서버용 클라우드 앱 검색 레지스트리 설정](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [클라우드 앱 검색 에이전트 Changelog ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)

