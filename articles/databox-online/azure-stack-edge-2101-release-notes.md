---
title: Azure Stack Edge Pro FPGA 2101 릴리스 정보 | Microsoft Docs
description: Azure Stack Edge Pro FPGA 2101 릴리스의 중요한 미해결 문제 및 해결 방법에 대해 설명합니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/01/2021
ms.author: alkohli
ms.openlocfilehash: 3c8882706bdbb6719dbc2550df350096a4970fd8
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110461225"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>Azure Stack Edge Pro with FPGA 2101 릴리스 정보

다음 릴리스 정보는 기본 제공 FPGA(필드 프로그래머블 게이트 어레이)를 사용하여 Azure Stack Edge Pro FPGA의 2101 릴리스에 대한 주요 미해결 문제 및 해결된 문제를 소개합니다.

릴리스 정보는 지속적으로 업데이트됩니다. 해결 방법이 필요한 중요한 문제가 발견되면 추가됩니다. Azure Stack Edge 디바이스를 배포하기 전에 릴리스 정보에 수록된 정보를 주의 깊게 검토하세요.  

이 릴리스는 다음 소프트웨어 버전에 해당합니다.

- **Azure Stack Edge 2101(1.6.1475.2528)** - KB 4599267

> [!NOTE]
> 업데이트 2101은 GA(일반 공급) 이상 버전의 소프트웨어를 실행하는 디바이스에만 적용할 수 있습니다.

## <a name="whats-new"></a>새로운 기능

이 릴리스에 다음 버그 수정이 포함되어 있습니다.

- **업로드 문제** - 이 릴리스는 오류로 인해 업로드가 다시 시작되면 업로드 완료 속도를 저하할 수 있는 업로드 문제를 해결합니다. 이 문제는 주로 사용 가능한 대역폭에 비해 크기가 큰 파일로 구성된 데이터 세트를 업로드할 때 발생할 수 있습니다. 특히 대역폭 제한이 활성화된 경우 더욱 그러하며 이에 국한되지 않습니다. 이렇게 변경하면 지정된 파일에 대한 업로드를 다시 시작하기 전에 업로드 완료를 위한 충분한 기회가 제공됩니다.

이 릴리스에는 다음 업데이트도 포함되어 있습니다.

- 2020년 10월까지 공개된 모든 누적 Windows 업데이트 및 .NET Framework 업데이트
- 최신 Dell 전원 공급 장치와의 호환성 문제를 해결하기 위해 공장 설치 시 BMC(베이스보드 관리 컨트롤러) 펌웨어 버전이 3.32.32.32에서 3.36.36.36으로 업그레이드됩니다.
- 이 릴리스는 Azure Stack Edge 디바이스에서 IoT Edge 1.0.9.3을 지원합니다.

## <a name="known-issues-in-this-release"></a>이 릴리스의 알려진 문제

이 릴리스에 대해 언급된 새로운 문제는 없습니다. 모든 릴리스 언급된 문제는 이전 릴리스에서 넘어 왔습니다. 알려진 문제 목록을 보려면 [GA 버전의 알려진 문제](../databox-gateway/data-box-gateway-release-notes.md#known-issues-in-ga-release)로 이동합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge 배포 준비](../databox-online/azure-stack-edge-deploy-prep.md)