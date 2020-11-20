---
title: Azure Virtual Machines에서 TmaxSoft OpenFrame 시작
description: Azure Virtual Machines (Vm)에서 TmaxSoft OpenFrame 환경을 사용 하 여 IBM z/OS 메인프레임 워크 로드를 Rehost.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.openlocfilehash: 215a17ced6be4cc8792ac1a06115450bfbccac99
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963269"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Azure에서 TmaxSoft OpenFrame 시작

기존 메인프레임 자산을 가져와 TmaxSoft OpenFrame을 사용 하 Microsoft Azure로 이동 합니다. 널리 사용 되는이 재호스팅 솔루션은 Azure에서 에뮬레이션 환경을 만들어 응용 프로그램을 신속 하 게 마이그레이션할 수 있도록 합니다. 다시 포맷 해야 하는 것은 아닙니다.

## <a name="openframe-rehosting-environment"></a>OpenFrame 재호스팅 환경

개발, 데모, 테스트 또는 프로덕션 워크 로드를 위해 Azure에서 OpenFrame 환경을 설정 합니다. 다음 그림에 나와 있는 것 처럼 OpenFrame에는 Azure에서 메인프레임 에뮬레이션 환경을 만드는 여러 구성 요소가 포함 되어 있습니다. 예를 들어 OpenFrame 온라인 서비스는 IBM CICS (Customer Information Control System)와 같은 메인프레임 미들웨어를 대체 합니다. OpenFrame Batch를 TJES 구성 요소와 함께 사용 하면 IBM 메인프레임의 JES (Job Entry Subsystem)를 대체 합니다. 

![OpenFrame 재호스팅 프로세스](media/openframe-01.png)

> [!NOTE]
> Azure에서 OpenFrame 환경을 실행 하려면 TmaxSoft의 유효한 제품 라이선스 또는 평가판 라이선스가 있어야 합니다.

## <a name="openframe-components"></a>OpenFrame 구성 요소

다음 구성 요소는 Azure의 OpenFrame 환경에 포함 되어 있습니다.

- OFMiner를 포함 하는 **마이그레이션 도구** 는 메인프레임 자산을 분석 한 후 Azure로 마이그레이션하는 솔루션입니다.
- 메인프레임 COBOL 프로그램을 해석 하는 컴파일러 인 OFCOBOL을 비롯 **한 컴파일러** 메인프레임의 PL/I 프로그램을 해석 하는 OFPLI 및 OFASM, 메인프레임의 어셈블러 프로그램을 해석 하는 컴파일러입니다.
- Java enterprise 사용자 솔루션 (JEUS), Java Enterprise Edition 6. OFGW에 대해 인증 된 웹 응용 프로그램 서버, 3270 수신기를 제공 하는 OpenFrame gateway 구성 요소 등의 **프런트 엔드** 구성 요소입니다.
- **응용 프로그램** 환경. OpenFrame Base는 전체 시스템을 관리 하는 미들웨어입니다. OpenFrame 서버 유형 C (.OSC)는 메인프레임 미들웨어 및 IBM CICS를 대체 합니다.
- Tibero (표시 됨), Oracle Database, Microsoft SQL Server, IBM Db2 또는 MySQL과 같은 **관계형 데이터베이스** 입니다. OpenFrame 응용 프로그램은 ODBC (Open Database Connectivity) 프로토콜을 사용 하 여 데이터베이스와 통신 합니다.
- 시스템 및 리소스에 대 한 사용자 액세스를 제어 하는 서비스 모듈인 TACF을 통한 **보안** . 
- **Ofmanager** 는 웹 환경에서 openframe의 작업 및 관리 기능을 제공 하는 솔루션입니다.

![OpenFrame 아키텍처](media/openframe-02.png)

## <a name="next-steps"></a>다음 단계

- [Azure에 TmaxSoft OpenFrame 설치](./install-openframe-azure.md)
