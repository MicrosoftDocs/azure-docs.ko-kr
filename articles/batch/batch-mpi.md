---
title: 다중 인스턴스 작업을 사용하여 MPI 애플리케이션 실행
description: Azure Batch에서 다중 인스턴스 작업 유형을 사용하여 MPI(메시지 전달 인터페이스) 애플리케이션을 실행하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 51fc580e0bb31e0e975c53b44887a5889a784eea
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605674"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>다중 인스턴스 작업을 사용하여 Batch에서 MPI(메시지 전달 인터페이스) 애플리케이션 실행

다중 인스턴스 작업을 통해 여러 컴퓨팅 노드에서 동시에 Azure Batch 작업을 실행할 수 있습니다. 이러한 작업을 통해 MPI(메시지 전달 인터페이스) 애플리케이션과 같은 고성능 컴퓨팅 시나리오를 Batch로 수행할 수 있습니다. 이 문서에서는 [Batch .NET](/dotnet/api/microsoft.azure.batch) 라이브러리를 사용하여 다중 인스턴스 작업을 실행하는 방법을 알아봅니다.

> [!NOTE]
> 이 문서의 예제에서는 Batch .NET, MS-MPI 및 Windows 컴퓨팅 노드에 집중하는 반면 여기에서 설명한 다중 인스턴스 작업 개념은 다른 플랫폼 및 기술(예를 들어 Linux 노드의 Python 및 Intel MPI)에 적용됩니다.

## <a name="multi-instance-task-overview"></a>다중 인스턴스 작업 개요

Batch에서 각 태스크는 일반적으로 단일 컴퓨팅 노드에서 실행됩니다. 작업에 여러 태스크를 제출하고 Batch 서비스는 노드에서 실행을 위해 각 태스크를 예약합니다. 그러나 태스크의 **다중 인스턴스 설정** 을 구성하여 Batch에 하나의 기본 태스크를 만들고 대신 여러 노드에서 여러 하위 태스크를 실행하도록 지시합니다.

:::image type="content" source="media/batch-mpi/batch_mpi_01.png" alt-text="다중 인스턴스 설정에 대 한 개요를 보여 주는 다이어그램":::

작업에 다중 인스턴스 설정을 사용하여 태스크를 제출할 때 Batch는 다중 인스턴스 태스크에 고유한 몇 가지 단계를 수행합니다.

1. Batch 서비스는 다중 인스턴스 설정에 따라 하나의 **기본** 태스크 및 여러 개의 **하위 태스크** 를 만듭니다. 작업의 총 수(주 및 모든 하위 작업)는 다중 인스턴스 설정에서 지정하는 **인스턴스**(컴퓨팅 노드)의 총 수와 일치합니다.
2. 배치는 컴퓨팅 노드 중 하나를 **마스터** 로 지정하고 주 작업이 마스터에서 실행되도록 예약합니다. 하위 작업이 다중 인스턴스 작업, 노드당 하나의 하위 작업에 할당된 컴퓨팅 노드의 나머지 부분에서 실행되도록 예약합니다.
3. 주 및 하위 작업은 다중 인스턴스 설정에서 지정하는 모든 **공용 리소스 파일** 을 다운로드합니다.
4. 공용 리소스 파일을 다운로드한 후 주 및 하위 작업에서 다중 인스턴스 설정에 지정하는 **조정 명령** 을 실행합니다. 조정 명령은 작업을 실행하기 위한 노드를 준비하는 데 일반적으로 사용됩니다. 여기에는 백그라운드 서비스 시작 (예: [MICROSOFT MPI의](/message-passing-interface/microsoft-mpi) `smpd.exe` ) 및 노드가 노드 간 메시지를 처리할 준비가 되었는지 확인 하는 작업이 포함 될 수 있습니다.
5. 주 및 모든 하위 작업에서 조정 명령이 성공적으로 완료된 *후* 주 작업은 마스터 노드에서 **애플리케이션 명령** 을 실행합니다. 애플리케이션 명령은 다중 인스턴스 작업 자체의 명령줄이며 주 작업에서만 실행됩니다. [MS mpi](/message-passing-interface/microsoft-mpi) 기반 솔루션에서는를 사용 하 여 mpi 지원 응용 프로그램을 실행 `mpiexec.exe` 합니다.

> [!NOTE]
> 기능적으로 고유하지만 "다중 인스턴스 작업"은 [StartTask](/dotnet/api/microsoft.azure.batch.starttask) 또는 [JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask)와 같은 고유 작업 유형이 아닙니다. 다중 인스턴스 작업은 단순히 다중 인스턴스 설정이 구성된 표준 Batch 작업(Batch .NET에서 [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask))입니다. 이 문서에서는 이를 **다중 인스턴스 작업** 이라고 합니다.

## <a name="requirements-for-multi-instance-tasks"></a>다중 인스턴스 작업에 대한 요구 사항

다중 인스턴스 작업은 **노드 간 통신이 활성화** 되고 **동시 작업 실행이 비활성화** 된 풀이 필요합니다. 동시 작업 실행을 사용 하지 않도록 설정 하려면 [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) 속성을 1로 설정 합니다.

> [!NOTE]
> 일괄 처리는 노드 간 통신을 사용하도록 설정한 풀의 크기를 [제한](batch-quota-limit.md#pool-size-limits)합니다.

이 코드 조각에서는 일괄 처리 .NET 라이브러리를 사용하여 다중 인스턴스 작업용 풀을 만드는 방법을 보여 줍니다.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.TaskSlotsPerNode = 1;
```

> [!NOTE]
> 노드 간 통신을 사용 하지 않도록 설정 하거나 1 보다 큰 *taskSlotsPerNode* 값을 사용 하 여 풀에서 다중 인스턴스 작업을 실행 하려고 하면 작업이 예약 되지 않으며 "활성" 상태로 무기한 유지 됩니다.

### <a name="use-a-starttask-to-install-mpi"></a>StartTask를 사용하여 MPI 설치

다중 인스턴스 작업으로 MPI 애플리케이션을 실행하려면 먼저 풀의 컴퓨팅 노드에 MPI 구현(예: MS-MPI 또는 Intel MPI)을 설치해야 합니다. 노드가 풀에 연결되거나 다시 시작될 때마다 실행하는 [StartTask](/dotnet/api/microsoft.azure.batch.starttask)를 사용하는 것이 좋습니다. 이 코드 조각은 MS-MPI 설치 패키지를 [리소스 파일](/dotnet/api/microsoft.azure.batch.resourcefile)로 지정하는 StartTask를 만듭니다. 리소스 파일이 노드로 다운로드된 후에 시작 태스크의 명령줄이 실행됩니다. 이 경우 명령줄은 MS-MPI의 무인 설치를 수행합니다.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>RDMA(원격 직접 메모리 액세스)

Batch 풀에서 컴퓨팅 노드에 대해 A9 등, [RDMA 지원 크기](../virtual-machines/sizes-hpc.md?toc=/azure/virtual-machines/windows/toc.json)를 사용하는 경우 MPI 애플리케이션은 Azure의 고성능, 지연율이 낮은 RDMA(원격 직접 메모리 액세스) 네트워크를 활용할 수 있습니다.

[Azure의 가상 컴퓨터 크기](../virtual-machines/sizes.md) (VirtualMachineConfiguration 풀의 경우) 또는 [Cloud Services의 크기](../cloud-services/cloud-services-sizes-specs.md) (cloud 구성 풀의 경우)로 지정 된 크기를 확인 합니다.

> [!NOTE]
> [Linux 컴퓨팅 노드](batch-linux-nodes.md)에서 RDMA를 활용하려면 노드에서 **Intel MPI** 를 사용해야 합니다.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Batch .NET을 사용하여 다중 인스턴스 작업 만들기

이제 풀 요구 사항 및 MPI 패키지 설치를 다루었으며 다중 인스턴스 작업을 만들어 보겠습니다. 이 코드 조각에서 표준 [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask)를 만든 다음, 해당 [MultiInstanceSettings](/dotnet/api/microsoft.azure.batch.cloudtask) 속성을 구성합니다. 이전에 설명했듯이 다중 인스턴스 작업은 고유한 작업 유형이 아니지만 다중 인스턴스 설정으로 구성된 표준 Batch 작업입니다.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>주 작업 및 하위 작업

작업에 대한 다중 인스턴스 설정을 만드는 경우 작업을 실행하는 컴퓨팅 노드 수를 지정합니다. 작업에 태스크를 제출하는 경우 Batch 서비스는 지정한 노드 수와 일치하는 하나의 **주** 작업과 충분한 **하위 작업** 을 만듭니다.

이러한 작업에는 0에서 *Numberofinstances* -1 범위의 정수 ID가 할당 됩니다. ID가 0 인 작업은 기본 작업 이며 다른 모든 Id는 하위 작업입니다. 예를 들어 작업에 대해 다음과 같은 다중 인스턴스 설정을 만드는 경우 기본 작업의 ID는 0이 고 하위 작업의 id는 1 ~ 9입니다.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>마스터 노드

다중 인스턴스 작업을 제출할 때 Batch 서비스는 컴퓨팅 노드 중 하나를 "마스터" 노드로 지정하고 주 작업이 마스터 노드에서 실행되도록 예약합니다. 하위 작업은 다중 인스턴스 작업에 할당된 노드의 나머지 부분에서 실행되도록 예약됩니다.

## <a name="coordination-command"></a>조정 명령

**조정 명령** 은 주 및 하위 작업에서 실행됩니다.

조정 명령의 호출을 차단합니다. Batch는 조정 명령이 모든 하위 작업에 대해 성공적으로 반환될 때까지 애플리케이션 명령을 실행하지 않습니다. 따라서 조정 명령은 모든 필요한 백그라운드 서비스를 시작하고 사용할 준비가 되었는지 확인한 다음 종료해야 합니다. 예를 들어 MS-MPI 버전 7을 사용하는 솔루션에 대한 이 조정 명령은 노드에서 SMPD 서비스를 시작한 다음 종료합니다.

`cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d`

이 조정 명령에서 `start`를 사용합니다. `smpd.exe` 애플리케이션은 실행 후 즉시 반환하지 않으므로 필요합니다. start 명령을 사용하지 않으면 이 조정 명령이 결과를 반환하지 않으므로 애플리케이션 명령의 실행이 차단됩니다.

## <a name="application-command"></a>애플리케이션 명령

주 작업 및 모든 하위 작업이 조정 명령 실행을 마치면 다중 인스턴스 작업의 명령줄이 주 작업에서 *만* 실행됩니다. 조정 명령과 구분하도록 **애플리케이션 명령** 이라고 합니다.

MS-MPI 애플리케이션의 경우 `mpiexec.exe`로 MPI 사용 애플리케이션을 실행하는 데 애플리케이션 명령을 사용합니다. 예를 들어 MS-MPI 버전 7을 사용하는 솔루션에 대한 애플리케이션 명령은 다음과 같습니다.

`cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe`

> [!NOTE]
> MS MPI의에서 `mpiexec.exe` 기본적으로 변수를 사용 하기 때문에 `CCP_NODES` ( [환경 변수](#environment-variables)참조) 위의 예제 응용 프로그램 명령줄은이를 제외 합니다.

## <a name="environment-variables"></a>환경 변수

Batch는 다중 인스턴스 작업에 할당된 컴퓨팅 노드의 다중 인스턴스 작업과 관련된 여러 [환경 변수](batch-compute-node-environment-variables.md)를 만듭니다. 조정 및 애플리케이션 명령줄은 실행하는 스크립트 및 프로그램을 참조할 수 있는 것처럼 이러한 환경 변수를 참조할 수 있습니다.

다음 환경 변수는 다중 인스턴스 작업에서 사용하기 위해 Batch 서비스에 의해 생성됩니다.

- `CCP_NODES`
- `AZ_BATCH_NODE_LIST`
- `AZ_BATCH_HOST_LIST`
- `AZ_BATCH_MASTER_NODE`
- `AZ_BATCH_TASK_SHARED_DIR`
- `AZ_BATCH_IS_CURRENT_NODE_MASTER`

해당 내용 및 가시성을 포함한 해당 환경 변수 및 다른 Batch 컴퓨팅 노드 환경 변수에 대한 자세한 내용은 [Compute 노드 환경 변수](batch-compute-node-environment-variables.md)를 참조하세요.

> [!TIP]
> [Batch LINUX MPI 코드 샘플](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch/article_samples/mpi) 에는 이러한 몇 가지 환경 변수를 사용할 수 있는 방법의 예가 포함 되어 있습니다.

## <a name="resource-files"></a>리소스 파일

다중 인스턴스 작업에 대해 고려해야 할 리소스 파일의 두 집합: *모든* 작업(주 및 하위 작업)이 다운로드하는 **공용 리소스 파일** 및 *주 작업에서만* 다운로드하는 다중 인스턴스 작업 자체에 지정된 **리소스 파일** 입니다.

작업에 대해 다중 인스턴스 설정에서 하나 이상의 **공용 리소스 파일** 을 지정할 수 있습니다. 이러한 공용 리소스 파일은 주 및 모든 하위 작업에 의해 [Azure Storage](../storage/common/storage-introduction.md)에서 각 노드의 **작업 공유 디렉터리** 로 다운로드됩니다. `AZ_BATCH_TASK_SHARED_DIR` 환경 변수를 사용하여 애플리케이션 및 조정 명령줄에서 작업 공유 디렉터리에 액세스할 수 있습니다. `AZ_BATCH_TASK_SHARED_DIR` 경로는 다중 인스턴스 작업에 할당된 모든 노드에서 동일하므로 주 데이터베이스와 모든 하위 작업 간의 단일 조정 명령을 공유할 수 있습니다. 배치는 원격 액세스 측면에서 디렉터리를 "공유"하지 않지만 환경 변수에 대한 팁에서 이전에 설명한 것처럼 탑재 또는 공유 지점으로 사용할 수 있습니다.

다중 인스턴스 작업 자체에 대해 지정한 리소스 파일은 작업의 작업 디렉터리 `AZ_BATCH_TASK_WORKING_DIR`에 기본적으로 다운로드됩니다. 언급한 대로 공용 리소스 파일과 달리 주 작업만이 다중 인스턴스 작업 자체에 대해 지정된 리소스 파일을 다운로드합니다.

> [!IMPORTANT]
> 명령줄에서 이러한 디렉터리를 나타내는 환경 변수 `AZ_BATCH_TASK_SHARED_DIR` 및 `AZ_BATCH_TASK_WORKING_DIR`을 항상 사용합니다. 경로를 수동으로 구성하지 마세요.

## <a name="task-lifetime"></a>작업 수명

주 작업의 수명은 전체 다중 인스턴스 작업의 수명을 제어합니다. 주 작업이 종료될 때 모든 하위 작업이 종료됩니다. 주 작업의 종료 코드는 작업의 종료 코드이며 따라서 재시도 목적에 대한 작업의 성공 여부를 결정하는 데 사용됩니다.

하위 작업 중 하나라도 실패하는 경우(예: 0이 아닌 반환 코드와 함께 종료) 전체 다중 인스턴스 작업이 실패합니다. 다중 인스턴스 작업은 종료되고 해당 재시도 한계까지 재시도됩니다.

다중 인스턴스 작업을 삭제하는 경우 주 및 모든 하위 작업도 Batch 서비스에서 삭제됩니다. 모든 하위 작업 디렉터리 및 해당 파일은 표준 작업의 경우처럼 컴퓨팅 노드에서 삭제됩니다.

[MaxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount), [MaxWallClockTime](/dotnet/api/microsoft.azure.batch.taskconstraints.maxwallclocktime) 및 [RetentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) 속성과 같은 다중 인스턴스 작업에 대한 [TaskConstraints](/dotnet/api/microsoft.azure.batch.taskconstraints)는 표준 작업에 대한 것이므로 주 및 모든 하위 작업에 적용됩니다. 그러나 다중 인스턴스 태스크를 작업에 추가한 후에는이 변경 내용이 주 작업에만 적용 되 고 모든 하위 작업은 원래 보존을 계속 해 서 사용 합니다.

최근 태스크가 다중 인스턴스 작업의 일부인 경우 계산 노드의 최근 작업 목록은 하위 작업의 ID를 반영 합니다.

## <a name="obtain-information-about-subtasks"></a>하위 작업에 대한 정보 가져오기

Batch .NET 라이브러리를 사용하여 하위 작업에 대한 정보를 가져오려면 [CloudTask.ListSubtasks](/dotnet/api/microsoft.azure.batch.cloudtask.listsubtasks) 메서드를 호출합니다. 이 메서드는 모든 하위 작업에 대한 정보 및 작업을 실행하는 컴퓨팅 노드에 대한 정보를 반환합니다. 이 정보를 통해 각 하위 작업의 루트 디렉터리, 풀 ID, 현재 상태, 종료 코드 등을 확인할 수 있습니다. 이 정보를 [PoolOperations.GetNodeFile](/dotnet/api/microsoft.azure.batch.pooloperations.getnodefile) 메서드와 함께 사용하여 하위 작업의 파일을 가져올 수 있습니다. 이 메서드는 기본 작업 (ID 0)에 대 한 정보를 반환 하지 않습니다.

> [!NOTE]
> 별도로 언급하지 않는 한 다중 인스턴스 [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) 자체에서 작동하는 Batch .NET 메서드는 *주 작업에만* 적용됩니다. 예를 들어 다중 인스턴스 작업에서 [CloudTask.ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask.listnodefiles) 메서드를 호출하는 경우 주 작업의 파일만 반환됩니다.

다음 코드 조각은 하위 작업 정보를 얻는 방법 뿐만 아니라 실행되는 노드에서 파일 콘텐츠를 요청하는 방법을 보여 줍니다.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>코드 샘플

GitHub의 [MultiInstanceTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks) 코드 샘플에서는 다중 인스턴스 태스크를 사용하여 Batch 컴퓨팅 노드에서 [MS-MPI](/message-passing-interface/microsoft-mpi) 애플리케이션을 실행하는 방법을 보여 줍니다. 아래 단계에 따라 샘플을 실행 합니다.

### <a name="preparation"></a>준비

1. [MS MPI SDK 및 Redist 설치 관리자](/message-passing-interface/microsoft-mpi) 를 다운로드 하 여 설치 합니다. 설치 후에는 MS MPI 환경 변수가 설정 되었는지 확인할 수 있습니다.
1. [MPIHelloWorld](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld) 샘플 MPI 프로그램의 *릴리스* 버전을 빌드합니다. 이는 다중 인스턴스 태스크를 통해 컴퓨팅 노드에서 실행할 프로그램입니다.
1. `MPIHelloWorld.exe`(2 단계에서 빌드한) 및 `MSMpiSetup.exe` (1 단계에서 다운로드 함)를 포함 하는 zip 파일을 만듭니다. 다음 단계의 애플리케이션 패키지로 이 Zip 파일을 업로드합니다.
1. [Azure Portal](https://portal.azure.com)을 사용하여 "MPIHelloWorld"라는 Batch [애플리케이션](batch-application-packages.md)을 만들고, 이전 단계에서 만든 Zip 파일을 버전 "1.0"의 애플리케이션 패키지로 지정합니다. 자세한 내용은 [애플리케이션 업로드 및 관리](batch-application-packages.md#upload-and-manage-applications)를 참조하세요.

> [!TIP]
> *릴리스* 버전을 빌드하면 `MPIHelloWorld.exe` `msvcp140d.dll` `vcruntime140d.dll` 응용 프로그램 패키지에 추가 종속성 (예: 또는)을 포함할 필요가 없습니다.

### <a name="execution"></a>실행

1. GitHub에서 [azure-batch-샘플 .zip 파일](https://github.com/Azure/azure-batch-samples/archive/master.zip) 을 다운로드 합니다.
1. Visual Studio 2019에서 MultiInstanceTasks **솔루션** 을 엽니다. `MultiInstanceTasks.sln` 솔루션 파일은 다음 위치에 있습니다.

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
1. **Microsoft.Azure.Batch.Samples.Common** 프로젝트의 `AccountSettings.settings`에 Batch 계정 및 Storage 계정의 자격 증명을 입력합니다.
1. MultiInstanceTasks 솔루션을 **빌드 및 실행** 하여 Batch 풀의 컴퓨팅 노드에서 MPI 샘플 애플리케이션을 실행합니다.
1. *선택 사항*: 리소스를 삭제하기 전에 [Azure Portal](https://portal.azure.com) 또는 [Batch Explorer](https://azure.github.io/BatchExplorer/)를 사용하여 샘플 풀, 작업 및 태스크("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask")를 검사합니다.

> [!TIP]
> Visual Studio가 아직 없는 경우 [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/) 를 무료로 다운로드할 수 있습니다.

`MultiInstanceTasks.exe`는 다음과 유사하게 출력됩니다.

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>다음 단계

- [Azure Batch에서 Linux에 대 한 MPI 지원](https://docs.microsoft.com/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch)에 대해 자세히 알아보세요.
- Azure Batch MPI 솔루션에서 사용하기 위해 [Linux 컴퓨팅 노드 풀을 만드는 방법](batch-linux-nodes.md)을 알아봅니다.
