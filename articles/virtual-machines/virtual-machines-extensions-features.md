<properties 
 pageTitle="가상 컴퓨터 확장 및 특징 정보 | Microsoft Azure" 
 description="다양한 가상 컴퓨터 확장을 연결 및 기본 관리 등, 제공 또는 향상 대상별로 그룹화하여 설명합니다." 
 services="virtual-machines" 
 documentationCenter="" 
 authors="squillace" 
 manager="timlt" 
 editor=""/>
<tags 
 ms.service="virtual-machines" 
 ms.devlang="na" 
 ms.topic="article" 
 ms.tgt_pltfrm="vm-multiple" 
 ms.workload="infrastructure-services"
 ms.date="10/31/2014" 
 ms.author="rasquill"/>
#가상 컴퓨터 확장 및 기능 정보
Microsoft Azure에서는 Azure Virtual Machine에서의 생산성을 높이는 데 활용할 수 있는 보안, 런타임, 디버깅, 관리 및 기타 기능을 구현하기 위해 Microsoft와 신뢰할 수 있는 타 공급업체가 구축한 VM 확장을 제공합니다. 이 항목에서는 Windows 및 Linux 가상 컴퓨터 모두에 제공되는 Azure VM 확장의 다양한 기능을 설명하고 각 기능에 대한 관련 문서를 안내합니다.

VM 에이전트에 대한 세부 정보와 VM 확장 지원 방법은 [VM 에이전트 및 VM 확장 기능 개요](https://msdn.microsoft.com/library/dn832621.aspx)를 참조하십시오.

##Azure VM 확장

VM 확장은 VM에서 사용하려는 대부분의 중요 기능을 구현합니다. 여기에는 암호 재설정, RDP 구성 등을 비롯한 많은 기본 기능이 포함됩니다. 지속적으로 새로운 확장이 추가되고 있으므로 Azure에서 VM이 지원 가능한 기능의 수도 지속적으로 증가하고 있습니다. 기본적으로 이미지 갤러리에서 VM을 만들 때 **IaaSDiagnostics**(현재 Windows VM만 해당), **VMAccess** 및 **BGInfo** 등, 몇 가지 기본 VM 확장이 설치됩니다. 그러나 기능 업데이트 및 새 확장의 연속성으로 인해 특정 시간에 Windows와 Linux 모두에서 모든 확장이 구현되는 것은 아닙니다.

##연결 및 기본 관리

다음 확장은 만들고 실행한 후에 VM과의 기본 연결을 사용, 재사용 또는 사용하지 않도록 설정하는 데 매우 중요합니다.

|VM 확장 이름|기능 설명|추가 정보
|---|---|---|
|VMAccessAgent(Windows)|사용자 정보 및 RDP와 SSH 연결 구성을 만들고, 업데이트하고, 재설정합니다.|[Windows](https://msdn.microsoft.com/library/dn606308.aspx)
|VMAccessForLinux(Linux)|사용자 정보 및 RDP와 SSH 연결 구성을 만들고, 업데이트하고, 재설정합니다.|[Linux](http://azure.microsoft.com/blog/2014/08/25/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/)

##배포 및 구성 관리

다음 확장에서는 다양한 종류의 배포 및 구성 관리 시나리오와 기능을 지원합니다. 아래 VM 작업 및 관리의 해당 섹션도 참조하십시오.

|VM 확장 이름|기능 설명|추가 정보|
|---|---|---|
|**MSEnterpriseApplication**|Windows System Center에서 지원에 대 한 기능을 구현합니다.|[System Center 2012 R2 가상 컴퓨터 역할](http://social.technet.microsoft.com/wiki/contents/articles/18274.system-center-2012-r2-virtual-machine-role-authoring-guide-resource-extension-package.aspx)|
|**Octopus Deploy**(DSC 확장 기반)|배포, 테스트 및 프러덕션 환경에서 ASP.NET 웹 응용 프로그램 및 Windows 서비스의 자동 배포를 지원합니다.|[Octopus Deploy 시작하기](http://docs.octopusdeploy.com/display/OD/Getting%20started)|
|**Visual Studio Release Manager**(DSC 확장 기반)|Visual Studio에서의 연속 배포를 지원합니다.|[릴리스 관리로 배포 자동화](https://msdn.microsoft.com/library/dn217874.aspx)|
|**CentosChefClient**|||
|**ChefClient**|Windows에서 Chef 클라이언트를 만듭니다. 아래 DSC 확장을 사용할 수도 있습니다.|[Chef 및 Microsoft Azure](https://www.getchef.com/solutions/azure/)|
|**LinuxChefClient**|||
|**DockerExtension**|원격 Docker 명령을 지원하기 위해 Docker 데몬을 설치합니다.|[Docker 가상 컴퓨터 확장을 사용하는 방법](virtual-machines-docker-vm-extension.md) 더 자세한 정보는 [Docker VM 확장 사용자 가이드](https://github.com/Azure/azure-docker-extension/blob/master/README.md)를 참조하십시오.|
|**DSC**|PowerShell DSC(Desired State Configuration) 확장|[Azure PowerShell DSC(Desired State Configuration) 확장](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx)|
|**PuppetEnterpriseAgent**|Puppet Enterprise의 기능을 구현합니다. |[Azure에서의 Puppet](http://puppetlabs.com/solutions/microsoft)|
|**CustomScriptExtension**(Windows)**CustomScriptForLinux**(Linux)|시작 시 또는 실행 중에 언제든 VM에서 사용자 지정 스크립트를 호출합니다.|[사용자 지정 스크립트 확장](https://msdn.microsoft.com/library/dn781373.aspx)[Linux](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)|
|**AzureCATExtensionHandler**|**IaaSDiagnostics**에서 수집한 진단 데이터와 몇 가지 다른 데이터 소스(예: [Azure 저장소 분석 메트릭스](https://msdn.microsoft.com/library/azure/hh343270.aspx))를 소비하고, SAP 호스트 제어 프로세스에서 소비하는 데 적합하도록 집계된 데이터 집합으로 변환합니다.|[SAP용 Azure Enhanced Monitoring](http://azure.microsoft.com/blog/2014/06/04/azure-enhanced-monitoring-for-sap/)|

##보안 및 보호

이 섹션의 확장은 Azure VM에 대한 중요 보안 기능을 제공합니다.

|VM 확장 이름|기능 설명|추가 정보|
|---|---|---|
|**CloudLinkSecureVMWindowsAgent**|Microsoft Azure 고객에게, 다중 테넌트 공유 인프라에서 가장 컴퓨터 데이터를 암호화하고 Azure 저장소 인프라에서 암호화된 데이터에 대한 암호화 키를 완벽히 제어하는 기능을 제공합니다.|[BitLocker 및 기본 OS 암호화를 활용하여 Microsoft Azure 가상 커퓨터 보호](http://www.cloudlinktech.com/azure)|
|**McAfeeEndpointSecurity**|악성 소프트웨어로부터 VM을 보호합니다.|[McAfee](http://www.mcafeeasap.com/)|
|**TrendMicroDSA**|침입 탐지 및 방지, 방화벽, 맬웨어 방지, 웹 평판, 로그 감사 및 무결성 모니터링을 제공하는 TrendMicro의 Deep Security 플랫폼을 구현합니다.|[Azure VM에 Trend Micro Deep Security as a Service를 설치하고 구성하는 방법](http://virtual-machines-install-trend.md)|
|**PortalProtectExtension**|Microsoft SharePoint 환경에 대한 위협으로부터 보호합니다.|[Azure에서의 SharePoint 배포 보호](http://blog.trendmicro.com/securing-sharepoint-deployment-azure/)|
|**IaaSAntimalware**|Azure 클라우드 서비스 및 가상 컴퓨터에 대한 Microsoft Antimalware는 악성 또는 원치 않는 소프트웨어가 사용자 시스템에서 스스로의 설치나 실행을 시도할 때 구성 가능한 알림을 통해 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별 및 제거하는 데 도움이 되는 실시간 보호 기능입니다.|[맬웨어 방지 설명서 다운로드](http://go.microsoft.com/fwlink/?linkid=398023&clcid=0x409)|
|**SymantecEndpointProtection**|Symantec Endpoint Protection 12.1.4 는 실제 및 가상 시스템 전체에서 보안 및 성능을 구현합니다.|[Azure VM에서 Symantec Endpoint Protection을 설치하고 구성하는 방법](http://virtual-machines-install-symantec.md)

##VM 운영 및 관리

일반적인 작업 관리 기능 및 동작을 지원합니다. 위의 배포 및 구성 관리 섹션도 참조하세요.

|**VM 확장 이름**|기능 설명|추가 정보|
|---|---|---|
|**AzureVmLogCollector**|필요에 따라 **AzureVMLogCollector** 확장을 사용하면 원격 VM 로그온 없이도 하나 이상의 클라우드 서비스 VM(웹 역할 및 작업자 역할 모두)에서 일회성 로그 수집을 수행하고 수집한 파일을 Azure 저장소 계정으로 보낼 수 있습니다. |[AzureLogCollector 확장](https://msdn.microsoft.com/library/dn927183.aspx)|
|**IaaSDiagnostics**|Azure Diagnostics를 사용하거나 사용하지 않도록 지정하고 구성하며, **AzureCATExtensionHandler**에서 SAP 모니터링을 지원하기 위해서도 사용됩니다.|[Azure Diagnostics 확장을 통한Microsoft Azure 가상 컴퓨터 모니터링](http://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/)|
|**OSPatchingForLinux**|Azure VM 관리자를 사용하여 사용자 지정 구성으로 VM OS 업데이트를 자동화합니다. OS 패치 업데이트 시기 및 빈도 지정, 설치할 패치 지정, 업데이트 후 재부팅 동작 구성 등, OSPatching 확장을 사용하여 가상 컴퓨터에 대한 OS 업데이트를 구성할 수 있습니다.|[OS 패치 확장 블로그 게시물](http://azure.microsoft.com/blog/2014/10/23/automate-linux-vm-os-updates-using-ospatching-extension/). [OS 패치 확장](https://github.com/Azure/azure-linux-extensions)에서 Github에 대한 Readme와 소스도 참조하십시오.|

##개발 및 디버깅

이러한 VM 확장은 직접적으로 사용할 목적이 아니라 Visual Studio 관련 기능에 대한 지원을 제공하며 여기서는 상세 설명을 위해 나열된 것입니다.

|VM 확장 이름|기능 설명|추가 정보|
|---|---|---|
|**VS14CTPDebugger**|Azure SDK 2.4를 사용한 VS에서의 원격 디버깅 지원|[Visual Studio에서의 원격 디버깅](https://msdn.microsoft.com/library/y7f5zaaa.aspx)|
|**VS2013Debugger**|Azure SDK 2.4를 사용한 VS에서의 원격 디버깅 지원||
|**VS2012Debugger**|Azure SDK 2.4를 사용한 VS에서의 원격 디버깅 지원||
|**RemoteDebugVS14CTP**|Azure SDK 2.3을 사용한 VS에서의 원격 디버깅 지원||
|**RemoteDebugVS2013**|Azure SDK 2.3을 사용한 VS에서의 원격 디버깅 지원||
|**RemoteDebugVS2012**|Azure SDK 2.3을 사용한 VS에서의 원격 디버깅 지원||
|**WebDeployForVSDevTest**|Windows 서버에서 IIS 및 웹 배포를 설치 및 구성합니다. 제거나 해제는 지원되지 않습니다.|

##기타 기능

이 확장은 유용할 수 있는 타 VM 기능에 대한 지원을 제공합니다.

|VM 확장 이름|기능 설명|추가 정보|
|---|---|---|
|**BGInfo**|RDP를 사용할 때 바탕 화면에 서버에 대한 유용한 통합 정보를 표시합니다.|[BGInfo 확장](https://msdn.microsoft.com/library/dn606289.aspx)|
|**HpcVmDrivers**|VM이 Azure RDMA(원격 직접 메모리 액세스) 네트워크에 액세스할 수 있게, A8 또는 A9 가상 컴퓨터에서 다음 네트워크 장치를 설치, 구성 및 유지 관리합니다.|[HpcVmDrivers 확장](https://msdn.microsoft.com/library/dn690126.aspx)

<!---HONumber=August15_HO7-->