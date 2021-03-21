---
title: FPGA 2101 릴리스 정보를 사용 하 여 Edge Pro Azure Stack | Microsoft Docs
description: Azure Stack Edge 2101 릴리스에 대 한 중요 한 미해결 문제 및 해결 방법을 설명 합니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/01/2021
ms.author: alkohli
ms.openlocfilehash: 5c95da8d6ee88786ff983b4963c1cb96394886cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727552"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>FPGA 2101 릴리스 정보를 사용 하 여 Edge Pro Azure Stack

다음 릴리스 정보는 FPGA (기본 제공 필드 프로그래밍 가능 게이트 배열)를 사용 하는 Azure Stack Edge Pro의 2101 릴리스에 대 한 중요 한 미해결 문제 및 해결 된 문제를 식별 합니다.

릴리스 정보는 지속적으로 업데이트 됩니다. 해결 방법이 필요한 중요 한 문제가 발견 되 면 추가 됩니다. Azure Stack Edge 장치를 배포 하기 전에 릴리스 정보의 정보를 주의 깊게 검토 하세요.  

이 릴리스는 소프트웨어 버전에 해당 합니다.

- **Azure Stack Edge 2101 (1.6.1475.2528)** -KB 4599267

> [!NOTE]
> 업데이트 2101는 GA (일반 공급) 버전의 소프트웨어를 실행 하는 장치에만 적용할 수 있습니다.

## <a name="whats-new"></a>새로운 기능

이 릴리스에 다음 버그 수정이 포함되어 있습니다.

- **업로드 문제** -이 릴리스에서는 업로드 문제를 해결 합니다 .이 릴리스에서는 오류로 인해 업로드가 다시 시작 되 면 업로드 완료 속도가 느려질 수 있습니다. 이 문제는 주로 대역폭 제한이 활성 상태인 경우에만 사용 가능한 대역폭을 기준으로 크고 제한적이 지 않은 파일로 구성 된 데이터 집합을 업로드할 때 발생할 수 있습니다. 이렇게 변경 하면 지정 된 파일에 대 한 업로드를 다시 시작 하기 전에 업로드를 완료할 수 있는 충분 한 기회가 생깁니다.

이 릴리스에는 다음과 같은 업데이트도 포함 되어 있습니다.

- 모든 누적 Windows 업데이트 및 .NET framework 업데이트는 10 월 2020까지 출시 되었습니다.
- 공장 설치 중에 BMC (베이스 보드 관리 컨트롤러) 펌웨어 버전이 3.32.32.32에서 3.36.36.36로 업그레이드 되어 최신 Dell 전원 공급 장치를 사용 하는 비 호환성 문제를 해결 합니다.
- 이 릴리스는 Azure Stack에 지 장치에 대 한 IoT Edge 1.0.9.3을 지원 합니다.

## <a name="known-issues-in-this-release"></a>이 릴리스의 알려진 문제

이 릴리스에는 발표 된 새로운 문제가 없습니다. 발표 된 모든 릴리스 문제는 이전 릴리스에서 수행 되었습니다. 알려진 문제 목록을 보려면 [GA 릴리스의 알려진 문제](../databox-gateway/data-box-gateway-release-notes.md#known-issues-in-ga-release)로 이동 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge 배포 준비](../databox-online/azure-stack-edge-deploy-prep.md)