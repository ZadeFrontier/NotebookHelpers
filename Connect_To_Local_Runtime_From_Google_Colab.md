# Connect to a Local Runtime from Google Colab to your Local Jupyter Notebook on HTTP-over-WebSocket

This Jupyter server extension allows running Jupyter notebooks that use a
WebSocket to proxy HTTP traffic. Browsers do not allow cross-domain
communication to localhost via HTTP, but do support cross-domain communication
to localhost via WebSocket.

## Setup instructions
In order to allow Colaboratory to connect to your locally running Jupyter server, you'll need to perform the following steps.

### Step 1: Install Jupyter
Install Jupyter on your local machine.

### Step 2: Install and enable the jupyter_http_over_ws jupyter extension (one-time)

The jupyter_http_over_ws extension is authored by the Colaboratory team and available on GitHub.

Open the Anaconda.exe and open cmd.exe Prompt

Run the following commands in a shell:

```shell
!pip install jupyter_http_over_ws
# Optional: Install the extension to run every time the notebook server starts.
# Adds a /http_over_websocket endpoint to the Tornado notebook server.
jupyter serverextension enable --py jupyter_http_over_ws
```

### Step 3: Start server and authenticate

New notebook servers are started normally, though you will need to set a flag to explicitly trust WebSocket connections from the Colaboratory frontend.

```shell
jupyter notebook --NotebookApp.allow_origin='https://colab.research.google.com' --port=8888 --NotebookApp.port_retries=0
```
  
Once the server has started, it will print a message with the initial backend URL used for authentication. Make a copy of this URL as you'll need to provide this in the next step.

### Step 4: Connect to the local runtime

In Colaboratory, click the "Connect" button and select "Connect to local runtime...". Enter the URL from the previous step in the dialog that appears and click the "Connect" button. After this, you should now be connected to your local runtime.

## Usage without Authentication

```shell
jupyter notebook --no-browser --allow-root --NotebookApp.allow_origin='https://colab.research.google.com' --NotebookApp.token='' --NotebookApp.disable_check_xsrf=True
```

Note: Before requests will be accepted by your Jupyter notebook, make sure to
open the browser window specified in the command-line when the notebook server
starts up. This will set an auth cookie that is required for allowing requests
(http://jupyter-notebook.readthedocs.io/en/stable/security.html).

## Troubleshooting

### Receiving 403 errors when attempting connection

If the auth cookie isn't present when a connection is attempted, you may see a
403 error. To help prevent these types of issues, consider starting your Jupyter
server using the `--no-browser` flag and open the provided link that appears in
the terminal from the same browser that you would like to connect from:

```shell
jupyter notebook --NotebookApp.allow_origin='https://colab.research.google.com' --port=8081 --no-browser
```
```shell
jupyter notebook --NotebookApp.allow_origin='https://colab.research.google.com' --port=9090 --no-browser
```
```shell
jupyter notebook --NotebookApp.allow_origin='https://colab.research.google.com' --port=8888 --no-browser
```

If you still see issues, consider retrying the above steps from an incognito
window which will prevent issues related to browser extensions.

## Uninstallation

The jupyter server extension can be disabled and removed by running the
following commands in a shell:

```shell
jupyter serverextension disable --py jupyter_http_over_ws
!pip uninstall jupyter_http_over_ws
```

## Security considerations

Make sure you trust the authors of any notebook before executing it. With a local connection, the code you execute can read, write, and delete files on your computer.
Connecting to a Jupyter notebook server running on your local machine can provide many benefits. With these benefits come serious potential risks. By connecting to a local runtime, you are allowing the Colaboratory frontend to execute code in the notebook using the local resources on your machine. This means that the notebook could:

Invoke arbitrary commands (e.g. "rm -rf /")
Access the local file system
Run malicious content on your machine
Before attempting to connect to a local runtime, make sure you trust the authors of the notebook and ensure you understand what code is being executed. For more information on the Jupyter notebook server's security model, consult http://jupyter-notebook.readthedocs.io/en/stable/security.html.

## Browser-specific settings
Note: If you're using Mozilla Firefox, you'll need to set thenetwork.websocket.allowInsecureFromHTTPS preference within the Firefox config editor. Colaboratory makes a connection to your local kernel using a WebSocket. By default, Firefox disallows connections from HTTPS domains using standard WebSockets.

## Sharing
If you share your notebook with others, the runtime on your local machine will not be shared. When others open the shared notebook, they will be connected to a standard Cloud runtime by default.

By default, all code cell outputs are stored in Google Drive. If your local connection will access sensitive data and you would like to omit code cell outputs, select Edit > Notebook settings > Omit code cell output when saving this notebook.

## Connecting to a runtime on a Google Compute Engine instance
If the Jupyter notebook server you'd like to connect to is running on another machine (e.g. Google Compute Engine instance), you can set up SSH local port forwarding to allow Colaboratory to connect to it.

Note: Google Cloud Platform provides Deep Learning VM images with Colaboratory local backend support preconfigured. Follow the how-to guides to set up your Google Compute Engine instance with local SSH port forwarding. If you use these images, skip directly to Step 4: Connect to the local runtime (using port 8888).

First, set up your Jupyter notebook server using the instructions above.

Second, establish an SSH connection from your local machine to the remote instance (e.g. Google Compute Engine instance) and specify the '-L' flag. For example, to forward port 8888 on your local machine to port 8888 on your Google Compute Engine instance, run the following:
```shell
gcloud compute ssh --zone YOUR_ZONE YOUR_INSTANCE_NAME -- -L 8888:localhost:8888
```
    
Finally, make the connection within Colaboratory by connecting to the forwarded port (follow the same instructions under Step 4: Connect to the local runtime).

