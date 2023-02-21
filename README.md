# Domino Experiment Management

Domino experiment management leverages [MLflow Tracking](https://mlflow.org/docs/latest/tracking.html) to enable easy logging of experiment parameters, metrics, and artifacts, while providing a Domino-native user experience to help you analyze your results.
MLflow runs as a service in your Domino cluster, fully integrated within your workspace and jobs, and honoring role-based access control. Existing MLflow experiments works right out of the box with no code changes required.

This repository holds the code and intructions that can be used to demonstrate this new capability which is available in private preview in Domino 5.4 (public preview to be released in Domino 5.5).

## Prerequisite - Create PyTorch Environment

ML Flow is included in the Domino Standard Environments, which means experiment tracking capabilities will work right out of the box. However, this example demonstrates how to use this capability with PyTorch Lightning so you will need to ensure that your environment has the PyTorch libriaries installed.

If you do not already have an environment with PyTorch, follow the instructions below to create one:

1. Navigate to the `Environments` page in the Domino UI

2. Click `Create Environment`.

3. Name your new environment.

4. In the Base Environment / Image section, select `Start from a custom base image`.

5. In the FROM line, enter `quay.io/domino/compute-environment-images:latest`.

6. Set the environment’s visbility.

7. Click `Customize Before Building`

8. In the `Dockerfile Fnstructions` section, add:

```
RUN pip install torchvision==0.14.1 torch==1.13.1 pytorch-lightning==1.9.0 protobuf==4.21.12 --user
```

9. In the `Pluggable Workspace Tools` section, add:

```
jupyter:
  title: "Jupyter (Python, R, Julia)"
  iconUrl: "/assets/images/workspace-logos/Jupyter.svg"
  start: [ "/opt/domino/workspaces/jupyter/start" ]
  supportedFileExtensions: [ ".ipynb" ]
  httpProxy:
    port: 8888
    rewrite: false
    internalPath: "/{{ownerUsername}}/{{projectName}}/{{sessionPathComponent}}/{{runId}}/{{#if pathToOpen}}tree/{{pathToOpen}}{{/if}}"
    requireSubdomain: false
jupyterlab:
  title: "JupyterLab"
  iconUrl: "/assets/images/workspace-logos/jupyterlab.svg"
  start: [  "/opt/domino/workspaces/jupyterlab/start" ]
  httpProxy:
    internalPath: "/{{ownerUsername}}/{{projectName}}/{{sessionPathComponent}}/{{runId}}/{{#if pathToOpen}}tree/{{pathToOpen}}{{/if}}"
    port: 8888
    rewrite: false
    requireSubdomain: false
vscode:
  title: "vscode"
  iconUrl: "/assets/images/workspace-logos/vscode.svg"
  start: [ "/opt/domino/workspaces/vscode/start" ]
  httpProxy:
    port: 8888
    requireSubdomain: false
rstudio:
  title: "RStudio"
  iconUrl: "/assets/images/workspace-logos/Rstudio.svg"
  start: [ "/opt/domino/workspaces/rstudio/start" ]
  httpProxy:
    port: 8888
    requireSubdomain: false
```

10. Click `Build` to create environment.

## Run Demo

### Create experiment from workspace

Experiments can be tracked within a workspace during the prototyping phase of the model. To track experiments within a workspace:

1. Create a workspace using the PyTorch environment created above.

2. Clone this repo into the directory and run the `train.py` script.

```
git clone https://github.com/ddl-jwu/experiment-management
python train.py
```

### Track experiment from a job

Experiments can be tracked from a job to ensure guaranteed reproducibility. To track experiments within a job:

1. Start a new job from the Domino UI

2. In the `File Name or Command` section, input the following:

```
git clone https://github.com/ddl-jwu/experiment-management && python train.py
```

3. Make sure the PyTorch environment that was created above is selected.

4. Click `Start` to begin the job.