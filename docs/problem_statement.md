# Problem Statement

My customer Tobias Trostel is running AzureML pipelines. In the validation step of the pipeline, he is looking for a solution to efficiently run a test framework in a docker container that includes Julia, Python, and other tooling environments. The validation / evaluation step involves processing distributed sequence recordings, labels, and third data in the pipeline. Each sequence (from three different sources) is around 1GB in size, and there are thousands of validations to be performed. Therefore, the solution needs to support parallel processing (AzureML Parallel Step) to handle the large volume of data efficiently.

The idea is to

1. Create a docker image that includes the necessary environments and tools for the test framework.
2. Use AzureML's ParallelRunStep to run the validation step in parallel across multiple sequences.
   - [ ] Create a sample MLTable to demonstrate how to structure the input data for the ParallelRunStep.

---

## Outcome

- Validate the approach
  1. Is ParallelRunStep the right choice for running the validation step in parallel across multiple sequences? Are there any limitations or considerations we should be aware of when using ParallelRunStep for this use case?
  2. Can we create a sample MLTable to demonstrate how to structure the input data for the ParallelRunStep? How should the MLTable be structured to efficiently handle the large volume of data and support parallel processing? What are the requirements for parallelstep to work effectively with the MLTable?
  3. What are the requirements to create a docker image that includes the necessary environments and tools for the test framework? What would be an ideal base image to use for this purpose, and what are the best practices for creating a docker image that can efficiently run the validation step in AzureML pipelines? Are there any specific configurations or optimizations we should consider when creating the docker image for this use case?

## Appendix

### Original Request

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
