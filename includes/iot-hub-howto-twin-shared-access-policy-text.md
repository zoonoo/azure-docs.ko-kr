---
title: 포함 파일
description: 포함 파일
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: f97ab0a0bb91af90daaf60c5c2c85f0ede56a5e7
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403995"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

이 문서에서는 장치 쌍에 원하는 속성을 추가 하는 백 엔드 서비스를 만든 다음 id 레지스트리를 쿼리하여 그에 따라 업데이트 된 보고 된 속성이 있는 모든 장치를 찾습니다. 서비스에서 장치 쌍의 desired 속성을 수정 하려면 **서비스 연결** 권한이 필요 하며, id 레지스트리를 쿼리하려면 **레지스트리 읽기** 권한이 필요 합니다. 이러한 두 개의 권한만 포함 하는 기본 공유 액세스 정책은 없으므로 만들어야 합니다.
