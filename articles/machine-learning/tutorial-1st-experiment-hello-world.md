---
title: 'Tutorial: Run a "Hello world!" Python script'
titleSuffix: Azure Machine Learning
description: Part 2 of the Azure Machine Learning get-started series shows how to submit a trivial "Hello world!" Python script to the cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python
---

# Tutorial: Run a "Hello world!" Python script (part 2 of 4)

In this tutorial, you learn how to use the Azure Machine Learning SDK for Python to submit and run a Python "Hello world!" script.

This tutorial is *part 2 of a four-part tutorial series* in which you learn the fundamentals of Azure Machine Learning and complete jobs-based machine learning tasks in Azure. This tutorial builds on the work you completed in [Part 1: Set up your local machine for Azure Machine Learning](tutorial-1st-experiment-sdk-setup-local.md).

In this tutorial, you will:

> [!div class="checklist"]
> * Create and run a "Hello world!" Python script locally.
> * Create a Python control script to submit "Hello world!" to Azure Machine Learning.
> * Understand the Azure Machine Learning concepts in the control script.
> * Submit and run the "Hello world!" script.
> * View your code output in the cloud.

## Prerequisites

- Completion of [part 1](tutorial-1st-experiment-sdk-setup-local.md) if you don't already have an Azure Machine Learning workspace.

## Create and run a Python script locally

Create a new subdirectory called `src` under the `tutorial` directory to store code that you want to run on an Azure Machine Learning compute cluster. In the `src` subdirectory, create the `hello.py` Python script:

```python
# src/hello.py
print("Hello world!")
```

Your project directory structure will now look like:

:::image type="content" source="media/tutorial-1st-experiment-hello-world/directory-structure.png" alt-text="Directory structure shows hello.py in src subdirectory":::


### <a name="test"></a>Test your script locally

You can run your code locally, by using your favorite IDE or a terminal. Running code locally has the benefit of interactive debugging of code.  In the window that has the activated *tutorial1* conda environment, run the Python file:

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

> [!div class="nextstepaction"]
> [I ran the script locally](?success=run-local#control-script) [I ran into an issue](https://www.research.net/r/7C2NTH7?issue=run-local)

## <a name="control-script"></a> Create a control script

A *control script* allows you to run your `hello.py` script in the cloud. You use the control script to control how and where your machine learning code is run.  

In your tutorial directory, create a new Python file called `03-run-hello.py` and copy/paste the following code into that file:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### Understand the code

Here's a description of how the control script works:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [Workspace](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) connects to your Azure Machine Learning workspace, so that you can communicate with your Azure Machine Learning resources.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py) provides a simple way to organize multiple runs under a single name. Later you can see how experiments make it easy to compare metrics between dozens of runs.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) wraps your `hello.py` code and passes it to your workspace. As the name suggests, you can use this class to _configure_ how you want your _script_ to _run_ in Azure Machine Learning. It also specifies what compute target the script will run on. In this code, the target is the compute cluster that you created in the [setup tutorial](tutorial-1st-experiment-sdk-setup-local.md).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Submits your script. This submission is called a [run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py). A run encapsulates a single execution of your code. Use a run to monitor the script progress, capture the output, analyze the results, visualize metrics, and more.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        The `run` object provides a handle on the execution of your code. Monitor its progress from the Azure Machine Learning studio with the URL that's printed from the Python script.  
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [I created the control script](?success=create-control-script#submit) [I ran into an issue](https://www.research.net/r/7C2NTH7?issue=create-control-script)

## <a name="submit"></a> Submit and run your code in the cloud

Run your control script, which in turn runs `hello.py` on the compute cluster that you created in the [setup tutorial](tutorial-1st-experiment-sdk-setup-local.md).


```bash
python 03-run-hello.py
```

> [!TIP]
> If running this code gives you an error that you do not have access to the subscription, see [Connect to a workspace](how-to-manage-workspace.md?tab=python#connect-multi-tenant) for information on authentication options.

> [!div class="nextstepaction"]
> [I submitted code in the cloud](?success=submit-to-cloud#monitor) [I ran into an issue](https://www.research.net/r/7C2NTH7?issue=submit-to-cloud)

## <a name="monitor"></a>Monitor your code in the cloud by using the studio

The output from your script will contain a link to the studio that looks something like this:
`https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>`.

Follow the link.  At first, you'll see a status of **Preparing**.  The very first run will take 5-10 minutes to complete. This is because the following occurs:

* A docker image is built in the cloud
* The compute cluster is resized from 0 to 1 node
* The docker image is downloaded to the compute. 

Subsequent runs are much quicker (~15 seconds) as the docker image is cached on the compute. You can test this by resubmitting the code below after the first run has completed.

Once the job completes, go to the **Outputs + logs** tab. There you can see a `70_driver_log.txt` file that looks like this:

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

On line 8, you see the "Hello world!" output.

The `70_driver_log.txt` file contains the standard output from a run. This file can be useful when you're debugging remote runs in the cloud.

> [!div class="nextstepaction"]
> [I saw the log in studio](?success=monitor-in-studio#next-steps) [I ran into an issue](https://www.research.net/r/7C2NTH7?issue=monitor-in-studio)

## Next steps

In this tutorial, you took a simple "Hello world!" script and ran it on Azure. You saw how to connect to your Azure Machine Learning workspace, create an experiment, and submit your `hello.py` code to the cloud.

In the next tutorial, you build on these learnings by running something more interesting than `print("Hello world!")`.

> [!div class="nextstepaction"]
> [Tutorial: Train a model](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> If you want to finish the tutorial series here and not progress to the next step, remember to [clean up your resources](tutorial-1st-experiment-bring-data.md#clean-up-resources).
