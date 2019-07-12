---
title: Azure(대규모 인스턴스)의 SAP HANA 크기 조정 | Microsoft Docs
description: Azure(대규모 인스턴스)에서 SAP HANA에 대한 크기 조정입니다.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c7a81468fd19d3bd5d6eee79bb8a75396e3021f1
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707273"
---
# <a name="sizing"></a>크기 조정

HANA 대규모 인스턴스에 대한 크기 조정은 일반적으로 HANA에 대한 크기 조정과 다르지 않습니다. 다른 RDBMS에서 HANA로 이동하려는 기존 및 배포된 시스템의 경우 SAP는 기존 SAP 시스템에서 실행되는 다양한 보고서를 제공합니다. 데이터베이스가 HANA로 이동되면 이러한 보고서는 데이터를 확인하고 HANA 인스턴스에 대한 메모리 요구 사항을 계산합니다. 이러한 보고서를 실행하고 최신 패치 또는 버전을 가져오는 방법에 대한 자세한 내용은 다음 SAP Note를 참조하세요.

- [SAP Note #1793345 - HANA에서 SAP Suite에 대한 크기 조정](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 - Suite on HANA 및 S/4 HANA 크기 조정 보고서](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330-FAQ: SAP BW on HANA 크기 조정 보고서](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 - BW on HANA에 대한 크기 조정 보고서](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290 - BW on HANA에 대한 새 크기 조정 보고서](https://launchpad.support.sap.com/#/notes/2296290)

녹색 필드 구현을 위해 SAP Quick Sizer를 사용하여 HANA 상단에서 SAP 소프트웨어 구현에 대한 메모리 요구 사항을 계산할 수 있습니다.

데이터 볼륨이 증가함에 따라 HANA에 대한 메모리 요구 사항도 증가합니다. 나중에 발생할 상황을 예측하는 데 도움이 되도록 현재 메모리 사용을 파악해야 합니다. 그러면 메모리 요구 사항에 따라 해당 요구 사항을 HANA 대규모 인스턴스 SKU 중 하나에 매핑할 수 있습니다.

**다음 단계**
- [온보딩 요구 사항](hana-onboarding-requirements.md) 참조