---
title: 파일 포함
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8c5c0c8f649e7cbab2c16688717de1aaabfb93c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75477149"
---
이 참조는 Azure Storage의 확장성 및 성능 목표에 대해 자세히 설명 합니다. 여기에 나열된 확장성 및 성능 목표는 최첨단 목표이지만 달성할 수 있습니다. 모든 경우, 계정 사용량에 따라 달성된 요청 속도 및 대역폭은 스토리지된 개채의 크기 및 애플리케이션이 수행한 작업 형태에 따릅니다.

서비스를 테스트 하 여 성능이 요구 사항을 충족 하는지 확인 해야 합니다. 가능하면 트래픽 속도가 갑자기 증가하지 않고 파티션 간의 트래픽이 적절하게 분산되도록 보장합니다.

애플리케이션이 파티션의 작업 처리 가능한 제한에 도달하면 Azure Storage는 오류 코드 503(서버 작업 중) 또는 오류 코드 500(작업 시간 제한) 응답을 반송하기 시작합니다. 503 오류가 발생하는 경우 재시도를 위해 지수 백오프 정책을 사용하도록 애플리케이션을 수정하는 것이 좋습니다. 지수 백오프는 파티션에 대한 부하를 감소시키고 해당 파티션에 트래픽의 급증을 완화할 수 있습니다.
