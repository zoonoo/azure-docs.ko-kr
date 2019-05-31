---
title: Azure 앱 구성 FAQ | Microsoft Docs
description: Azure 앱 구성에 대 한 질문과 대답
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: e321c0b473b110597b5b87a6e67666737116daa2
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393341"
---
# <a name="azure-app-configuration-faq"></a>Azure 앱 구성 FAQ

이 문서에서는 Azure App 구성에 대 한 질문과 대답을 다룹니다.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>App Configuration은 Azure Key Vault와 어떻게 다른가요?

앱 구성 사용 사례의 고유 집합 용인지: 개발자가 응용 프로그램 설정을 관리 하 고 기능 가용성을 제어할 수 있도록 합니다. 다양 한 복잡 한 구성 데이터를 사용 하는 작업을 단순화 하려고 합니다.

앱 구성을 지원합니다.

- 계층적 네임 스페이스
- 레이블 지정
- 광범위 한 쿼리
- 일괄 검색
- 특수 한 관리 작업
- 기능 관리 사용자 인터페이스

앱 구성에는 Key vault로 보완 되며 대부분의 응용 프로그램 배포의 두를 함께 사용 해야 합니다.

## <a name="should-i-store-secrets-in-app-configuration"></a>앱 구성에서 비밀 정보를 저장 해야 합니까?

앱 구성을 강화 된 보안을 제공 하지만 Key Vault가 여전히 응용 프로그램 비밀을 저장 하기 위한 가장 좋은 위치입니다. 키 자격 증명 모음에는 하드웨어 수준 암호화, 세분화 된 액세스 정책 및 인증서 회전 등의 관리 작업을 제공합니다.

## <a name="does-app-configuration-encrypt-my-data"></a>앱 구성 않습니다 내 데이터를 암호화?

예. 앱 구성 보유 하는 모든 키 값을 암호화 하 고 네트워크 통신 암호화 키를 누릅니다. 키 이름에는 암호화 되지 않습니다 및 구성 데이터를 검색 하는 것에 대 한 인덱스로 사용 됩니다.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>여러 환경 (테스트, 스테이징, 프로덕션, 및 등)에 대 한 구성을 저장 해야 어떻게 해야 하나요?

현재 저장소 수준에서 앱 구성에 대 한 액세스 권한이 있는 사용자를 제어할 수 있습니다. 다른 권한이 필요한 각 환경에 대 한 별도 저장소를 사용 합니다. 이 방법은 최상의 보안 격리를 제공합니다.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>앱 구성을 사용 하도록 권장 되는 방법은 무엇 인가요?

참조 [모범 사례](./howto-best-practices.md)합니다.

## <a name="how-much-does-app-configuration-cost"></a>앱 구성의 비용은 얼마 인가요?

서비스는 공개 미리 보기 중에 사용할 수 있습니다.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>문제를 보고 하거나 제안을 제공 수 어떻게 하나요?

에 직접 연결할 수 있습니다 [GitHub](https://github.com/Azure/AppConfiguration/issues)합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 앱 구성에 대 한](./overview.md)
