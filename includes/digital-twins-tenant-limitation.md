---
author: baanders
description: Azure Digital Twins를 사용하여 테넌트 사이의 제한을 설명하는 파일을 포함하기
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 16684f8c5947f8b6a09a9a785968dabf3e644c18
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589392"
---
결과적으로, Azure Digital Twins API에 대한 요청에는 해당 Azure Digital Twins 인스턴스가 위치한 동일 테넌트의 일부인 사용자 또는 서비스 주체가 필요합니다. Azure Digital Twins 엔드포인트의 악의적인 검색을 방지하기 위해 원래 테넌트 외부에서 액세스 토큰이 있는 요청은 "404 하위 도메인을 찾을 수 없음" 오류 메시지가 반환됩니다. [Azure AD B2B](../articles/active-directory/external-identities/what-is-b2b.md) 협업을 통해 사용자나 서비스 주체가 Azure Digital Twins 데이터 소유자나 Azure Digital Twins 데이터 Reader [역할](../articles/digital-twins/concepts-security.md)을 확보 *했더라도* 해당 오류는 반환됩니다. 