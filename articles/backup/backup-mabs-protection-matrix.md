---
title: Azure Backup Server에서 백업할 수 있는 항목
description: 이 문서에서는 Azure Backup Server에서 보호하는 모든 워크로드, 데이터 형식 및 설치 프로그램을 나열하는 지원 매트릭스를 제공합니다.
ms.date: 11/13/2018
ms.topic: conceptual
ms.openlocfilehash: c7aa8823ca2bf638c6b726c504cddf8c5217fa19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444046"
---
# <a name="azure-backup-server-protection-matrix"></a>Azure Backup Server 보호 매트릭스

이 문서에는 다양한 서버 및 Azure Backup Server로 보호할 수 있는 워크로드가 나와 있습니다. 다음 매트릭스는 Azure Backup Server를 사용하여 보호할 수 있는 항목을 나열합니다.

## <a name="protection-support-matrix"></a>보호 지원 매트릭스

|워크로드|버전|Azure Backup 서버</br> installation|지원되는 Azure 백업 서버|보호 및 복구|
|------------|-----------|---------------|--------------|--------------|
|클라이언트 컴퓨터(64비트 및 32비트)|윈도우 10|물리적 서버<br /><br />Hyper-V 가상 머신<br /><br />VMware 가상 머신|V3, V2|볼륨, 공유, 폴더, 파일, 중복 제거된 볼륨<br /><br />보호된 볼륨은 NTFS여야 합니다. FAT 및 FAT32는 지원되지 않습니다.<br /><br />볼륨은 1GB 이상이어야 합니다. Azure 백업 서버는 VSS(볼륨 섀도우 복사 서비스)를 사용하여 데이터 스냅숏을 만들고 볼륨이 1GB 이상인 경우에만 스냅숏이 작동합니다.|
|클라이언트 컴퓨터(64비트 및 32비트)|Windows 8.1|물리적 서버<br /><br />Hyper-V 가상 머신|V3, V2|파일<br /><br />보호된 볼륨은 NTFS여야 합니다. FAT 및 FAT32는 지원되지 않습니다.<br /><br />볼륨은 1GB 이상이어야 합니다. Azure 백업 서버는 VSS(볼륨 섀도우 복사 서비스)를 사용하여 데이터 스냅숏을 만들고 볼륨이 1GB 이상인 경우에만 스냅숏이 작동합니다.|
|클라이언트 컴퓨터(64비트 및 32비트)|Windows 8.1|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)|V3, V2|볼륨, 공유, 폴더, 파일, 중복 제거된 볼륨<br /><br />보호된 볼륨은 NTFS여야 합니다. FAT 및 FAT32는 지원되지 않습니다.<br /><br />볼륨은 1GB 이상이어야 합니다. Azure 백업 서버는 VSS(볼륨 섀도우 복사 서비스)를 사용하여 데이터 스냅숏을 만들고 볼륨이 1GB 이상인 경우에만 스냅숏이 작동합니다.|
|클라이언트 컴퓨터(64비트 및 32비트)|Windows 8|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신|V3, V2|볼륨, 공유, 폴더, 파일, 중복 제거된 볼륨<br /><br />보호된 볼륨은 NTFS여야 합니다. FAT 및 FAT32는 지원되지 않습니다.<br /><br />볼륨은 1GB 이상이어야 합니다. Azure 백업 서버는 VSS(볼륨 섀도우 복사 서비스)를 사용하여 데이터 스냅숏을 만들고 볼륨이 1GB 이상인 경우에만 스냅숏이 작동합니다.|
|클라이언트 컴퓨터(64비트 및 32비트)|Windows 8|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)|V3, V2|볼륨, 공유, 폴더, 파일, 중복 제거된 볼륨<br /><br />보호된 볼륨은 NTFS여야 합니다. FAT 및 FAT32는 지원되지 않습니다.<br /><br />볼륨은 1GB 이상이어야 합니다. Azure 백업 서버는 VSS(볼륨 섀도우 복사 서비스)를 사용하여 데이터 스냅숏을 만들고 볼륨이 1GB 이상인 경우에만 스냅숏이 작동합니다.|
|클라이언트 컴퓨터(64비트 및 32비트)|Windows 7|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신|V3, V2|볼륨, 공유, 폴더, 파일, 중복 제거된 볼륨<br /><br />보호된 볼륨은 NTFS여야 합니다. FAT 및 FAT32는 지원되지 않습니다.<br /><br />볼륨은 1GB 이상이어야 합니다. Azure 백업 서버는 VSS(볼륨 섀도우 복사 서비스)를 사용하여 데이터 스냅숏을 만들고 볼륨이 1GB 이상인 경우에만 스냅숏이 작동합니다.|
|클라이언트 컴퓨터(64비트 및 32비트)|Windows 7|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)|V3, V2|볼륨, 공유, 폴더, 파일, 중복 제거된 볼륨<br /><br />보호된 볼륨은 NTFS여야 합니다. FAT 및 FAT32는 지원되지 않습니다.<br /><br />볼륨은 1GB 이상이어야 합니다. Azure 백업 서버는 VSS(볼륨 섀도우 복사 서비스)를 사용하여 데이터 스냅숏을 만들고 볼륨이 1GB 이상인 경우에만 스냅숏이 작동합니다.|
|서버(64비트)|Windows Server 2019|Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우)<br /><br />VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /><br />물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신<br /> <br /> Azure Stack|V3 <br />Nano 서버 아님|볼륨, 공유, 폴더, 파일, 시스템 상태/완전 복구, 중복 제거된 볼륨|
|서버(32비트 및 64비트)|Windows Server 2016|Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우)<br /><br />VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /><br />물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신<br /> <br /> Azure Stack|V3, V2<br />Nano 서버 아님|볼륨, 공유, 폴더, 파일, 시스템 상태/완전 복구, 중복 제거된 볼륨|
|서버(32비트 및 64비트)|Windows Server 2012 R2 - Datacenter 및 Standard|Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우)<br /> <br /> Azure Stack|V3, V2|볼륨, 공유, 폴더, 파일<br /><br />Windows Server 2012 삭제된 볼륨을 보호하려면 Azure 백업 서버가 최소한 Windows Server 2012 R2에서 실행중이어야 합니다.|
|서버(32비트 및 64비트)|Windows Server 2012 R2 - Datacenter 및 Standard|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|볼륨, 공유, 폴더, 파일, 시스템 상태/완전 복구<br /><br />Windows 서버 2012 삭제 된 볼륨을 보호 하기 위해 Windows 서버 2012 또는 2012 R2에서 실행 되 고 있어야 합니다.|
|서버(32비트 및 64비트)|Windows Server 2012/2012 SP1 - Datacenter 및 Standard|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신<br /> <br /> Azure Stack|V3, V2|볼륨, 공유, 폴더, 파일, 시스템 상태/완전 복구<br /><br />Windows Server 2012 삭제된 볼륨을 보호하려면 Azure 백업 서버가 최소한 Windows Server 2012 R2에서 실행중이어야 합니다.|
|서버(32비트 및 64비트)|Windows Server 2012/2012 SP1 - Datacenter 및 Standard|Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우)<br /> <br /> Azure Stack|V3, V2|볼륨, 공유, 폴더, 파일<br /><br />Windows Server 2012 삭제된 볼륨을 보호하려면 Azure 백업 서버가 최소한 Windows Server 2012 R2에서 실행중이어야 합니다.|
|서버(32비트 및 64비트)|Windows Server 2012/2012 SP1 - Datacenter 및 Standard|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|볼륨, 공유, 폴더, 파일, 시스템 상태/완전 복구<br /><br />Windows Server 2012 삭제된 볼륨을 보호하려면 Azure 백업 서버가 최소한 Windows Server 2012 R2에서 실행중이어야 합니다.|
|서버(32비트 및 64비트)|Windows Server 2008 R2 SP1 - Standard 및 Enterprise|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신<br /> <br /> Azure Stack|V3, V2<br />SP1을 실행하고 [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)을 설치해야 합니다.|볼륨, 공유, 폴더, 파일, 시스템 상태/완전 복구|
|서버(32비트 및 64비트)|Windows Server 2008 R2 SP1 - Standard 및 Enterprise|Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우)<br /> <br /> Azure Stack|V3, V2<br />SP1을 실행하고 [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)을 설치해야 합니다.|볼륨, 공유, 폴더, 파일|
|서버(32비트 및 64비트)|Windows Server 2008 R2 SP1 - Standard 및 Enterprise|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2<br />SP1을 실행하고 [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)을 설치해야 합니다.|볼륨, 공유, 폴더, 파일, 시스템 상태/완전 복구|
|서버(32비트 및 64비트)|Windows Server 2008 SP2|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신<br /> <br /> Azure Stack|지원되지 않음|볼륨, 공유, 폴더, 파일, 시스템 상태/완전 복구|
|서버(32비트 및 64비트)|Windows Server 2008 SP2|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|볼륨, 공유, 폴더, 파일, 시스템 상태/완전 복구|
|서버(32비트 및 64비트)|Windows Storage Server 2008|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신<br /> <br /> Azure Stack|V3, V2|볼륨, 공유, 폴더, 파일, 시스템 상태/완전 복구|
|SQL Server|SQL Server 2019|물리적 서버 <br /><br /> 온-프레미스 Hyper-V 가상 머신 <br /> <br /> Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우) <br /><br /> VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2017|물리적 서버 <br /><br /> 온-프레미스 Hyper-V 가상 머신 <br /> <br /> Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우) <br /><br /> VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2016 SP2|물리적 서버 <br /><br /> 온-프레미스 Hyper-V 가상 머신 <br /> <br /> Azure 가상 머신 <br /><br /> VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2016 SP1|물리적 서버 <br /><br /> 온-프레미스 Hyper-V 가상 머신 <br /> <br /> Azure 가상 머신 <br /><br /> VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2016|물리적 서버 <br /><br /> 온-프레미스 Hyper-V 가상 머신 <br /> <br /> Azure 가상 머신 <br /><br /> VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2014|Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우)<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2014|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2012 SP2|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2012 SP2|Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우)<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2012 SP2|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2012, SQL Server 2012 SP1|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2012, SQL Server 2012 SP1|Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우)<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2012, SQL Server 2012 SP1|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2008 R2|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2008 R2|Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우)<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2008 R2|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2008|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2008|Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우)<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|SQL Server|SQL Server 2008|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|모든 배포 시나리오: 데이터베이스|
|Exchange|Exchange 2016|물리적 서버<br/><br/> 온-프레미스 Hyper-V 가상 머신<br /> <br /> Azure Stack<br /> <br />Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우)|V3, V2|보호(모든 배포 시나리오): 독립 실행형 Exchange Server, DAG(데이터베이스 사용 가능 그룹)의 데이터베이스<br /><br />복구(모든 배포 시나리오): 사서함, DAG의 사서함 데이터베이스<br/><br/> ReFS에 Exchange 백업은 지원되지 않음 |
|Exchange|Exchange 2016|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|보호(모든 배포 시나리오): 독립 실행형 Exchange Server, DAG(데이터베이스 사용 가능 그룹)의 데이터베이스<br /><br />복구(모든 배포 시나리오): 사서함, DAG의 사서함 데이터베이스<br/><br/> ReFS에 Exchange 백업은 지원되지 않음 |
|Exchange|Exchange 2013|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신<br /> <br /> Azure Stack|V3, V2|보호(모든 배포 시나리오): 독립 실행형 Exchange Server, DAG(데이터베이스 사용 가능 그룹)의 데이터베이스<br /><br />복구(모든 배포 시나리오): 사서함, DAG의 사서함 데이터베이스<br/><br/> ReFS에 Exchange 백업은 지원되지 않음 |
|Exchange|Exchange 2013|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|보호(모든 배포 시나리오): 독립 실행형 Exchange Server, DAG(데이터베이스 사용 가능 그룹)의 데이터베이스<br /><br />복구(모든 배포 시나리오): 사서함, DAG의 사서함 데이터베이스<br/><br/> ReFS에 Exchange 백업은 지원되지 않음 |
|Exchange|Exchange 2010|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신<br /> <br /> Azure Stack|V3, V2|보호(모든 배포 시나리오): 독립 실행형 Exchange Server, DAG(데이터베이스 사용 가능 그룹)의 데이터베이스<br /><br />복구(모든 배포 시나리오): 사서함, DAG의 사서함 데이터베이스<br/><br/> ReFS에 Exchange 백업은 지원되지 않음 |
|Exchange|Exchange 2010|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|보호(모든 배포 시나리오): 독립 실행형 Exchange Server, DAG(데이터베이스 사용 가능 그룹)의 데이터베이스<br /><br />복구(모든 배포 시나리오): 사서함, DAG의 사서함 데이터베이스<br/><br/> ReFS에 Exchange 백업은 지원되지 않음 |
|SharePoint|SharePoint 2016|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신<br /><br />Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우)<br /><br />VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|보호(모든 배포 시나리오): 팜, 프런트 엔드 웹 서버 콘텐츠<br /><br />복구(모든 배포 시나리오): 팜, 데이터베이스, 웹 애플리케이션, 파일 또는 목록 항목, SharePoint 검색, 프런트 엔드 웹 서버<br /><br />콘텐츠 데이터베이스에 SQL Server 2012 AlwaysOn 기능을 사용하는 SharePoint 팜을 보호하는 기능은 지원되지 않습니다.|
|SharePoint|SharePoint 2013|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신<br /> <br /> Azure Stack|V3, V2|보호(모든 배포 시나리오): 팜, 프런트 엔드 웹 서버 콘텐츠<br /><br />복구(모든 배포 시나리오): 팜, 데이터베이스, 웹 애플리케이션, 파일 또는 목록 항목, SharePoint 검색, 프런트 엔드 웹 서버<br /><br />콘텐츠 데이터베이스에 SQL Server 2012 AlwaysOn 기능을 사용하는 SharePoint 팜을 보호하는 기능은 지원되지 않습니다.|
|SharePoint|SharePoint 2013|Azure 가상 시스템(워크로드가 Azure 가상 컴퓨터로 실행되는 경우) - <br /> <br /> Azure Stack|V3, V2|보호(모든 배포 시나리오): 팜, SharePoint 검색, 프런트 엔드 웹 서버 콘텐츠<br /><br />복구(모든 배포 시나리오): 팜, 데이터베이스, 웹 애플리케이션, 파일 또는 목록 항목, SharePoint 검색, 프런트 엔드 웹 서버<br /><br />콘텐츠 데이터베이스에 SQL Server 2012 AlwaysOn 기능을 사용하는 SharePoint 팜을 보호하는 기능은 지원되지 않습니다.|
|SharePoint|SharePoint 2013|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|보호(모든 배포 시나리오): 팜, SharePoint 검색, 프런트 엔드 웹 서버 콘텐츠<br /><br />복구(모든 배포 시나리오): 팜, 데이터베이스, 웹 애플리케이션, 파일 또는 목록 항목, SharePoint 검색, 프런트 엔드 웹 서버<br /><br />콘텐츠 데이터베이스에 SQL Server 2012 AlwaysOn 기능을 사용하는 SharePoint 팜을 보호하는 기능은 지원되지 않습니다.|
|SharePoint|SharePoint 2010|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신<br /> <br /> Azure Stack|V3, V2|보호(모든 배포 시나리오): 팜, SharePoint 검색, 프런트 엔드 웹 서버 콘텐츠<br /><br />복구(모든 배포 시나리오): 팜, 데이터베이스, 웹 애플리케이션, 파일 또는 목록 항목, SharePoint 검색, 프런트 엔드 웹 서버|
|SharePoint|SharePoint 2010|Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우)<br /> <br /> Azure Stack|V3, V2|보호(모든 배포 시나리오): 팜, SharePoint 검색, 프런트 엔드 웹 서버 콘텐츠<br /><br />복구(모든 배포 시나리오): 팜, 데이터베이스, 웹 애플리케이션, 파일 또는 목록 항목, SharePoint 검색, 프런트 엔드 웹 서버|
|SharePoint|SharePoint 2010|VMWare의 Windows 가상 머신(VMWare의 Windows 가상 머신에서 실행되는 워크로드를 보호)<br /> <br /> Azure Stack|V3, V2|보호(모든 배포 시나리오): 팜, SharePoint 검색, 프런트 엔드 웹 서버 콘텐츠<br /><br />복구(모든 배포 시나리오): 팜, 데이터베이스, 웹 애플리케이션, 파일 또는 목록 항목, SharePoint 검색, 프런트 엔드 웹 서버|
|하이퍼 V 호스트 - 하이퍼 V 호스트 서버, 클러스터 또는 VM의 MABS 보호 에이전트|Windows Server 2019|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신|V3|보호: Hyper-V 컴퓨터, CSV(클러스터 공유 볼륨)<br /><br />복구: 가상 머신, 파일 및 폴더의 항목 수준 복구, 볼륨, 가상 하드 드라이브|
|하이퍼 V 호스트 - 하이퍼 V 호스트 서버, 클러스터 또는 VM의 MABS 보호 에이전트|Windows Server 2016|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신|V3, V2|보호: Hyper-V 컴퓨터, CSV(클러스터 공유 볼륨)<br /><br />복구: 가상 머신, 파일 및 폴더의 항목 수준 복구, 볼륨, 가상 하드 드라이브|
|하이퍼 V 호스트 - 하이퍼 V 호스트 서버, 클러스터 또는 VM의 MABS 보호 에이전트|Windows Server 2012 R2 - Datacenter 및 Standard|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신|V3, V2|보호: Hyper-V 컴퓨터, CSV(클러스터 공유 볼륨)<br /><br />복구: 가상 머신, 파일 및 폴더의 항목 수준 복구, 볼륨, 가상 하드 드라이브|
|하이퍼 V 호스트 - 하이퍼 V 호스트 서버, 클러스터 또는 VM의 MABS 보호 에이전트|Windows Server 2012 - Datacenter 및 Standard|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신|V3, V2|보호: Hyper-V 컴퓨터, CSV(클러스터 공유 볼륨)<br /><br />복구: 가상 머신, 파일 및 폴더의 항목 수준 복구, 볼륨, 가상 하드 드라이브|
|하이퍼 V 호스트 - 하이퍼 V 호스트 서버, 클러스터 또는 VM의 MABS 보호 에이전트|Windows Server 2008 R2 SP1 - Enterprise 및 Standard|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신|V3, V2|보호: Hyper-V 컴퓨터, CSV(클러스터 공유 볼륨)<br /><br />복구: 가상 머신, 파일 및 폴더의 항목 수준 복구, 볼륨, 가상 하드 드라이브|
|하이퍼 V 호스트 - 하이퍼 V 호스트 서버, 클러스터 또는 VM의 MABS 보호 에이전트|Windows Server 2008 SP2|물리적 서버<br /><br />온-프레미스 Hyper-V 가상 머신|지원되지 않음|보호: Hyper-V 컴퓨터, CSV(클러스터 공유 볼륨)<br /><br />복구: 가상 머신, 파일 및 폴더의 항목 수준 복구, 볼륨, 가상 하드 드라이브|
|VMware VM|VMware vCenter/vSphere ESX/ESXi 라이선스 버전 5.5/6.0/6.5 |물리적 서버, <br/>온-프레미스 Hyper-V VM, <br/> VMWare의 Windows VM|V3, V2|클러스터 공유 볼륨(CSVs), NFS 및 SAN 스토리지의 VMware VM<br /> 파일 및 폴더의 항목 수준 복구는 Windows VM에만 사용 가능하며, VMware vApps는 지원되지 않습니다.|
|VMware VM|[VMware vSphere 라이센스 버전 6.7](backup-azure-backup-server-vmware.md#vmware-vsphere-67) |물리적 서버, <br/>온-프레미스 Hyper-V VM, <br/> VMWare의 Windows VM|V3|클러스터 공유 볼륨(CSVs), NFS 및 SAN 스토리지의 VMware VM<br /> 파일 및 폴더의 항목 수준 복구는 Windows VM에만 사용 가능하며, VMware vApps는 지원되지 않습니다.|
|Linux|Hyper-V 또는 VMware 게스트로 실행되는 Linux|물리적 서버, <br/>온-프레미스 Hyper-V VM, <br/> VMWare의 Windows VM|V3, V2|Hyper-V는 Windows Server 2012 R2 또는 Windows Server 2016에서 실행되어야 합니다. 보호: 전체 가상 머신<br /><br />복구: 전체 가상 머신 <br/><br/> 파일에 일관적인 스냅샷만 지원됩니다. <br/><br/> 지원되는 Linux 배포 및 버전의 전체 목록은 [Azure 보증 배포판의 Linux](../virtual-machines/linux/endorsed-distros.md)를 참조하세요.|

## <a name="azure-expressroute-support"></a>Azure 익스프레스루트 지원

공용 피어링(이전 회로에서 사용 가능) 및 Microsoft 피어링을 사용하여 Azure ExpressRoute를 통해 데이터를 백업할 수 있습니다. 개인 피어링을 통해 백업은 지원되지 않습니다.

공용 피어링: 다음 도메인/주소에 대한 액세스 확인:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Microsoft 피어링을 사용하면 다음 서비스/지역 및 관련 커뮤니티 값을 선택하십시오.

* Azure Active Directory (12076:5060)
* Microsoft Azure 지역(복구 서비스 볼트의 위치에 따라 다)
* Azure 저장소(복구 서비스 자격 증명 모음의 위치에 따라 다릅니다)

자세한 내용은 [ExpressRoute 라우팅 요구 사항을](https://docs.microsoft.com/azure/expressroute/expressroute-routing)참조하십시오.

>[!NOTE]
>새 회로의 경우 공용 피어링이 더 이상 사용되지 않습니다.

## <a name="cluster-support"></a>클러스터 지원

Azure Backup Server는 다음과 같은 클러스터 된 애플리케이션에서 데이터를 보호할 수 있습니다.

* 파일 서버

* SQL Server

* Hyper-V - 확장된 MABS 보호 에이전트를 사용하여 Hyper-V 클러스터를 보호하는 경우 보호된 Hyper-V 워크로드에 대한 보조 보호를 추가할 수 없습니다.

    Windows Server 2008 R2에서 Hyper-V를 실행하는 경우 KB[975354](https://support.microsoft.com/kb/975354)에 설명된 업데이트를 설치해야 합니다.
    클러스터 구성에서 Hyper-V를 Windows Server 2008 R2에서 실행하는 경우 SP2 및 KB[971394](https://support.microsoft.com/kb/971394)를 설치해야 합니다.

* Exchange Server - Azure Backup Server는 지원되는 Exchange Server 버전의 공유되지 않은 디스크 클러스터(클러스터 연속 복제)를 보호할 수 있으며 로컬 연속 복제를 위해 구성된 Exchange Server도 보호할 수 있습니다.

* SQL Server - Azure Backup Server는 CSV(클러스터 공유 볼륨)에서 호스트되는 SQL Server 데이터베이스를 백업할 수 없습니다.

Azure Backup Server는 MABS 서버와 동일한 도메인에 있는 클러스터 워크로드와 자식 또는 신뢰할 수 있는 도메인에 있는 클러스터 워크로드를 보호할 수 있습니다. 트러스트되지 않은 도메인 또는 작업 그룹의 데이터 원본을 보호하려면 단일 서버의 경우 NTLM 또는 인증서 인증을 사용하거나 클러스터의 경우 인증서 인증만 사용합니다.
