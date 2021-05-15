---
title: Azure Virtual Machines에서 TmaxSoft OpenFrame 시작
description: Azure Virtual Machines(Vm)에서 TmaxSoft OpenFrame 환경을 사용하여 IBM z/OS 메인프레임 워크로드를 다시 호스트.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 346c331596f984cbb068c03110997c2478499488
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950674"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Azure에서 TmaxSoft OpenFrame 시작

기존 메인프레임 자산을 가져와 TmaxSoft OpenFrame을 사용하여 Microsoft Azure로 이동합니다. 널리 사용되는 이 재호스팅 솔루션은 Azure에서 에뮬레이션 환경을 만들어 애플리케이션을 신속하게 마이그레이션할 수 있도록 합니다. 서식 다시 지정이 필요하지 않습니다.

## <a name="openframe-rehosting-environment"></a>OpenFrame 재호스팅 환경

Azure의 OpenFrame 환경을 개발, 데모, 테스트 또는 프로덕션 워크로드에 대해 설정합니다. 다음 그림에 나와 있는 것처럼 OpenFrame에는 Azure에서 메인프레임 에뮬레이션 환경을 만드는 여러 구성 요소가 포함되어 있습니다. 예를 들어 OpenFrame 온라인 서비스는 IBM CICS(Customer Information Control System)와 같은 메인프레임 미들웨어를 대체합니다. OpenFrame Batch를 TJES 구성 요소와 함께 사용하면 IBM 메인프레임의 JES(Job Entry Subsystem)를 대체합니다. 

![OpenFrame 재호스팅 프로세스](media/openframe-01.png)

> [!NOTE]
> Azure에서 OpenFrame 환경을 실행하려면 TmaxSoft의 유효한 제품 라이선스 또는 평가판 라이선스가 있어야 합니다.

## <a name="openframe-components"></a>OpenFrame 구성 요소

다음 구성 요소는 Azure의 OpenFrame 환경의 일부입니다.

- OFMiner를 포함하는 **마이그레이션 도구** 는 메인프레임 자산을 분석한 후 Azure로 마이그레이션하는 솔루션입니다.
- 메인프레임 COBOL 프로그램을 해석하는 컴파일러인 OFCOBOL을 비롯한 **컴파일러** 메인프레임의 PL/I 프로그램을 해석하는 OFPLI 및 OFASM, 메인프레임의 어셈블러 프로그램을 해석하는 컴파일러입니다.
- Java Enterprise User Solution(JEUS), Java Enterprise Edition 6. OFGW에 대해 인증된 웹 애플리케이션 서버, 3270 수신기를 제공하는 OpenFrame gateway 구성 요소 등의 **프런트 엔드** 구성 요소입니다.
- **애플리케이션** 환경. OpenFrame Base는 전체 시스템을 관리하는 미들웨어입니다. OSC(OpenFrame 서버 유형 C)는 메인프레임 미들웨어 및 IBM CICS를 대체합니다.
- Tibero(표시됨), Oracle Database, Microsoft SQL Server, IBM Db2 또는 MySQL과 같은 **관계형 데이터베이스** 입니다. OpenFrame 애플리케이션은 ODBC(Open Database Connectivity) 프로토콜을 사용하여 데이터베이스와 통신합니다.
- 시스템 및 리소스에 대한 사용자 액세스를 제어하는 서비스 모듈인 TACF을 통한 **보안**. 
- **Ofmanager** 는 웹 환경에서 openframe의 작업 및 관리 기능을 제공하는 솔루션입니다.

![OpenFrame 아키텍처](media/openframe-02.png)

## <a name="next-steps"></a>다음 단계

- [Azure에 TmaxSoft OpenFrame 설치](./install-openframe-azure.md)
