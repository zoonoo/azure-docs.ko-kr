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
ms.date: 01/05/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 1d0ad06fc7eec07f8e1e0ba47121b6eec01c87df
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2018
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>Cloud App Discovery를 사용하여 관리되지 않는 클라우드 응용 프로그램 찾기
## <a name="summary"></a>요약

Azure Active Directory의 Cloud App Discovery는 이제 Microsoft Cloud App Security에서 제공하는 개선된 에이전트 없는 검색 환경을 제공합니다. Cloud App Discovery를 사용하려면 Azure AD Premium P1 자격 증명으로 로그인하기만 하면 됩니다. 이 업데이트는 모든 Azure AD Premium P1 고객이 추가 비용 없이 사용할 수 있습니다. [Azure ad에서 Cloud App Discovery 설정](https://docs.microsoft.com/azure/active-directory/cloudappdiscovery-get-started) 문서부터 시작한 후 [Microsoft Cloud App Security](https://portal.cloudappsecurity.com/)를 시도하세요.

> [!IMPORTANT] 
> 에이전트 기반 검색을 제공하는 현재 Azure AD Cloud App Discovery 환경은 2018년 3월 5일에 해제될 예정이며, 이후부터 에이전트는 비활성화되고 데이터는 삭제됩니다. 서비스 중단을 방지하려면 3월 5일 이전에 조치를 취하여 새 환경을 작동하세요.  
 
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

