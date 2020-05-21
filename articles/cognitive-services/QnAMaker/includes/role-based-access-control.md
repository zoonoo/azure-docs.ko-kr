---
title: 파일 포함
description: 포함 파일
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: b34d3ed2481600cde7ffb3275d635b8a57926bd6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83666011"
---
공동 작업을 위해 제공 되는 역할은 다음과 같습니다.

|역할|기능|API 액세스|API 사용 권한|
|--|--|--|--|
|소유자|모두|인증 키|모두|
|참가자|역할에 새 멤버를 추가 하는 기능을 제외한 모든 기능|인증 키|역할에 새 멤버를 추가 하는 기능을 제외한 모든 기능|
|QnA Maker 읽기<br>읽음|내보내기/다운로드<br>테스트|전달자 토큰|1. KB API 다운로드<br>2. 사용자 API에 대 한 Kb 나열<br>3. 기술 자료 정보 가져오기<br>4. 변경 사항을 다운로드 합니다.<br>답변 생성 |
|QnA Maker 편집기<br>(읽기/쓰기)|내보내기/다운로드<br>테스트<br>업데이트 KB<br>내보내기 KB<br>가져오기 KB<br>KB 대체<br>기술 자료 만들기|전달자 토큰|1. KB API 만들기<br>2. KB API 업데이트<br>3. KB API를 바꿉니다.<br>4. 변경 내용 바꾸기<br>5. "교육 API" [새 서비스 모델 v5]|
|인지 서비스 사용자<br>(읽기/쓰기/게시)|모두|전달자 토큰|모두|