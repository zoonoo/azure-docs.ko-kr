---
title: 피어 링 서비스-FAQ
titleSuffix: Azure
description: 피어 링 서비스-FAQ
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75775473"
---
# <a name="peering-service---faqs"></a>피어 링 서비스-Faq

일반적인 질문에 대 한 다음 정보를 검토할 수 있습니다.

**피어 링 서비스를 지원 하기 위해 Microsoft와 기존 직접 피어 링을 사용 하는 통신 회사를 사용할 수 있나요?**

예, 반송파는 기존 PNI를 활용 하 여 피어 링 서비스를 지원할 수 있습니다. 피어 링 서비스 PNI는 HA를 지원 하기 위해 다양 한 다양성을 필요로 합니다. 기존 PNI가 이미 다양성을가지고 있는 경우에는 새로운 인프라가 필요 하지 않습니다. 기존 PNI가 산포도를 필요로 하는 경우에는 확대할 수 있습니다.

**반송파가 피어 링 서비스를 지원 하기 위해 Microsoft와 새로운 직접 피어 링을 사용할 수 있나요?**

예, 가능한 경우도 있습니다. Microsoft는 운송 업체와 협력 하 여 피어 링 서비스를 지원할 새로운 직접 피어 링을 만듭니다.  

**피어 링 서비스를 지원 하기 위해 직접 피어 링이 요구 되는 이유는 무엇 인가요?**

피어 링 서비스의 기본 드라이버 중 하나는 잘 연결 된 SP를 통해 Microsoft 온라인 서비스에 대 한 연결을 제공 하는 것입니다. PNI는 항상 Gbps 범위에 있으므로 캐리어와 Microsoft 간의 처리량이 높은 연결을 위한 기본 빌딩 블록입니다.

**피어 링 서비스를 지원 하기 위해 직접 피어 링에 대 한 다양성 요구 사항은 무엇 인가요?**

PNI는 로컬 중복 및 지리적 중복성을 지원 해야 합니다. 로컬 중복은 특정 피어 링 사이트의 다양 한 경로 집합으로 정의 됩니다. 지역 중복성을 사용 하려면 기본 사이트에 오류가 발생 하는 경우 다른 Microsoft edge 사이트에서 해당 통신 업체가 추가 연결을 사용 해야 합니다. 단기 실패 기간에 대 한 반송파는 백업 사이트를 통해 트래픽을 라우팅할 수 있습니다.

**이 공급 업체는 이미 SLA 및 엔터프라이즈급 인터넷을 제공 하 고 있으며,이 제품은 어떻게 다른 가요?**

일부 매개체는 네트워크의 해당 부분에 SLA 및 엔터프라이즈급 인터넷을 제공 합니다. 피어 링 서비스에서 Microsoft는 네트워크의 Microsoft 부분에 SLA 제공 트래픽을 제공 합니다. 피어 링을 선택 하면 고객은 종단 간 SLA를 얻게 됩니다. Isp 네트워크에서 해당 사이트의 Microsoft edge에 대 한 SLA는 ISP에서 다룰 수 있습니다. Microsoft edge에서 최종 사용자 응용 프로그램까지 microsoft 글로벌 네트워크의 SLA는 이제 Microsoft에서 다룹니다.

**서비스 공급자가 PNI를 사용 하 여 이미 Microsoft와 피어 링 하는 경우 피어 링 서비스를 지원 하기 위해 어떤 종류의 변경이 필요 한가요?**

* 피어 링 서비스 사용자와 해당 트래픽을 식별 하기 위한 소프트웨어 변경 피어 링 서비스 연결을 통해 가장 가까운 Microsoft edge에서 사용자의 트래픽을 교환 하려면 라우팅 정책 변경 해야 할 수 있습니다.
* 연결에 로컬 중복 및 지역 중복이 있는지 확인 합니다.
