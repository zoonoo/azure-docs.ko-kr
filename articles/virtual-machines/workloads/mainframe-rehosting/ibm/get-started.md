---
title: Azure의 IBM 워크로드 | Microsoft Docs
description: Microsoft 파트너의 메인프레임 에뮬레이터 및 기타 서비스를 사용하여 Microsoft Azure를 사용하는 IBM z/OS 워크로드를 다시 호스트합니다.
services: virtual-machines
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: d834b1fccf2951fffa8d00f4deb21434b9fea4ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955214"
---
# <a name="ibm-workloads-on-azure"></a>Azure의 IBM 워크로드

z/OS를 기반으로 하는 많은 IBM 메인프레임 워크로드는 기능 손실 없이 사용자가 기본 시스템의 변경을 모를 정도로 Azure에 복제할 수 있습니다. Azure의 재호스팅 애플리케이션은 필요한 메인프레임 기능과 탄력성, 가용성, 클라우드의 잠재적 비용 절감을 제공합니다.

Azure는 기존 IBM 메인프레임 환경과의 통합을 지원하여 적합한 애플리케이션을 마이그레이션하고, 필요한 경우 하이브리드 솔루션을 실행하고, 시간에 따라 마이그레이션을 수행할 수 있도록 지원합니다. Azure에 대한 기존 메인프레임 기반 프로그램을 완전히 다시 작성할 수 있지만 재호스팅하는 것이 더 일반적입니다. 재작성하면 마이그레이션 프로젝트의 비용, 복잡성, 시간이 늘어납니다. 재호스팅을 통해 다음을 이용할 수 있습니다.

- 애플리케이션을 클라우드 기반 에뮬레이터로 이동합니다.

- 데이터베이스를 클라우드 기반 데이터베이스로 마이그레이션합니다.

- 코드 변환 엔진을 사용하여 모듈 및 코드를 바꿉니다.

또한 WebSphere MQ를 비롯한 IBM 소프트웨어는 현재 Azure Marketplace에 있습니다. IBM 소프트웨어용 라이선스를 사용하면 Azure에서 제공하는 주문형 인프라 크기 조정을 활용하여 가상 머신을 신속하게 시작할 수 있습니다.

광범위한 파트너 에코시스템을 통해 IBM 메인프레임 시스템을 Azure로 마이그레이션할 수 있습니다. 대부분의 경우 다시 작성하거나 애플리케이션을 교체하는 단계별 배포를 시작하기 전에 가능한 한 자주 사용하는 방식을 사용합니다. [Azure 메인프레임 마이그레이션 센터](https://azure.microsoft.com/migration/mainframe/)에서 파트너의 추가 지침 및 도움을 받으세요.

**다음 단계**

- [메인프레임 마이그레이션: 허구 및 팩트](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Azure에서 IBM zD&T 개발/테스트 환경 설치](./install-ibm-z-environment.md)
- [IBM zD&T v1에서 ADCD(Application Developers Controlled Distribution) 설치](./demo.md)
- [Azure의 IBM DB2 pureScale](ibm-db2-purescale-azure.md)
