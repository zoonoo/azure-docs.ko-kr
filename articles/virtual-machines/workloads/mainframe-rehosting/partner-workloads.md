---
title: Azure에서 지원 되는 메인프레임 워크 로드 | Microsoft Docs
description: Microsoft 파트너의 메인프레임 에뮬레이터 및 기타 서비스를 사용 하 여 Microsoft Azure를 사용 하는 IBM Z 기반 시스템과 같은 메인프레임 워크 로드를 rehost.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 05/09/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 07234e5f456a5467adde4d899341a6124d128567
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83006112"
---
# <a name="mainframe-workloads-supported-on-azure"></a>Azure에서 지원 되는 메인프레임 워크 로드

여기에 나열 된 솔루션은 메인프레임 작업을 Microsoft Azure로 마이그레이션하는 데 도움이 될 수 있습니다. 비교적 쉽게 마이그레이션할 수 있는 워크로드도 있는 반면 레거시 시스템 소프트웨어에 의존 하는 다른 작업은 다시 호스팅할 수 있습니다. 

메인프레임 에뮬레이션 및 서비스에 대 한 자세한 내용은 [Azure 메인프레임 마이그레이션 센터](https://azure.microsoft.com/migration/mainframe/)를 참조 하세요.

## <a name="migrate-mainframe-closer-to-azure"></a>Azure에 더 가까운 메인프레임 마이그레이션

- Visual Studio 플러그 인을 통해 .NET Framework [.net 컴파일러의 Asna visual RPG](https://asna.com/us/products/visual-rpg)
- [ASYSCO AMT cobol](https://www.asysco.com/cobol/) 개발 환경 (UNISYS, IBM 메인프레임 및 기타 cobol 언어 (예: 마이크로 포커스 COBOL)).
- [ASYSCO AMT](https://www.asysco.com/amt-go/) 는 고성능 워크 로드에 대 한 클라우드 기반 배포 아키텍처로 이동 합니다.
- [ASYSCO AMT 변환은](https://www.asysco.com/amt-transform/) 데이터, 코드, 스크립팅, 보안, 인터페이스 및 기타 메인프레임 아티팩트를 변환 하는 데 사용할 것입니다.
- [Fujitsu NetCOBOL](https://www.fujitsu.com/global/products/software/developer-tool/netcobol/) 개발 및 통합 도구
- [마이크로 포커스 시각적 COBOL](https://www.microfocus.com/products/visual-cobol/) 개발 및 통합 도구입니다.
- 마이크로 포커스는 .NET 플랫폼을 위한 [pl/i](https://www.microfocus.com/campaign/download/pli-modernization/) 레거시 컴파일러로, 메인프레임 pl/i 구문, 데이터 형식 및 동작을 지원 합니다.
- [마이크로 포커스 엔터프라이즈 서버](https://www.microfocus.com/products/enterprise-suite/enterprise-server/) 메인프레임 통합 플랫폼입니다.
- [최신 시스템 CTU (COBOL-유니버설)](https://modernsystems.com/automatic-cobol-to-java-conversion/) 개발 및 통합 도구입니다.
- [Ntt 데이터 엔터프라이즈 COBOL](https://us.nttdata.com/en/digital/application-development-and-modernization) 개발 및 통합 도구
- Ntt .NET 플랫폼에 대해 [pl/i](https://us.nttdata.com/en/digital/application-development-and-modernization) 레거시 컴파일러를 열어 메인프레임 pl/i 구문, 데이터 형식 및 동작을 지원 합니다.
- [COBOL 컴파일러](https://www.raincode.com/products/cobol/) 개발 및 통합 도구를 Raincode.
- .NET 플랫폼용 [RAINCODE pl/i 컴파일러](https://www.raincode.com/products/pli/) 는 메인프레임 pl/i 구문, 데이터 형식 및 동작을 지원 합니다.
- 메인프레임 어셈블러 370 및 HLASM 구문에 대 한 [RAINCODE ASM370 컴파일러](https://www.raincode.com/technical-landscape/asm370/) 입니다.

## <a name="deploy-an-emulation-environment-for-online-and-batch-processing"></a>온라인 및 일괄 처리를 위한 에뮬레이션 환경 배포

- [ASYSCO AMT GO](https://www.asysco.com/amt-go/) 배포 아키텍처는 CICS, IMS, TIP, hvtip 및 기타 일반적인 메인프레임 환경을 지원 합니다.
- [마이크로 포커스 엔터프라이즈 서버](https://www.microfocus.com/products/enterprise-suite/enterprise-server/) 메인프레임 통합 플랫폼입니다.
- [Ntt 데이터 메인프레임 재호스팅 Development environment](https://us.nttdata.com/en/-/media/assets/white-paper/apps-mainframe-re-hosting-development-environment-whitepaper.pdf) native transaction processing environment.
- [Ntt bpe (데이터 일괄 처리 환경](https://us.nttdata.com/en/-/media/assets/white-paper/apps-mainframe-re-hosting-development-environment-whitepaper.pdf) )는 jcl 트랜잭션 기능을 포함 합니다.
- .NET 및 Azure 플랫폼용 [Raincode CICS](https://www.raincode.com/technical-landscape/cics/) 에뮬레이터.
- [RAINCODE jcl](https://www.raincode.com/products/jcl/) 플러그 호환 jcl 인터프리터입니다.

## <a name="code-conversion"></a>코드 변환

- 소스 코드, 데이터, 일괄 처리, 일정, TP 모니터, 인터페이스, 보안, 관리 등을 포함 하는 [Asysco](https://www.asysco.com/azure-cloud/) 시스템 변환 기술입니다.
- 인벤토리 및 분석, 디자인 교육, 드레스 rehearsals, [Asysco](https://www.asysco.com/migration-services/) 및 마이그레이션 후 지원 등을 포함 하 여 마이그레이션 프로젝트용 종단 간 서비스를 제공 합니다.
- 기존 비즈니스 응용 프로그램 및 데이터베이스용 디지털에 대 한 [블루레이](https://www.bluage.com/) 도구입니다.
- [Heirloom 컴퓨팅](https://www.heirloomcomputing.com/tag/convert-cobol-to-java/) 서비스를 통해 메인프레임 COBOL, CICS 및 VSAM를 Java로 변환 합니다.
- 메인프레임 응용 프로그램을 Linux 컴퓨터 또는 사설, 공용 및 하이브리드 클라우드 환경으로 마이그레이션하기 위한 [소프트웨어 정의 메인프레임](https://www.lzlabs.com/) 관리 소프트웨어 컨테이너를 LzLabs.

## <a name="modernization-services"></a>현대화 서비스

Microsoft 파트너와 GSIs (global system 통합자)를 사용 하 여 대기업의 디자인, 빌드 및 관리를 지원할 수 있습니다. 

- [Azure 메인프레임 마이그레이션 센터](https://azure.microsoft.com/migration/mainframe/)
