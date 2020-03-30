---
title: Azure에서 IBM 워크로드 | 마이크로 소프트 문서
description: 메인프레임 에뮬레이터 및 Microsoft 파트너의 기타 서비스를 사용하여 Microsoft Azure를 사용하여 IBM z/OS 워크로드를 다시 호스트합니다.
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 13c83c53cdad719d6a4bed4cc1852b85d62082e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68834592"
---
# <a name="ibm-workloads-on-azure"></a>Azure에서 IBM 워크로드

z/OS를 기반으로 하는 많은 IBM 메인프레임 워크로드는 기능 손실 없이 Azure에서 복제할 수 있으며 사용자가 기본 시스템의 변경 사항을 알아차리지 않고도 복제할 수 있습니다. Azure에서 응용 프로그램을 다시 호스팅하면 필요한 메인프레임과 같은 기능과 클라우드의 탄력성, 가용성 및 잠재적비용 절감효과를 제공합니다.

Azure는 기존 IBM 메인프레임 환경과의 통합을 지원하므로 적합한 어플리케이션을 마이그레이션하고, 필요한 경우 하이브리드 솔루션을 실행하고, 시간이 지남에 따라 마이그레이션할 수 있습니다. Azure용 기존 메인프레임 기반 프로그램을 완전히 다시 작성할 수 있지만 다시 호스트하는 것이 일반적입니다. 다시 작성하면 마이그레이션 프로젝트에 대한 비용, 복잡성 및 시간이 추가됩니다. 리호스팅을 사용하면 다음을 수행할 수 있습니다.

- 응용 프로그램을 클라우드 기반 에뮬레이터로 이동합니다.

- 데이터베이스를 클라우드 기반 데이터베이스로 마이그레이션합니다.

- 코드 변환 엔진을 사용하여 모듈 및 코드를 바꿉니다.

또한 WebSphere 및 MQ를 포함한 IBM 소프트웨어가 이제 Azure 마켓플레이스에 있습니다. IBM 소프트웨어에 대한 라이선스를 사용하면 Azure에서 제공하는 온디맨드 인프라 확장 기능을 활용하여 가상 컴퓨터를 신속하게 시작할 수 있습니다.

IBM 메인프레임 시스템을 Azure로 마이그레이션하는 데 도움이 되는 광범위한 파트너 에코시스템을 사용할 수 있습니다. 대부분은 응용 프로그램을 다시 작성하거나 교체하는 단계적 배포에 착수하기 전에 가능한 경우 실용적인 재사용 접근 방식을 따릅니다. [Azure 메인프레임 마이그레이션 센터에서](https://azure.microsoft.com/migration/mainframe/)파트너의 추가 지침과 도움을 받으십시오.

**다음 단계**

- [메인프레임 마이그레이션: 신화와 사실](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Azure에 IBM zD&T 개발/테스트 환경 설치](./install-ibm-z-environment.md)
- [IBM zD&T v1에서 응용 프로그램 개발자 제어 배포(ADCD) 설정](./demo.md)
- [AZURE의 IBM DB2 순수 스케일](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
