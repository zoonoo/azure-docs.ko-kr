---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "67182561"
---
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 클라우드 서비스당 [가상 머신](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)<sup>1</sup> |50 |50 |
| 클라우드 서비스당 입력 엔드포인트<sup>2</sup> |150 |150 |

<sup>1</sup> Azure Resource Manager 대신 클래식 배포 모델을 사용 하 여 만든 가상 머신은 자동으로 클라우드 서비스에 저장 됩니다. 부하 분산 및 가용성을 위해 해당 클라우드 서비스에 더 많은 가상 머신을 추가할 수 있습니다. 

<sup>2</sup>입력 엔드포인트를 사용하여 가상 컴퓨터의 클라우드 서비스 외부에서 가상 컴퓨터에 통신할 수 있습니다. 같은 클라우드 서비스나 가상 네트워크에 있는 가상 머신은 서로 간에 자동으로 통신할 수 있습니다. 자세한 내용은 [가상 머신으로 엔드포인트를 설정하는 방법](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요. 
