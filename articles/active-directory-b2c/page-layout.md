---
title: 페이지 레이아웃 버전
titleSuffix: Azure AD B2C
description: 사용자 지정 정책에서 UI 사용자 지정에 대한 페이지 레이아웃 버전 기록입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3d0cb06f84fdd96d099e05f55ba62c37cb1192c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183978"
---
# <a name="page-layout-versions"></a>페이지 레이아웃 버전

페이지 레이아웃 패키지는 페이지 요소의 수정 사항 및 개선 사항을 포함하도록 주기적으로 업데이트됩니다. 다음 변경 로그는 각 버전에 도입된 변경 내용을 지정합니다.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- 자체 어설션`selfasserted`된 페이지 ()
  - 사용자 지정 정책의 [디스플레이 컨트롤에](display-controls.md) 대한 지원이 추가되었습니다.

## <a name="120"></a>1.2.0

- 모든 페이지
  -  접근성 수정
  - 이제 HTML 태그에 `data-preload="true"` 속성을 추가하여 CSS 및 JavaScript의 로드 순서를 제어할 수 [있습니다.](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content)
    - 연결된 CSS 파일을 HTML 템플릿과 동시에 로드하여 파일을 로드하는 동안 '깜박임'되지 않습니다.
    - 페이지로드 전에 태그를 `script` 가져오고 실행되는 순서를 제어합니다.
  - 이메일 필드는 `type=email` 지금이며 모바일 키보드는 올바른 제안을 제공 할 것입니다
  - 크롬 번역지원
- 통합 및 자체 어설션 페이지
  - 이제 사용자 이름/전자 메일 및 `form` 암호 필드는 HTML 요소를 사용하여 Edge 및 인터넷 탐색기(IE)가 이 정보를 올바르게 저장할 수 있도록 합니다.

## <a name="110"></a>1.1.0

- 예외 페이지(전역 예외)
  - 접근성 수정
  - 정책에서 연락처가 없는 경우 기본 메시지를 제거했습니다.
  - 기본 CSS 제거됨
- MFA 페이지(다단계)
  - '코드 확인' 버튼 제거
  - 이제 코드의 입력 필드는 입력을 최대 6자까지만 사용합니다.
  - 단추를 클릭할 필요 없이 6자리 코드를 입력할 때 입력된 코드를 자동으로 확인하려고 페이지가 자동으로 시도합니다.
  - 코드가 잘못되면 입력 필드가 자동으로 지워집니다.
  - 잘못된 코드를 사용하여 세 번 시도한 후 B2C는 오류를 다시 사용 당사자에게 보냅니다.
  -  접근성 수정
  - 기본 CSS 제거됨
- 자체 어설션 된 페이지(자체 어설션)
  - 제거된 취소 경고
  - 오류 요소에 대한 CSS 클래스
  - 오류 표시/숨기기 로직 개선
  - 기본 CSS 제거됨
- 통합 SSP(통합)
  - 추가 된 나 (KMSI) 컨트롤에 로그인 유지

## <a name="100"></a>1.0.0

- 초기 릴리스

## <a name="next-steps"></a>다음 단계

사용자 지정 정책에서 응용 프로그램의 사용자 인터페이스를 사용자 지정하는 방법에 대한 자세한 내용은 [사용자 지정 정책을 사용하여 응용 프로그램의 사용자 인터페이스 사용자 지정을 참조하세요.](custom-policy-ui-customization.md)
