---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 86d03e980a233a60e4f3a6541462264dae0862af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74005577"
---
이 문서는 Azure Premium Storage를 사용하여 고성능 애플리케이션을 빌드하기 위한 지침을 제공합니다. 애플리케이션에서 사용되는 기술에 적용 가능한 성능 모범 사례가 결합된 이 문서에 제공된 지침을 사용할 수 있습니다. 지침을 설명하기 위해 이 문서 전체에서 한 예로 Premium Storage에서 실행되는 SQL Server를 사용했습니다.

이 문서에서는 스토리지 계층에 대한 성능 시나리오를 해결하지만 사용자는 애플리케이션 계층을 최적화해야 합니다. 예를 들어 Azure Premium Storage에 SharePoint 팜을 호스팅하는 경우 데이터베이스 서버를 최적화하기 위해 이 문서에서 SQL Server 예제를 사용할 수 있습니다. 또한 최적의 성능을 얻기 위해 SharePoint 팜의 웹 서버 및 애플리케이션 서버를 최적화합니다.

이 문서에서는 Azure Premium Storage에서 애플리케이션 성능을 최적화하는 방법에 대한 다음과 같은 일반적인 질문에 대답합니다.

* 애플리케이션 성능을 측정하는 방법은 무엇인가요?  
* 예상되는 고성능이 표시되지 않는 이유는 무엇입니까?  
* Premium Storage에서 애플리케이션 성능에 영향을 주는 요인은 무엇입니까?  
* 이러한 요소가 Premium Storage의 애플리케이션 성능에 주는 영향은 무엇인가요?  
* IOPS, 대역폭 및 대기 시간을 최적화하는 방법은 무엇입니까?  

Premium Storage에서 실행되는 작업은 성능이 매우 중요하므로 특별히 Premium Storage에 대한 지침을 제공합니다. 적절한 예제를 제공합니다. Standard Storage 디스크가 있는 IaaS VM에서 실행되는 애플리케이션에 이러한 지침 중 일부를 적용할 수도 있습니다.