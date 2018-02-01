---
title: "Azure AD Connect 동기화: 디렉터리 확장 | Microsoft Docs"
description: "이 항목에서는 Azure AD Connect의 디렉터리 확장 기능을 설명합니다."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 9abd035b13a0d51c534eb8cac50c045012399a69
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect 동기화: 디렉터리 확장
디렉터리 확장을 사용하면 온-프레미스 Active Directory의 사용자 고유 특성을 사용하여 Azure AD에서 스키마를 확장할 수 있습니다. 이 기능을 통해 온-프레미스를 계속 관리하는 특성을 이용하는 LOB 앱을 빌드할 수 있습니다. 이러한 특성은 [Azure AD Graph 디렉터리 확장](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) 또는 [Microsoft Graph](https://graph.microsoft.io/)를 통해 이용할 수 있습니다. 각각 [Azure AD Graph 탐색기](https://graphexplorer.azurewebsites.net/) 및 [Microsoft Graph 탐색기](https://developer.microsoft.com/en-us/graph/graph-explorer)를 통해 사용할 수 있는 특성을 볼 수 있습니다.

현재 이 특성을 이용하는 Office 365 워크로드가 없습니다.

설치 마법사의 사용자 지정 설정 경로에서 동기화할 추가 속성을 구성합니다.
![스키마 확장 마법사](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  
설치 시 다음과 같은 특성이 표시됩니다. 이러한 특성은 유효한 후보입니다.

* 사용자 및 그룹 개체 유형
* 단일 값 특성: 문자열, 부울, 정수, 이진
* 다중 값 특성: 문자열, 이진


>[!NOTE]
> Azure AD Connect는 다중값 AD 특성을 Azure AD에 다중값 디렉터리 확장으로 동기화하는 것을 지원하지만, Azure AD에는 현재 다중값 디렉터리 확장을 지원하는 기능이 없습니다.

특성 목록은 Azure AD Connect 설치 도중에 만들어진 스키마 캐시에서 읽힙니다. 추가 특성을 사용하여 Active Directory 스키마를 확장한 경우 [스키마를 새로 고쳐야](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) 이러한 새 특성을 볼 수 있습니다.

Azure AD의 개체에는 최대 100개의 디렉터리 확장 특성이 있을 수 있습니다. 최대 길이는 250자입니다. 특성 값이 더 긴 경우 동기화 엔진에 의해 잘립니다.

Azure AD Connect를 설치하는 동안 이러한 특성을 사용할 수 있는 응용 프로그램이 등록됩니다. Azure 포털에서 다음 응용 프로그램을 볼 수 있습니다.  
![스키마 확장 앱](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

특성은 extension\_{AppClientId}\_를 접두사로 사용합니다. AppClientId는 Azure AD 테넌트의 모든 특성에 대해 동일한 값을 가집니다.

이제 이러한 특성을 **Azure AD Graph**에서 사용할 수 있습니다.

Azure AD Graph 탐색기를 통해 Azure AD Graph를 쿼리할 수 있습니다: [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)

![그래프](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

또는 **Microsoft Graph API**를 통해 쿼리할 수 있습니다.

Microsoft Graph 탐색기: [https://developer.microsoft.com/en-us/graph/graph-explorer#](https://developer.microsoft.com/en-us/graph/graph-explorer#)를 통해 Microsoft Graph API를 쿼리할 수 있습니다.

>[!NOTE]
> 반환할 특성을 명시적으로 요청해야 합니다. 다음과 같은 특성을 명시적으로 선택하여 이 작업을 수행할 수 있습니다: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division 자세한 내용은 [Microsoft Graph: 쿼리 매개 변수 사용](https://developer.microsoft.com/en-us/graph/docs/concepts/query_parameters#select-parameter)을 확인하세요.

## <a name="next-steps"></a>다음 단계
[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.
