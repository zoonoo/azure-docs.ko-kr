---
title: Peering Service - FAQ
titleSuffix: Azure
description: Peering Service - FAQ
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75775473"
---
# <a name="peering-service---faqs"></a>Peering Service FAQ

일반적인 질문에 대하여 다음 정보를 검토하세요.

**이동 통신 사업자가 Peering Service 지원을 위해 Microsoft와의 기존 직접 피어링을 사용할 수 있나요?**

예, 이동 통신 사업자는 기존 PNI를 활용하여 Peering Service를 지원할 수 있습니다. Peering Service PNI가 HA를 지원하려면 다양성이 필요합니다. 기존 PNI가 이미 다양성을 가지고 있는 경우에는 새로운 인프라가 필요 없습니다. 기존 PNI가 다양성을 필요로 하는 경우에는 확대가 가능합니다.

**이동 통신 사업자가 Peering Service 지원을 위해 Microsoft와의 새로운 직접 피어링을 사용할 수 있나요?**

예, 가능합니다. Microsoft는 이동 통신 사업자와 협력을 통해 직접 피어링을 새로 생성하여 Peering Service를 지원합니다.  

**왜 직접 피어링이 Peering Service 지원의 요구 사항인가요?**

Peering Service의 주요 동인 중 하나는 잘 연결된 SP를 통해 Microsoft 온라인 서비스에 대한 연결을 제공하는 것입니다. PNI는 항상 Gbps 범위에 있으므로 이동 통신 사업자와 Microsoft 간의 처리량이 높은 연결에 필요한 기본 구성 요소입니다.

**Peering Service 지원을 위한 직접 피어링에 대한 다양성 요구 사항은 무엇인가요?**

PNI는 로컬 중복 및 지리적 중복성을 지원해야 합니다. 로컬 중복은 특정 피어링 사이트에 있는 두 개의 다양한 경로 집합으로 정의됩니다. 지역 중복성의 경우, 기본 사이트의 오류 발생에 대비해 이동 통신 사업자는 다른 Microsoft 에지 사이트에 추가적으로 연결성을 갖추고 있어야 합니다. 오류가 짧게 발생하는 경우 이동 통신 사업자는 백업 사이트를 통해 트래픽을 라우팅할 수 있습니다.

**이동 통신 사업자가 이미 SLA와 엔터프라이즈급 인터넷을 제공하고 있다면 이 제공은 어떻게 다른가요?**

일부 이동 통신 사업자는 담당하는 네트워크 부분에서 SLA 및 엔터프라이즈급 인터넷을 제공합니다. Peering Service에서 Microsoft는 네트워크의 Microsoft 부분에서 SLA 제공 트래픽을 제공합니다. Peering Service를 선택하는 고객은 엔드투엔드 SLA를 갖게 됩니다. 고객 사이트로부터 ISP 네트워크의 마이크로소프트 에지까지의 SLA는 해당 ISP에서 보장이 가능합니다. Microsoft 에지에서 최종 사용자 애플리케이션까지 Microsoft 글로벌 네트워크의 SLA는 현재 Microsoft에서 보장합니다.

**서비스 공급자가 PNI를 사용하여 이미 Microsoft와 피어링하는 경우 Peering Service를 지원하기 위해 어떤 종류의 변경이 필요한가요?**

* Peering Service 사용자와 해당 트래픽을 식별하기 위한 소프트웨어 변경이 필요합니다. Peering Service 연결을 통해 가장 가까운 Microsoft 에지에서 사용자의 트래픽을 교환하려면 라우팅 정책을 변경해야 할 수 있습니다.
* 연결에 반드시 로컬 중복 및 지역 중복이 있어야 합니다.
