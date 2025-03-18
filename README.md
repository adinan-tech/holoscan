
# Deploy NVIDIA Holoscan on Oracle Linux A10 Instance

This Oracle Cloud Infrastructure (OCI) Terraform stack deploys an A10 (VM.GPU.A10.1) instance running Oracle Linux, installs NVIDIA Holoscan, and configures all required dependencies, including Docker and the NVIDIA container toolkit. The deployment occurs within an existing Virtual Cloud Network (VCN) and allows SSH access to the deployed VM for administration and troubleshooting. Additionally, a Jupyter Notebook service is set up to facilitate Holoscan usage.

## Prerequisites

Before deploying, ensure you have the following:

1. **OCI Account**: A valid Oracle Cloud Infrastructure (OCI) account with access to GPU instances.
2. **NVIDIA NGC API Key**: Required to pull the Holoscan container image from NVIDIA's NGC Catalog. You can generate an API key by following the instructions in NVIDIA's documentation: [Generate Your NGC Keys](https://docs.nvidia.com/nemo/retriever/extraction/ngc-api-key/).
3. **SSH Key Pair**: A public SSH key for accessing the deployed instance.
4. **Existing VCN and Public Subnet**: The deployment requires an existing VCN and a public subnet in OCI.

## Resource Configuration


### Required Inputs

The following variables are visible and need to be configured in the deployment UI:

| Parameter               | Description                                                           |
| ----------------------- | --------------------------------------------------------------------- |
| **Compartment OCID**    | OCI Compartment where the GPU VM will be deployed.                    |
| **VCN ID**              | ID of the Virtual Cloud Network where resources will be provisioned.  |
| **Subnet ID**           | The public subnet within the VCN for deployment.                             |
| **VM Display Name**     | Custom display name for the VM instance.                              |
| **SSH Public Key**      | Your public SSH key for remote access.                                |
| **Availability Domain** | The availability domain where the instance will be deployed.          |
| **NVIDIA API Key**      | Required to authenticate with NVIDIA NGC and pull the Holoscan image. |


## Deployment Time  
Running the cloud-init script **takes approximately 10-15 minutes**, as it includes pulling the Holoscan container image and setting up the environment.  
During this time, the **Jupyter Notebook link will not be immediately available**.  

To **monitor progress**, SSH into the VM and run:  
```
tail -f /var/log/cloud-init-output.log
```

## CloudInit Script Automation

The OCI ORM automation will create a VM and will then a `cloudinit.sh` script is executed on instance boot to:

1. **Install required packages** (Docker, NVIDIA container toolkit, and Python dependencies).

2. **Configure Docker** to use NVIDIA's runtime.

3. **Authenticate to NVIDIA NGC** and pull the Holoscan container.

4. **Run the Holoscan container** with GPU support, mounting required volumes.

5. **Start a Jupyter Notebook service**, accessible on port **8888**, to facilitate interactive exploration of Holoscan.

6. **Configure firewall rules** to allow access to Jupyter Notebook.

Running this code in OCI Resource Manager (ORM) will deploy the VM and configure everything automatically. Once the deployment is complete, the output will include both a public IP and a private IP. If needed, you can access the Jupyter Notebook on http://<public_ip>:8888.

## Jupyter Notebooks Folder

**Note:** The Jupyter notebooks folder is initially empty.

After starting the Jupyter server, you can access it by navigating to:  
`http://<public_ip>:8888`  

Once inside, browse to the `holoscan_jupyter_notebooks` directory to create and manage your notebooks.  

For example notebooks and detailed guidance, refer to the official NVIDIA Holoscan documentation:  
[Holoscan by Example](https://docs.nvidia.com/holoscan/sdk-user-guide/holoscan_by_example.html)