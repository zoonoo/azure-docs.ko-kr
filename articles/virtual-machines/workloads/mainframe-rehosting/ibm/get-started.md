---
title: Azure에서 IBM 워크 로드 | Microsoft Docs
description: Microsoft 파트너의 메인프레임 에뮬레이터 및 기타 서비스를 사용 하 여 Microsoft Azure 사용 하 여 IBM z/OS 워크 로드를 rehost.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "68834592"
---
# <a name="ibm-workloads-on-azure"></a>Azure에서 IBM 워크 로드

Z/OS를 기반으로 하는 많은 IBM 메인프레임 워크 로드는 기능 손실 없이 Azure에 복제 될 수 있으며, 사용자가 기본 시스템의 변경 내용을 확인할 수도 있습니다. Azure의 재호스팅 응용 프로그램은 필요한 메인프레임 기능과 탄력성, 가용성 및 클라우드의 잠재적 비용 절감을 제공 합니다.

Azure는 기존 IBM 메인프레임 환경과의 통합을 지원 하 여 적절 한 applicates를 마이그레이션하고, 필요한 경우 하이브리드 솔루션을 실행 하 고, 시간에 따라 마이그레이션하는 데 사용할 수 있습니다. Azure에 대 한 기존 메인프레임 기반 프로그램을 완전히 다시 작성할 수 있지만 rehost 하는 것이 더 일반적입니다. 재작성은 마이그레이션 프로젝트에 비용, 복잡성 및 시간을 추가 합니다. 재호스팅를 사용 하 여 다음을 수행할 수 있습니다.

- 응용 프로그램을 클라우드 기반 에뮬레이터로 이동 합니다.

- 데이터베이스를 클라우드 기반 데이터베이스로 마이그레이션합니다.

- 코드 변환 엔진을 사용 하 여 모듈 및 코드를 바꿉니다.

또한 WebSphere MQ를 비롯 한 IBM 소프트웨어는 현재 Azure Marketplace에 있습니다. IBM 소프트웨어용 라이선스를 사용 하 여 Azure에서 제공 하는 주문형 인프라 크기 조정을 활용 하 여 가상 컴퓨터를 신속 하 게 시작할 수 있습니다.

광범위 한 파트너 에코 시스템을 통해 IBM 메인프레임 시스템을 Azure로 마이그레이션할 수 있습니다. 응용 프로그램을 다시 작성 하거나 대체 하는 단계별 배포를 형성 하기 전에 가능한 한 자주 사용 하는 방식으로 다시 사용 합니다. [Azure 메인프레임 마이그레이션 센터](https://azure.microsoft.com/migration/mainframe/)에서 파트너의 추가 지침 및 도움을 받으세요.

**다음 단계**

- [메인프레임 마이그레이션: myths 및 팩트](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Azure에서 IBM zD&T 개발/테스트 환경 설치](./install-ibm-z-environment.md)
- [IBM zD&T v1에서 응용 프로그램 개발자 제어 배포 (ADCD)를 설정 합니다.](./demo.md)
- [Azure에서 IBM DB2 pureScale](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
