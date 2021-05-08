---
title: Oracle용 BareMetal에 대한 패치 고려 사항
description: Oracle용 BareMetal Infrastructure의 운영 체제/커널 패치 고려 사항에 관해 알아봅니다.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 5af713c776def8af558531c84013b221a8d30087
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558676"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>Oracle용 BareMetal에 대한 패치 고려 사항

이 문서에서는 Oracle용 BareMetal Infrastructure의 중요한 운영 체제/커널 패치 고려 사항을 살펴봅니다.

적절한 네트워크 성능 및 시스템 안정성을 보장하기 위해 다음 호환성 표와 같이 OS별 버전의 eNIC 및 fNIC 드라이버를 설치합니다. 

서버는 호환되는 버전이 있는 고객에게 제공됩니다. 그러나 OS(운영 체제)/커널 패치 중에 드라이버를 기본 드라이버 버전으로 롤백할 수 있습니다. 따라서 다음 OS/커널 패치 작업에 따라 적절한 드라이버 버전이 실행되고 있는지 확인해야 합니다.

| OS 공급업체 | OS 패키지 버전 | 펌웨어 버전 | eNIC 드라이버 | fNIC 드라이버 |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7.6 | 3.2.3i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 2.3.0.53 | 1.6.0.34 |

## <a name="next-steps"></a>다음 단계

Oracle용 BareMetal의 이더넷 구성에 관해 알아봅니다.

> [!div class="nextstepaction"]
> [Oracle용 BareMetal의 이더넷 구성](oracle-baremetal-ethernet.md)

