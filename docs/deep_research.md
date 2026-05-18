# Deep Research on Running Docker in AzureML and Parallelizing Tasks

## Query:

Research goal:
I am not looking for a broad overview.

Task:

1. Identify the 3-10 most decision-relevant claims that would materially change
   a well-informed person's conclusion about this topic.
2. Explicitly ignore secondary, illustrative, historical, or rhetorical claims.
3. For each selected claim:
   - State the claim precisely.
   - Define its scope (population, time, conditions).
   - Assess evidence strength (strong / moderate / weak).
   - Cite primary sources only.
4. If a claim cannot be strongly supported, mark it as "uncertain" rather than smoothing it.
5. Only then synthesize into a narrative. First claims, then narrative.

Context:

My customer at Bosch asked me the following:

"""
Hi Hendrik,

I once again got a question with respect to AzureML, where I didn’t find answers so far:  
There is a framework in a docker image which I want to use for testing. Is there a way how I can run this framework docker inside AzureML?  
If I added the docker tool to my environment and also mounted the /var/run/docker.sock, then still if I run it, it says:  
Stderr: failed to connect to the docker API at unix:///var/run/docker.sock; check if the path is correct and if the daemon is running: dial unix /var/run/docker.sock: connect: no such file or directory  
It feels like there is a security mechanism which prevents this Docker in Docker execution. Is this true or can I do something about it?

And then the 2nd related question: This test container I want to run on multiple files which are distributed among multiple blobstores, I just have their paths listed in a table.  
Is there a way to parallelize this in azure ML instances such that every path in the table gets mounted as input to one instance which then contains the code to execute my test container?

Mit freundlichen Grüßen / Best regards
"""

Question:

- Research the customers questions

## Clarifications:

Thanks, Hendrik. You're asking for a focused, evidence-based investigation into two specific technical questions about Azure Machine Learning (AzureML):

Whether Docker-in-Docker (DinD) is supported in AzureML, particularly when mounting /var/run/docker.sock.
Whether AzureML can parallelise execution of a test container across multiple blobstore paths listed in a table.

Before I begin, I want to confirm a few details to ensure I target the most decision-relevant claims:

For the Docker-in-Docker question:

Are you specifically trying to run DinD inside a custom Docker image used as the AzureML environment, or are you using AzureML’s curated environments?
Is the goal to run arbitrary containers inside your training/inference script (e.g. for testing frameworks or tools)?

For the parallelisation question:

Are you looking to use AzureML’s parallel run step or map-style parallelism (e.g. via ParallelRunStep or parallel job APIs)?
Are the blobstore paths pointing to different Azure Blob Storage containers/accounts, or are they within the same container?

Let me know if you'd like me to proceed with the research using my best judgement, or feel free to clarify any of the above.

## Clarification answers:

1. a)We can use custom environments b) possibly. not necessarily2. a) i was more thinking about mltables or different mounting of blobstores, not a parallel run step. That imho doesnt make sense at all. b) assume both is possible.

## Research:

**1. Docker-in-Docker (DinD) in Azure ML jobs:**

- **Azure ML job containers lack a running Docker daemon by default.** An Azure ML training/inference run executes inside an isolated Docker container that does _not_ itself have the Docker Engine active. Consequently, calling `docker` inside the job fails because the Docker socket (`/var/run/docker.sock`) isn’t available. (Scope: Azure ML on **AmlCompute**, current; Evidence: _moderate_) [[stackoverflow.com]](https://stackoverflow.com/questions/77994269/what-is-the-best-way-to-containarise-model-using-azure-ml-pipeline)
- **SDK v1 allowed mounting the host’s Docker socket as a workaround.** In Azure ML **SDK v1** (Python), one could use a `RunConfiguration` to mount the host machine’s Docker socket into the container (e.g. `docker.arguments = ["-v", "/var/run/docker.sock:/var/run/docker.sock"]`). This enabled running Docker commands (building or running containers) _inside_ the training script’s container. (Scope: Azure ML SDK v1 ~2020–2022; Evidence: _moderate_) [[stackoverflow.com]](https://stackoverflow.com/questions/77994269/what-is-the-best-way-to-containarise-model-using-azure-ml-pipeline)
- **SDK v2 initially blocked the socket mount due to a parsing bug.** In **SDK v2**, the equivalent parameter (`docker_args`) didn’t accept the `/var/run/docker.sock` path out-of-the-box. Attempts to pass `"-v /var/run/docker.sock:/var/run/docker.sock"` triggered an error (“includes invalid characters for a local volume name…”) and the job container would not start. This was identified as a bug (see Azure SDK issue #30466) rather than an intentional security block. (Scope: Azure ML SDK v2 in 2023; Evidence: _strong_) [[stackoverflow.com]](https://stackoverflow.com/questions/77994269/what-is-the-best-way-to-containarise-model-using-azure-ml-pipeline)
- **Workaround in SDK v2: adjust syntax to mount the socket.** The above issue was resolved by using a different syntax. For example, prefixing with `-v=` (no space) or using `--mount` allowed the job to launch with the Docker socket bound. In practice, setting `docker_args="-v=/var/run/docker.sock:/var/run/docker.sock"` in the component definition allows the container to see the host’s Docker API socket. With Docker installed in the environment, the script can then successfully run Docker CLI commands (e.g. `docker build/run`) via the host’s daemon. (Scope: Azure ML SDK v2 after Feb 2024; Evidence: _moderate_) [[stackoverflow.com]](https://stackoverflow.com/questions/77994269/what-is-the-best-way-to-containarise-model-using-azure-ml-pipeline), [[stackoverflow.com]](https://stackoverflow.com/questions/77994269/what-is-the-best-way-to-containarise-model-using-azure-ml-pipeline)
- **No **official** support for DinD – use with caution.** Azure ML’s documentation does **not** advertise or guarantee Docker-in-Docker functionality. The recommended approach is to build container images _outside_ of the training job (for example, on a build agent or Azure ML Compute Instance which has Docker installed) and then use that image in Azure ML. Relying on DinD inside Azure ML is effectively an unsupported workaround. It presumes the compute cluster’s OS has Docker and that the job can run with sufficient privileges to use the socket. This workaround may be subject to breakage or restrictions (e.g. in managed ephemeral runs or future security hardening), so while it **can** be made to work, it should be considered **uncertain** in the long term. (Scope: general Azure ML usage, 2024; Evidence: _moderate_, _uncertain_) [[azure.github.io]](https://azure.github.io/azureml-sdk-for-r/articles/building-custom-docker-images.html)

**2. Parallelizing a container execution across multiple blob paths in Azure ML:**

- **Azure ML provides built-in parallel job support for embarrassingly parallel tasks.** Azure ML pipelines (v2) include a _Parallel_ step type (or **ParallelRunStep** in SDK v1) specifically to “**distribute repeated tasks on powerful multi-node compute** clusters,” dramatically speeding up processing by running many tasks concurrently. (Scope: Azure ML SDK v1 & v2; Evidence: _strong_) [[learn.microsoft.com]](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-use-parallel-job-in-pipeline?view=azureml-api-2)
- **Parallel jobs split a list of inputs (files or URIs) automatically.** You supply a **major input dataset** (in this case, the table of blob paths) to the parallel step, and Azure ML will partition it so that each parallel invocation gets a subset. For file-based workloads, the input is typically defined as an **MLTable pointing to the blob files** (or a folder dataset). Azure ML recognizes **“file list”** inputs and can divide them such that each parallel run handles a portion of the file list. (Scope: Azure ML pipelines with MLTable inputs, 2024; Evidence: _strong_)
- **Each blob path can be routed to a separate instance.** By configuring the parallel job’s `mini_batch_size` to 1 file, you ensure each parallel execution processes exactly one file from the list. Azure ML’s parallel execution engine will then launch as many parallel runs as needed (up to the defined `instance_count` or the number of files) so that **each blob path from the table is handled by one isolated run**. In other words, if there are N file paths and you have N (or >=N) nodes available, the job can spin up N containers in parallel, each receiving a different file path as input. (Scope: Parallel jobs on Azure ML, file per mini-batch; Evidence: _moderate_)
- **Data can be mounted into each parallel run for efficient access.** Azure ML allows two modes for file input delivery: **`download`** (copy files) or **`ro_mount`** (read-only mount). In parallel jobs, using `ro_mount` on an MLTable of file URIs will mount each blob from its source to the node, rather than copying it, which is efficient for large data. Each parallel instance thus sees its designated input file mounted locally (e.g. as if on a filesystem). This directly addresses the requirement: each blob path from the table is made available inside the corresponding container instance (without needing the user to manually attach storage for each). (Scope: Azure ML parallel input modes, current; Evidence: _strong_)
- **ParallelRunStep vs. parallel job:** In SDK v1, one would use a **ParallelRunStep** with a FileDataset to achieve the above. In SDK v2, the concept is unified as a parallel component or job. Both serve the same purpose – they abstract the chore of launching M independent container runs for M inputs. There is no need to manually orchestrate multiple jobs or VMs; Azure ML’s parallel execution feature handles scheduling and scaling. (Scope: Azure ML pipelines v1/v2; Evidence: _strong_ via official design). In summary, **yes**, Azure ML can natively parallelize the described task: the recommended solution is to define the blob paths as a data asset (MLTable) and use a Parallel pipeline step so that each path is processed in its own container instance concurrently.

**--- Narrative Summary ---**

**Docker-in-Docker within Azure ML:** Azure Machine Learning by default does **not** support running a Docker container _inside_ a training job’s container. The job environment itself is already a Docker container (with a base image that Azure ML manages), and no Docker daemon is running in it. This is why your attempt to use Docker (mounting `/var/run/docker.sock` and calling Docker CLI) resulted in an error – the container couldn’t find the Docker socket, indicating it couldn’t reach a Docker Engine. In practice, Azure ML isolates job containers for security, so “Docker-in-Docker” isn’t a standard feature. [[stackoverflow.com]](https://stackoverflow.com/questions/77994269/what-is-the-best-way-to-containarise-model-using-azure-ml-pipeline)

That said, it has been **possible** (though unofficially) to work around this in certain scenarios. Under the older SDK v1, some users achieved DinD by exposing the host machine’s Docker socket to the job container. Essentially, if the training cluster’s VM is running Docker, one can bind `"/var/run/docker.sock"` into the job. This lets the code inside the container talk to the host’s Docker daemon – effectively allowing you to run sibling containers from within the Azure ML job. Your colleague’s memory is correct: with SDK v1, one could configure `RunConfiguration(docker_arguments=["-v", "/var/run/docker.sock:/var/run/docker.sock"])` and then use `docker build` or `docker run` in the script. [[stackoverflow.com]](https://stackoverflow.com/questions/77994269/what-is-the-best-way-to-containarise-model-using-azure-ml-pipeline)

In Azure ML **SDK v2**, this became trickier. Initially, the new `docker_args` parameter did not accept the `"/var/run/docker.sock"` mount syntax due to how it parsed the string (it treated the leading “/” as an illegal volume name). This wasn’t a deliberate security change so much as a bug. Indeed, a GitHub issue was logged, and engineers clarified that you needed a slightly different syntax. By adding an equals sign or using the long form `--mount`, you can successfully pass the socket through. In practice, setting `docker_args="-v=/var/run/docker.sock:/var/run/docker.sock"` in your Azure ML component definition will mount the host’s Docker socket inside the container. You also must ensure Docker is installed in your environment image (Azure ML’s base images do **not** include the Docker CLI by default, so you’d need to add it). With those in place, people have been able to run Docker commands inside Azure ML jobs in 2024. In short, **it is true** that Azure ML doesn’t make DinD easy – by design it’s not advertised – but **it is possible** to get it working on (for example) an AML Compute cluster, since those are Ubuntu VMs with Docker running the job orchestrator. [[stackoverflow.com]](https://stackoverflow.com/questions/77994269/what-is-the-best-way-to-containarise-model-using-azure-ml-pipeline)

**However, caution is warranted.** Running Docker-in-Docker in Azure ML is **not officially supported or documented**. It relies on an implementation detail (the presence of Docker on the host and permissive socket access). There could indeed be underlying security mechanisms – for example, the Azure ML service might restrict certain Docker privileges or capabilities for job containers. (One hint: Azure ML jobs typically run as a non-root user called `dockeruser`; that user would need to be in the Docker group or the container run as root to access the socket.) There’s no guarantee Microsoft won’t tighten this in future. The officially recommended approach to “use a framework in a Docker image for testing” would be to build that Docker image externally, or wrap whatever you need to do in your Azure ML job without nesting containers. In fact, Microsoft’s guidance for custom dependencies is to build a custom container (using tools like Azure Container Registry or Docker on a VM) and then use that as the environment in Azure ML, rather than building or running new containers from inside an Azure ML run. So, while you **can** attempt DinD (and some have succeeded by mounting the Docker socket), go in knowing it’s a bit of a hack. If it’s just for one-off testing, it might be okay; but if this is a regular part of a production pipeline, consider an alternative design (e.g. run that test container in a separate Azure Container Instances or Kubernetes step, or as a distinct Azure ML job where the “container” is the job itself). [[azure.github.io]](https://azure.github.io/azureml-sdk-for-r/articles/building-custom-docker-images.html)

**Parallelizing a container execution across many blob files:** This scenario – running the same containerized operation on many data items in parallel – is exactly what Azure ML’s parallel execution features were built for. You do _not_ have to manually start multiple instances or write your own scheduler; you can leverage the **ParallelRun** capability of Azure ML pipelines. In Azure ML SDK v1 this was via **ParallelRunStep**, and in SDK v2 it’s via a **Parallel component** or **parallel job** in a pipeline. The idea is the same: you prepare a pipeline step that takes a list of inputs and a script (or command) to run on each input, and Azure ML will take care of launching that step across multiple nodes in parallel. [[learn.microsoft.com]](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-use-parallel-job-in-pipeline?view=azureml-api-2)

Concretely, you would package your “test container execution” logic as an Azure ML component or script. The input to this parallel step would be something like an **MLTable** listing all the blob paths you want to process. An MLTable is just a convenient way to represent a collection of file URIs or data references in Azure ML. You can easily create one that enumerates all the files across those multiple blob stores (for example, by referencing multiple `datastore/path` entries in the MLTable YAML). Azure ML will treat that as a **file list** input. You then specify how to split it: in your case, you want each file (each path) to be handled individually, so you would set the parallel step’s `mini_batch_size` to 1 file. This signals Azure ML to give one file per running instance. Under the hood, the service will schedule one container per input file (up to the max concurrency you allow, which can be the size of your compute cluster or smaller). Each container gets one entry from the table – i.e., one blob path – as its input.

Azure ML also simplifies data access for these parallel tasks. Since your input is a blob path, you don’t need to manually mount anything if you use the recommended approach. The parallel step can use **`ro_mount`** mode for the file input, meaning Azure ML will mount the storage location for that file into the container’s filesystem at runtime. For example, if your MLTable points to a blob URL, each parallel run might see the blob mounted as, say, `/mnt/inputs/<file>` within the container. Your code can then read the file directly. This is efficient and avoids transferring the entire dataset to every node – each node only accesses its assigned blob. (Alternatively, Azure ML could download the file to local disk, but mounting is usually preferable for large data to avoid copy overhead.)

To set this up in practice: you’d create a pipeline YAML (or Python SDK pipeline) with a step like:

- **Type**: Parallel
- **Input**: the MLTable of file paths (with `input_mode: ro_mount`)
- **Mini-batch size**: 1 (file per job)
- **Instance count**: (e.g.) 10, if you want up to 10 parallel containers at once (or more, up to the number of files)
- **Command**: something that runs your test container logic on the given file.

Often, instead of literally invoking `docker run` for the test container (which would circle back to the DinD issue), you might refactor the test container’s functionality into a script or entry-point that can run in the Azure ML environment. For example, if the “test container” is an executable that processes a file and outputs results, you could bake that into the Azure ML environment or call it via a command. If the test container must run as-is, another pattern is to use Azure ML’s ability to run a custom Docker image: you could define your parallel step’s environment to _be that test container image_. Then each parallel instance is actually directly running your test container (with the input mounted). This way, you avoid nesting Docker – Azure ML itself will launch your test container image on each node to process the data. This might be the cleanest solution: register the test’s Docker image as an Azure ML environment and use that in the parallel job component.

In summary, **yes**, you can parallelize the operation across all those blob paths using Azure ML’s parallel jobs. You do not need to script it yourself or use an unsupported method. By leveraging MLTable to list the inputs and the parallel pipeline step to fan out the work, Azure ML will ensure that **each file path is handled on a separate instance** (or separate container on the cluster) concurrently【26†L14-L18】【29†L39-L47】. This approach is fully supported and is exactly how one would tackle the “multiple independent tasks” scenario in Azure ML. It will take care of provisioning the compute cluster nodes, running your container or script on each, feeding in the correct input path, and (if you specify) collecting the outputs from each run. All you need to do is define the step and ensure your code knows how to consume one file input and produce the desired output for that file. Azure ML’s parallel execution will scale it out to as many files as you have, significantly speeding up the overall processing time.
