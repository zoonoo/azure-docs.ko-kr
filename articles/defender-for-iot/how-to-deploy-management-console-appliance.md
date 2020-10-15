---
title: IoT 용 Azure Defender에서 관리 콘솔 배포
description: IoT 용 Azure Defender에서 관리 콘솔을 배포 하는 방법에 대해 알아봅니다.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2020
ms.author: rkarlin
ms.openlocfilehash: 6af943951b2dba0c4a11b08ad1bcd961cf1c0fc2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094415"
---
# <a name="deploy-the-management-console"></a>관리 콘솔 배포
이 문서에서는 Azure Defender for IoT 온-프레미스 관리 콘솔을 배포 하는 방법을 설명 합니다.

## <a name="the-on-premises-management-console"></a>온-프레미스 관리 콘솔

온-프레미스 관리 콘솔은 모든 네트워크 자산에 대 한 통합 보기를 제공 하 고 모든 시설에서 주요 IoT 및 OT 위험 표시기와 경고에 대 한 실시간 보기를 제공 합니다. SOC 워크플로 및 실행 서적에 긴밀 하 게 통합 되어 있어 완화 작업 및 위협에 대 한 사이트 간 상관 관계를 쉽게 확인할 수 있습니다.

- 자산 관리, 위험 및 취약성 관리용 단일 통합 플랫폼 및 인시던트 응답과 함께 위협 모니터링을 사용 하 여 전체적인 복잡성을 줄입니다.

- 집계 및 상관 관계 – 모든 사이트에서 수집 된 데이터 및 경고를 표시, 집계 및 분석 합니다.

- 모든 센서 제어 – 단일 위치에서 모든 센서를 구성 하 고 모니터링 합니다.

   ![IoT 용 Azure Defender 사이트 관리 뷰](media/updates/image2.png)

## <a name="deploy-the-on-premises-management-console-appliance"></a>온-프레미스 관리 콘솔 어플라이언스 배포

이 프로세스를 수행 하려면 자체 하드웨어를 확보 하 고 소프트웨어를 설치 해야 합니다. 솔루션은 인증 된 어플라이언스에서 실행 됩니다. 인증 된 어플라이언스를 구매 하는 경우 [에 대 한 참조로 IoT 용 Azure Defender 하드웨어 사양 가이드](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) 를 참조 하세요.

ISO 이미지를 다운로드 하 고 센서 소프트웨어를 설치 하는 방법에 대 한 자세한 내용은 [IoT 용 Azure Defender 설치 가이드](https://aka.ms/AzureDefenderforIoTInstallSensorISO) 를 참조 하세요.

**온-프레미스 관리 콘솔을 배포 하려면 다음을 수행 합니다.**

1. IoT 용 Microsoft Azure Defender로 이동 합니다.

2. **온-프레미스 관리 콘솔을**선택 합니다.

   ![IoT 용 Azure Defender 온-프레미스 관리 콘솔 보기](media/updates/image15.png)

3. **버전 선택** 메뉴에서 버전 3.1을 선택 합니다.

4. **다운로드** 를 선택 하 고 파일을 저장 합니다.

5. 소프트웨어가 설치 되 면 네트워크 설정 작업을 수행 합니다. 자세한 내용은 [IoT 용 Azure Defender 네트워크 설정 가이드](https://aka.ms/AzureDefenderForIoTNetworkSetup) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

구성 옵션에 대 한 자세한 내용을 보려면 모듈 구성 방법 가이드를 참조 하세요.
> [!div class="nextstepaction"]
> [모듈 구성 방법 가이드](./how-to-agent-configuration.md)
