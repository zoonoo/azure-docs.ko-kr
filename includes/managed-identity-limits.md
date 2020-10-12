---
title: 포함 파일
description: 포함 파일
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: dc7849e52b3e00c87d271ee8aef8788d816dff7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971041"
---
- 각 관리 ID는 [Azure AD 서비스 한도 및 제한 사항](../articles/active-directory/users-groups-roles/directory-service-limits-restrictions.md)에 설명된 대로 Azure AD 테넌트의 개체 할당량 한도 계산에 포함됩니다.
-   관리 ID를 만들 수 있는 속도에는 다음과 같은 한도가 있습니다.

    1. Azure 지역별 Azure AD 테넌트당: 20초당 200개의 만들기 작업
    2. Azure 지역별 Azure 구독당: 20초당 40개의 만들기 작업

- 사용자 할당 관리 ID를 만드는 경우, 영숫자 문자(0-9, a-z, A-Z) 및 하이픈(-)만 지원됩니다. 가상 머신 또는 가상 머신 확장 집합에 대한 할당이 제대로 작동하려면 이름이 24자로 제한됩니다.
