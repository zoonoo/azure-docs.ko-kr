---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182151"
---
다음으로, 클러스터에서 Windows Server 2008 R2 또는 Windows Server 2012를 실행하는 서버가 있는 경우 각각의 온-프레미스 서버 또는 해당 클러스터에 속하는 Azure VM에 핫픽스 [KB2854082](https://support.microsoft.com/kb/2854082) 가 설치되어 있는지 확인해야 합니다. 클러스터에 있지만 가용성 그룹에 없는 서버 또는 VM에도 이 핫픽스를 설치해야 합니다.

각 클러스터 노드에 대한 원격 데스크톱 세션에서 로컬 디렉터리에 [KB2854082](https://support.microsoft.com/kb/2854082) 를 다운로드합니다. 그런 다음 각 클러스터 노드에 핫픽스를 순차적으로 설치합니다. 클러스터 서비스가 클러스터 노드에서 현재 실행 중인 경우 핫픽스 설치가 끝나면 서버가 다시 시작됩니다.

> [!WARNING]
> 클러스터 서비스를 중지하거나 서버를 다시 시작하면 클러스터 및 가용성 그룹의 쿼럼 상태에 영향을 미치고 클러스터가 오프라인 상태가 될 수 있습니다. 설치하는 동안 클러스터의 고가용성을 유지하려면 다음을 확인하세요.
> 
> * 클러스터가 최적의 쿼럼 상태에 있습니다. 
> * 노드에 핫픽스를 설치하기 전에 모든 클러스터 노드가 온라인 상태입니다.
> * 클러스터의 다른 노드에 핫픽스를 설치하기 전에 서버를 완전히 다시 시작하는 작업을 포함하여 한 노드에서 핫픽스 설치를 완료할 때까지 실행하도록 허용합니다.
> 
> 

