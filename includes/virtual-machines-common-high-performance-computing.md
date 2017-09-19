조직에는 대규모 컴퓨팅 요구 사항이 있습니다. 이러한 큰 계산 작업에는 엔지니어링 설계 및 분석, 재무 위험 계산, 이미지 렌더링, 복잡한 모델링, 몬테카를로 시뮬레이션 등이 포함됩니다. 

Azure를 사용하면 선택한 Azure 인프라 서비스 또는 관리 서비스를 사용하여 HPC(고성능 컴퓨팅) 및 일괄 처리 작업을 Azure 가상 컴퓨터 클러스터에서 실행할 수 있습니다. Azure에서는 계산 리소스를 수천 개의 VM 또는 코어로 크기 조정한 다음 필요한 리소스가 더 적을 때 규모 축소할 수 있습니다. 

Azure HPC 솔루션은 병렬 일괄 처리 작업에서 기존의 HPC 시뮬레이션에 이르기까지 계산 집약적인 Linux 및 Windows 작업을 효율적으로 실행합니다. 선도적인 HPC 응용 프로그램은 Azure VM에서 실행되도록 지원되며, 계산 집약적 작업에 맞게 전문화된 VM 크기와 이미지를 활용할 수 있습니다. 


## <a name="solution-architectures"></a>솔루션 아키텍처

Azure의 계산 집약적 솔루션 아키텍처 샘플은 다음과 같습니다.

* Linux 또는 Windows 클러스터 또는 선택한 그리드 관리자를 사용하여 가상 컴퓨터 또는 [가상 컴퓨터 확장 집합](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)에서 HPC 응용 프로그램을 실행합니다. [자세한 정보](https://azure.microsoft.com/en-us/solutions/architecture/hpc-cluster/)
* 온-프레미스 HPC 클러스터를 확장하여 최대 워크로드를 Azure VM에 오프로드("버스트")하는 하이브리드 솔루션을 만듭니다. [자세한 정보](https://azure.microsoft.com/en-us/solutions/architecture/hpc-on-prem-burst/)
* 확장성 있는 Azure [Batch](https://azure.microsoft.com/services/batch/) 관리 서비스를 사용하여 기본 인프라를 관리하지 않고 계산 집약적 작업을 실행합니다. [자세한 정보](https://azure.microsoft.com/en-us/solutions/architecture/hpc-big-compute-saas/)

## <a name="hpc-cluster-and-grid-solutions"></a>HPC 클러스터 및 그리드 솔루션
친숙한 HPC 작업 관리 도구를 Azure 가상 컴퓨터로 배포하거나 확장하고 계산 집약적 작업을 실행합니다. 옵션에는 [Slurm 작업 관리자](https://slurm.schedmd.com/)와 같은 오픈 소스 도구와 Microsoft 및 다른 게시자의 클러스터 또는 그리드 관리자가 포함되어 있습니다.

### <a name="linux-and-oss-cluster-solutions"></a>Linux 및 OSS 클러스터 솔루션
Azure Resource Manager 템플릿을 사용하여 가상 컴퓨터 또는 가상 컴퓨터 확장 집합에 다음을 포함한 Linux HPC 클러스터 솔루션을 배포합니다.

* [Slurm](https://azure.microsoft.com/documentation/templates/slurm/)
* [토크](https://azure.microsoft.com/documentation/templates/torque-cluster/)
* [PBS Professional](https://github.com/xpillons/azure-hpc/tree/master/Compute-Grid-Infra)

[5clickTemplates(5회 클릭 템플릿)](https://github.com/tanewill/5clickTemplates) 컬렉션도 참조하세요.

### <a name="grid-managers"></a>그리드 관리자
Microsoft는 상용 그리드 관리자와 협력하여 Azure VM에서 이러한 솔루션을 사용할 수 있게 합니다. 예를 들면 다음과 같습니다.

* [IBM Spectrum Symphony 및 Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)
* [Univa Grid Engine](http://www.univa.com/products/grid-engine)

### <a name="microsoft-hpc-pack"></a>Microsoft HPC 팩
[HPC 팩](https://technet.microsoft.com/library/cc514029(v=ws.11).aspx)은 Microsoft Azure 및 Windows Server 기술을 기반으로 하는 Microsoft의 무료 HPC 솔루션입니다. HPC 팩에는 [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 및 [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) VM에서 실행할 수 있는 몇 가지 옵션이 제공됩니다. 

방법 배우기:

* [Azure에서 HPC 팩 2016 클러스터 만들기](../articles/virtual-machines/windows/hpcpack-2016-cluster.md)
* [HPC 팩을 사용하여 Azure로 버스트(영문)](https://technet.microsoft.com/library/gg481749(v=ws.11).aspx)




## <a name="azure-batch"></a>Azure 배치
[Batch](../articles/batch/batch-technical-overview.md)는 클라우드에서 대규모 병렬 및 HPC(고성능 컴퓨팅) 응용 프로그램을 효율적으로 실행하기 위한 플랫폼 서비스입니다. Azure Batch는 가상 컴퓨터의 관리되는 풀에서 실행되는 계산 집약적 작업을 예약하고, 작업 요구에 맞게 계산 리소스를 자동으로 크기 조정할 수 있습니다. 

Batch SDK 및 도구를 사용하여 HPC 응용 프로그램 또는 컨테이너 작업을 Azure에 통합하고, 데이터를 Azure로 스테이징하고, 작업 실행 파이프라인을 빌드합니다. 또한 여분(낮은 우선 순위)의 Azure VM 용량을 [할인된 가격](https://azure.microsoft.com/pricing/details/batch/)으로 사용하여 Batch의 일부 작업을 실행하는 비용을 크게 줄일 수도 있습니다.

방법 배우기:

* [Batch를 사용하여 개발 시작](../articles/batch/batch-dotnet-get-started.md)
* [Azure Batch 샘플 코드 사용(영문)](https://github.com/Azure/azure-batch-samples)
* [Batch(미리 보기)에서 낮은 우선 순위 VM 사용](../articles/batch/batch-low-pri-vms.md)
* [Batch Shipyard를 사용하여 컨테이너화된 HPC 작업 실행(영문)](https://github.com/Azure/batch-shipyard)
* [Batch에서 R 언어 사용(영문)](https://github.com/Azure/doAzureParallel)

## <a name="hpc-and-gpu-vm-sizes"></a>HPC 및 GPU VM 크기
Azure는 계산 집약적 작업에 맞게 설계된 크기를 포함하여 [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 및 [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) VM에 다양한 크기를 제공합니다. 예를 들어 H16r 및 H16mr VM은 높은 처리량 백 엔드 RDMA 네트워크에 연결할 수 있습니다. 이 클라우드 네트워크는 [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) 또는 Intel MPI에서 실행되는 긴밀하게 결합된 병렬 응용 프로그램의 성능을 향상시킬 수 있습니다. N 시리즈 VM은 AI(인공 지능) 학습 및 시각화를 포함한 계산 집약적 또는 그래픽 집약적 응용 프로그램을 위해 설계된 NVIDIA GPU를 특징으로 합니다. 

자세한 정보:

* [Linux](../articles/virtual-machines/linux/sizes-hpc.md) 및 [Windows](../articles/virtual-machines/windows/sizes-hpc.md) VM에 대한 고성능 계산 크기 
* [Linux](../articles/virtual-machines/linux/sizes-gpu.md) 및 [Windows](../articles/virtual-machines/windows/sizes-gpu.md) VM에 대한 사용 가능한 GPU 크기 

방법 배우기:

* [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [MPI 응용 프로그램을 실행하기 위해 Microsoft HPC Pack을 사용하여 Windows RDMA 클러스터 설정](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Batch 풀에서 계산 집약적 VM 사용](../articles/batch/batch-pool-compute-intensive-sizes.md)

## <a name="hpc-images"></a>HPC 이미지

HPC용으로 설계된 Linux 및 Windows VM 이미지를 얻으려면 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)를 방문하세요. 예를 들면 다음과 같습니다.

* [RogueWave CentOS 기반 HPC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server for HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
* [TIBCO Grid Server 6.2.0 Engine](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [Windows 및 Linux용 Azure 데이터 과학 VM](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)


 
## <a name="hpc-applications"></a>HPC 응용 프로그램

Azure에서 사용자 지정 또는 상용 HPC 응용 프로그램을 실행합니다. 일부는 추가 VM 또는 계산 코어를 통해 효율적으로 확장하도록 벤치마킹됩니다. 다음 섹션은 예제입니다.

> [!IMPORTANT]
> 클라우드에서 실행하기 위해 라이선스나 기타 제한 사항에 대한 상용 응용 프로그램을 공급 업체에 확인합니다. 일부 공급 업체는 종량제 라이선스를 제공합니다. 솔루션에 대한 라이선스 서버가 클라우드에 필요하거나 온-프레미스 라이선스 서버에 연결할 수 있습니다.

### <a name="engineering-applications"></a>엔지니어링 응용 프로그램


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB 분산 컴퓨팅 서버](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM+](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>그래픽 및 렌더링

* Azure Batch(미리 보기)에 있는 [Autodesk Maya, 3ds Max 및 Arnold](../articles/batch/batch-rendering-service.md)

### <a name="ai-and-deep-learning"></a>AI 및 심층 학습

* [Microsoft Cognitive 도구 키트](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [데이터 과학 VM용 심층 학습 도구 키트](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [심층 학습용 Batch Shipyard 레시피](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)

## <a name="solution-partners"></a>솔루션 파트너

Azure에서 일괄 처리 및 HPC 응용 프로그램을 개발 또는 제공하는 파트너는 다음과 같습니다.

* [Cycle Computing](https://cyclecomputing.com/)(이제 [Microsoft와 제휴](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/))
* [Rescale](https://www.rescale.com/azure/)
* [UberCloud](https://www.theubercloud.com/)

## <a name="hpc-storage"></a>HPC 저장소

대규모 Batch 및 HPC 작업에는 기존 클라우드 파일 시스템의 용량을 초과하는 데이터 저장 및 액세스에 대한 요구 사항이 있습니다. Azure에서 [Lustre](http://lustre.org/) 및 [BeeGFS](http://www.beegfs.com/content/)와 같은 병렬 파일 시스템 솔루션을 구현할 수 있습니다.

자세한 정보:

* [Azure의 HPC 저장소를 위한 병렬 파일 시스템](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)


## <a name="related-azure-services"></a>관련 Azure 서비스

Azure 가상 컴퓨터, 가상 컴퓨터 확장 집합, Batch 및 관련 계산 서비스는 Azure HPC 솔루션 대부분의 기초가 됩니다. 그러나 이 솔루션에서는 다양한 Azure 관련 서비스를 이용할 수 있습니다. 다음은 목록의 일부입니다.

### <a name="storage"></a>저장소

* [Blob, 테이블 및 큐 저장소](../articles/storage/storage-introduction.md)
* [File Storage](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>데이터 및 분석
* Azure의 Hadoop 클러스터용 [HDInsight](../articles/hdinsight/hdinsight-hadoop-introduction.md)
* [데이터 팩터리](../articles/data-factory/data-factory-introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [기계 학습](../articles/machine-learning/machine-learning-what-is-machine-learning.md)
* [SQL 데이터베이스](../articles/sql-database/sql-database-technical-overview.md)

### <a name="networking"></a>네트워킹
* [Virtual Network](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>컨테이너
* [컨테이너 서비스](../articles/container-service/dcos-swarm/container-service-intro.md)
* [컨테이너 레지스트리](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>고객 사례

Azure HPC 솔루션으로 비즈니스 문제를 해결한 고객은 다음과 같습니다.

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P&C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://enterprise.microsoft.com/en-us/customer-story/industries/insurance/metlife/)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>다음 단계
* [엔지니어링 시뮬레이션](https://simulation.azure.com/), [렌더링](https://simulation.azure.com/), [금융 및 자본 시장](https://finance.azure.com/)에 대한 큰 계산 솔루션에 대해 자세히 알아봅니다.
* 최신 공지 사항은 [Microsoft HPC 및 배치 팀 블로그](http://blogs.technet.com/b/windowshpc/) 및 [Azure 블로그](https://azure.microsoft.com/blog/tag/hpc/)를 참조하세요.


