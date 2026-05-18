# Things I found and would like to adjust

## Entry Script

- Is tempfile really the way to go in AzureML jobs and compute clusters?
  - What's documented best practice?
- I think we can add a comment why we run a subprocess instead of calling a Python function directly.
- is AzureMachineLearningFileSystem correct parametrized? validate

## Requirements.txt

Is that the latest and greatest? Need to validate with our local setup.

## UV

I'd like to use uv for running the tests locally and creating an environment. or would we only use the github workflow for that?

## test_entry

is this test complete?

# pipeline.yml

when saying parametrize, I had thought of

```yaml
settings:
  # Replace with your compute cluster name.
  default_compute: azureml:<your-compute-cluster>
  max_concurrent_runs: 1 # prevent multiple simultaneous pipeline runs (optional)
  mini_batch_size: "1kb" # target 1-3 rows per mini-batch (tune for your data and validation speed)
```

or whatever is the correct syntax for referencing these values in the parallel job definition. But if that's not supported, we can just put comments in the YAML itself.
