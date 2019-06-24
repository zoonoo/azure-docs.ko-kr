---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: beb08c29587e4ce522131142fd61925b5af45fa9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182273"
---
Azure Portal 또는 SMB를 사용하여 Azure 파일 공유에서 변경한 사항은 즉시 검색되지 않고 서버 엔드포인트의 변경 사항처럼 복제됩니다. Azure Files에는 아직 변경 알림/저널링이 없어서 파일이 변경될 때 동기화 세션을 자동으로 시작할 방법이 없습니다. Windows Server에서 Azure 파일 동기화는 [Windows USN 저널링](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx)을 사용하여 파일이 변경될 때 자동으로 동기화 세션을 시작합니다.<br /><br /> Azure 파일 공유의 변경 사항을 검색하기 위해 Azure 파일 동기화에는 *변경 검색 작업*이라는 예약된 작업이 있습니다. 변경 검색 작업은 파일 공유의 모든 파일을 열거한 다음 해당 파일의 동기화 버전과 비교합니다. 변경 검색 작업에서 파일이 변경된 것으로 판단되면 Azure 파일 동기화는 동기화 세션을 시작합니다. 변경 검색 작업은 24시간마다 시작됩니다. 변경 검색 작업은 Azure 파일 공유의 모든 파일을 열거하여 작동하므로 변경 검색은 큰 네임스페이스가 작은 네임스페이스보다 오래 걸립니다. 큰 네임스페이스의 경우 변경된 파일을 확인하는 것이 24시간마다 한 번 이상이 걸릴 수 있습니다.<br /><br />
REST를 사용하여 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다. <br /><br />
Windows Server의 볼륨에 대해 USN과 유사한 Azure 파일 공유에 대한 변경 검색 기능을 추가하는 방법을 모색하고 있습니다. 이 기능의 향후 개발을 위해 [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)에서 투표하여 우선 순위 지정에 도움을 주세요.
