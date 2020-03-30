---
title: Azure에서 지원되는 메인프레임 워크로드 | 마이크로 소프트 문서
description: Microsoft 파트너의 메인프레임 에뮬레이터 및 기타 서비스를 사용하여 Microsoft Azure를 사용하여 IBM Z 기반 시스템과 같은 메인프레임 워크로드를 다시 호스트합니다.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: cf02ff7078fe5d33f220c0abc2d941d483e5c125
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293507"
---
# <a name="mainframe-workloads-supported-on-azure"></a>Azure에서 지원되는 메인프레임 워크로드

여기에 나열된 솔루션은 메인프레임 워크로드를 Microsoft Azure로 마이그레이션하는 데 도움이 될 수 있습니다. 비교적 쉽게 마이그레이션할 수 있는 워크로드도 있는 반면 레거시 시스템 소프트웨어에 종속된 다른 워크로드를 다시 호스트할 수 있습니다. 

메인프레임 에뮬레이션 및 서비스에 대한 자세한 내용은 [Azure 메인프레임 마이그레이션 센터를](https://azure.microsoft.com/migration/mainframe/)참조하십시오.

## <a name="migrate-mainframe-closer-to-azure"></a>메인프레임을 Azure에 더 가깝게 마이그레이션

- [마이크로 포커스 비주얼 코볼](https://www.microfocus.com/products/visual-cobol/) 개발 및 통합 도구.
- .NET 플랫폼을 위한 [마이크로 포커스 PL/I](https://www.microfocus.com/campaign/download/pli-modernization/) 레거시 컴파일러로 메인프레임 PL/I 구문, 데이터 형식 및 동작을 지원합니다.
- [마이크로 포커스 엔터프라이즈 서버](https://www.microfocus.com/products/enterprise-suite/enterprise-server/) 메인프레임 통합 플랫폼.
- [후지쯔 넷코볼](https://www.fujitsu.com/global/products/software/developer-tool/netcobol/) 개발 및 통합 도구.
- [NTT 데이터 엔터프라이즈 코볼](https://us.nttdata.com/en/digital/application-development-and-modernization) 개발 및 통합 도구.
- .NET 플랫폼을 위한 [NTT Open PL/I](https://us.nttdata.com/en/digital/application-development-and-modernization) 레거시 컴파일러로 메인프레임 PL/I 구문, 데이터 형식 및 동작을 지원합니다.
- [레인 코드 코볼 컴파일러](https://www.raincode.com/products/cobol/) 개발 및 통합 도구.
- .NET 플랫폼용 [Raincode PL/I 컴파일러는](https://www.raincode.com/products/pli/) 메인프레임 PL/I 구문, 데이터 형식 및 동작을 지원합니다.
- 메인프레임 어셈블러 370 및 HLASM 구문에 대한 [레인코드 ASM370 컴파일러.](https://www.raincode.com/technical-landscape/asm370/)
- 비주얼 스튜디오 플러그인을 통해 .NET 프레임 워크에 대한 [.NET 컴파일러에](https://asna.com/us/products/visual-rpg) 대한 ASNA 비주얼 RPG.
- [현대 시스템 CTU (코볼 - 유니버설)](https://modernsystems.com/automatic-cobol-to-java-conversion/) 개발 및 통합 도구.

## <a name="deploy-an-emulation-environment-for-online-and-batch-processing"></a>온라인 및 일괄 처리에 대한 에뮬레이션 환경 배포

- [마이크로 포커스 엔터프라이즈 서버](https://www.microfocus.com/products/enterprise-suite/enterprise-server/) 메인프레임 통합 플랫폼.
- [NTT DATA 메인프레임 개발 환경](https://us.nttdata.com/en/-/media/assets/white-paper/apps-mainframe-re-hosting-development-environment-whitepaper.pdf) 네이티브 트랜잭션 처리 환경.
- JCL 트랜잭션 기능을 포함한 [NTT 데이터 배치 처리](https://us.nttdata.com/en/-/media/assets/white-paper/apps-mainframe-re-hosting-development-environment-whitepaper.pdf) 환경(BPE).
- .NET 및 Azure 플랫폼에 대한 [비코드 CICS](https://www.raincode.com/technical-landscape/cics/) 에뮬레이터.
- [레인 코드 JCL](https://www.raincode.com/products/jcl/) 플러그 호환 JCL 인터프리터.

## <a name="code-conversion"></a>코드 변환

- [Asysco](https://www.asysco.com/azure-cloud/) 소스 코드 변환.
- [LzLabs 소프트웨어 정의 메인프레임](https://www.lzlabs.com/) 관리 소프트웨어 컨테이너는 메인프레임 애플리케이션을 Linux 컴퓨터 또는 프라이빗, 퍼블릭 및 하이브리드 클라우드 환경으로 마이그레이션합니다.
- 레거시 비즈니스 응용 프로그램 및 데이터베이스를 디지털화하기 위한 [Blu Age](https://www.bluage.com/) 도구입니다.
- [메인프레임](https://www.heirloomcomputing.com/tag/convert-cobol-to-java/) 코볼, CICS 및 VSAM을 Java로 변환하는 가보 컴퓨팅 서비스.

## <a name="modernization-services"></a>현대화 서비스

Microsoft는 대규모 조직에서 솔루션을 설계, 빌드 및 관리하는 데 도움을 줄 수 있는 글로벌 시스템 통합업체(GSIs)와 파트너관계를 맺고 있습니다. 

- [Azure 메인프레임 마이그레이션 센터](https://azure.microsoft.com/migration/mainframe/)
