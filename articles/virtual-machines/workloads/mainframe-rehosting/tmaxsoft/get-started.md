---
title: Azure 가상 머신에서 TmaxSoft 오픈프레임 시작
description: Azure 가상 머신(VM)에서 TmaxSoft OpenFrame 환경을 사용하여 IBM z/OS 메인프레임 워크로드를 다시 호스팅합니다.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "61485539"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Azure에서 TmaxSoft 오픈프레임 시작

기존 메인프레임 에셋을 TmaxSoft OpenFrame을 사용하여 Microsoft Azure로 이동합니다. 이 인기 있는 리호스팅 솔루션은 Azure에서 에뮬레이션 환경을 만들어 응용 프로그램을 빠르게 마이그레이션할 수 있도록 합니다. 다시 포시할 필요가 없습니다.

## <a name="openframe-rehosting-environment"></a>오픈프레임 리호스팅 환경

개발, 데모, 테스트 또는 프로덕션 워크로드를 위해 Azure에서 OpenFrame 환경을 설정합니다. 다음 그림과 같이 OpenFrame에는 Azure에서 메인프레임 에뮬레이션 환경을 만드는 여러 구성 요소가 포함되어 있습니다. 예를 들어 OpenFrame 온라인 서비스는 IBM 고객 정보 제어 시스템(CICS)과 같은 메인프레임 미들웨어를 대체합니다. TJES 구성 요소가 있는 OpenFrame 일괄 처리는 IBM 메인프레임의 작업 항목 하위 시스템(JES)을 대체합니다. 

![오픈프레임 재호스팅 프로세스](media/openframe-01.png)

> [!NOTE]
> Azure에서 OpenFrame 환경을 실행하려면 TmaxSoft의 유효한 제품 라이센스 또는 평가판 라이선스가 있어야 합니다.

## <a name="openframe-components"></a>오픈프레임 구성 요소

다음 구성 요소는 Azure의 OpenFrame 환경의 일부입니다.

- 메인프레임 자산을 분석한 다음 Azure로 마이그레이션하는 솔루션인 OFMiner를 포함한 **마이그레이션 도구입니다.**
- **메인프레임의**코볼 프로그램을 해석하는 컴파일러OFCOBOL을 포함한 컴파일러; 메인프레임의 PL/I 프로그램을 해석하는 OFPLI; 그리고 OFASM, 메인 프레임의 어셈블러 프로그램을 해석하는 컴파일러.
- 자바 엔터프라이즈 사용자 솔루션 (JEUS), 자바 엔터프라이즈 에디션 6.OFGW에 대한 인증 웹 응용 프로그램 서버, 그리고 3270 수신기를 제공하는 OpenFrame 게이트웨이 구성 요소를 포함한 **프런트 엔드** 구성 요소입니다.
- **응용 프로그램** 환경. 오픈 프레임 베이스는 전체 시스템을 관리하는 미들웨어입니다. 오픈프레임 서버 C(OSC)는 메인프레임의 미들웨어와 IBM CICS를 대체합니다.
- 티베로 (도시), 오라클 데이터베이스, 마이크로 소프트 SQL 서버, IBM Db2, 또는 MySQL과 같은 **관계형 데이터베이스.** OpenFrame 응용 프로그램은 개방형 데이터베이스 연결(ODBC) 프로토콜을 사용하여 데이터베이스와 통신합니다.
- 시스템 및 리소스에 대한 사용자 액세스를 제어하는 서비스 모듈인 TACF를 통한 **보안입니다.** 
- **OFManager는** 웹 환경에서 OpenFrame의 운영 및 관리 기능을 제공하는 솔루션입니다.

![오픈프레임 아키텍처](media/openframe-02.png)

## <a name="next-steps"></a>다음 단계

- [Azure에 TmaxSoft 오픈프레임 설치](./install-openframe-azure.md)
