---
title: Azure(대규모 인스턴스)의 SAP HANA에 대한 데이터 계층 및 확장 노드 | Microsoft Docs
description: Azure(대규모 인스턴스)에서 SAP HANA에 대한 데이터 계층 및 확장 노드에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/17/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6d13edf6e2512f6f70af8265e1859b106e75885c
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110577552"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>SAP HANA 데이터 계층화 및 확장 노드 사용

SAP는 SAP NetWeaver 릴리스와 SAP BW/4HANA가 서로 다른 SAP BW(Business Warehouse)에 대한 데이터 계층화 모델을 지원합니다. 데이터 계층 모델에 대한 자세한 내용은 [SAP HANA 확장 노드가 있는 SAP BW/4HANA 및 SAP BW on HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#)를 참조하세요.

HANA 대규모 인스턴스를 사용하면 FAQ 및 SAP 블로그 문서에서 설명한 대로 SAP HANA 확장 노드의 옵션 1 구성을 사용할 수 있습니다. 옵션 2 구성은 다음 HANA 대규모 인스턴스 SKU(S72m, S192, S192m, S384 및 S384m)와 함께 설정될 수 있습니다.

## <a name="advantages-of-sap-hana-extension-nodes"></a>SAP HANA 확장 노드의 장점

옵션 1 또는 2 중 하나를 사용하여 SAP HANA 확장 노드를 사용하면 SAP HANA 메모리를 보다 효율적으로 사용할 수 있습니다. SAP 크기 조정 지침을 살펴보면 SAP HANA 확장 노드의 이점이 명확해질 수 있습니다. 다음은 몇 가지 예입니다.

- SAP HANA 크기 조정 지침은 일반적으로 메모리에 비해 데이터 볼륨 크기의 두 배를 필요로 합니다. 핫 데이터를 사용하여 SAP HANA 인스턴스를 실행하는 경우 50% 미만의 메모리만 데이터를 저장합니다. 작업을 수행하는 SAP HANA를 위해 나머지 메모리가 보유하는 것이 가장 좋습니다.
- 즉, SAP BW 데이터베이스를 실행하는 2TB의 메모리가 있는 HANA 대규모 인스턴스 S192 단위에서 데이터 볼륨으로 1TB만을 갖습니다.
- 다른 SAP HANA 확장 노드 옵션 1과 S192 HANA 대규모 인스턴스 SKU를 사용하는 경우 데이터 볼륨에서 또 다른 2TB 용량을 제공합니다. 옵션 2 구성에서는 웜 데이터 볼륨에 4TB를 추가로 제공합니다. 핫 노드와 비교할 때 "웜" 확장 노드의 전체 메모리 용량은 옵션 1에 대한 데이터 저장에 사용할 수 있습니다. 옵션 2 SAP HANA 확장 노드 구성의 데이터 볼륨에 메모리를 두 배로 사용할 수 있습니다.
- 최종적으로 데이터 용량은 3TB이며, 옵션 1의 경우 핫 대 웜 비율은 1:2입니다. 옵션 2 확장 노드 구성에서는 데이터 용량이 5TB이고, 비율은 1:4입니다.

메모리에 비해 데이터 볼륨이 높을수록 요청하는 웜 데이터가 디스크에 저장될 가능성이 높아집니다.

## <a name="next-steps"></a>다음 단계

Azure(대규모 인스턴스)의 SAP HANA 작업 모델과 사용자의 책임에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [작업 모델 및 책임](hana-operations-model.md)
