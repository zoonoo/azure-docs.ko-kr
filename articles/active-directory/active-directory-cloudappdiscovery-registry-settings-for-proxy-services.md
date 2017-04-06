---
title: "프록시 서비스용 클라우드 앱 검색 레지스트리 설정 | Microsoft Docs"
description: "이 항목의 목표는 클라우드 앱 검색 에이전트를 실행하는 컴퓨터에 필요한 포트를 설정하기 위해 수행해야 하는 단계를 제공하는 것입니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8d78e925-e331-40ba-904a-e4ef14260cac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 6adb1dd25c24b18b834dd921c2586ef29d56dc81
ms.openlocfilehash: 7f2a3c4bf8fda9dd235986be8b0ceaa8eb8313e0
ms.lasthandoff: 12/29/2016


---
# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>프록시 서비스용 클라우드 앱 검색 레지스트리 설정
클라우드 앱 검색 에이전트는 기본적으로 80 또는 443 포트만 사용하도록 구성됩니다. 사용자 지정 포트(80이나 443이 아닌)를 사용하는 프록시 서버가 있는 환경에 클라우드 앱 검색을 설치하고자 한다면 이 포트를 사용하도록 에이전트를 구성해야 합니다. 구성은 레지스트리 키를 기반으로 합니다.

이 항목의 목표는 클라우드 앱 검색 에이전트를 실행하는 컴퓨터에 필요한 포트를 설정하기 위해 수행해야 하는 단계를 제공하는 것입니다.

**클라우드 앱 검색 에이전트를 실행하는 컴퓨터에서 사용하는 포트를 수정하려면 다음 단계를 수행합니다.**

1. 레지스트리 편집기를 엽니다. <br> ![실행](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)
2. 다음 레지스트리 키로 이동하거나 만듭니다. <br> **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud App Discovery\Endpoint** 
3. **포트**라고 불리는 새 **다중 문자열**을 만듭니다. ![새로 만들기](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)
4. **다중 문자열 편집** 대화 상자를 열려면 해당 포트 값을 두 번 클릭합니다.
5. 값 데이터 상자에 다음 값을 입력하고 조직에서 사용되는 모든 사용자 지정 포트를 추가합니다. <br><br>
   **80** <br>
   **8080** <br>
   **8118** <br>
   **8888** <br>
   **81** <br>
   **12080** <br>
   **6999** <br>
   **30606** <br>
   **31595** <br>
   **4080** <br>
   **443** <br>
   **1110** <br><br>
   ![다중 문자열 편집](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)
6. **확인**을 클릭하여 **다중 문자열 편집** 대화 상자를 닫습니다.

**추가 리소스**

* [조직 내에서 사용되고 있는 허용되지 않은 클라우드 앱을 검색하는 방법](active-directory-cloudappdiscovery-whatis.md) 


