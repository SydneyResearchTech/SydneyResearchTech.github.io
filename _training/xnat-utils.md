---
---

## Launching Jupyter notebook

Open a terminal on your CVL instance (either from the desktop or directly
from RONIN link) and type the following line to launch a test XNAT instance
on your CVL machine.

```bash

ubuntu@ip-10-0-5-248:~ $ jupyter notebook

  _   _          _      _
 | | | |_ __  __| |__ _| |_ ___
 | |_| | '_ \/ _` / _` |  _/ -_)
  \___/| .__/\__,_\__,_|\__\___|
       |_|
                       
Read the migration plan to Notebook 7 to learn about the new features and the actions to take if you are using extensions.

https://jupyter-notebook.readthedocs.io/en/latest/migrate_to_notebook7.html

Please note that updating to Notebook 7 might break some of your extensions.

[I 18:52:58.654 NotebookApp] Serving notebooks from local directory: /home/ubuntu
[I 18:52:58.654 NotebookApp] Jupyter Notebook 6.5.3 is running at:
[I 18:52:58.655 NotebookApp] http://localhost:8888/?token=76d3d8582bec992544edf0b39fc6689dff87e207e51f9d2f
[I 18:52:58.655 NotebookApp]  or http://127.0.0.1:8888/?token=76d3d8582bec992544edf0b39fc6689dff87e207e51f9d2f
[I 18:52:58.655 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 18:52:58.680 NotebookApp] 
    
    To access the notebook, open this file in a browser:
        file:///home/ubuntu/.local/share/jupyter/runtime/nbserver-17728-open.html
    Or copy and paste one of these URLs:
        http://localhost:8888/?token=76d3d8582bec992544edf0b39fc6689dff87e207e51f9d2f
     or http://127.0.0.1:8888/?token=76d3d8582bec992544edf0b39fc6689dff87e207e
```

The Jupyter notebook will launch on the 8888 port of the CVL host, i.e.
http://localhost:8888, and should open a firefox window showing it

### Accessing the test XNAT instance directly via RONIN link

Just like with the test XNAT instance you can use the port forwarding feature of RONIN link
to access the notebook in the browser of your local machine.

Select `CONNECT TO MACHINE` from RONIN link under your instance and then enter the `8888`
port number under `LINK TO A CUSTOM APPLICATION` and press `LINK`.

This should map `8888` on the CVL desktop to `8888` on your local machine, so it can
be accessed from your browser at `http://localhost:8888`.

![Ronin link to custom application](/assets/img/Ronin link to custom application.png){:class="img-responsive"}


## Opening the Xnat-Utils tutorial

To open the Xnat-Utils tutorial, navigate to the `git/training-resources/notebooks` directory
and open the notebook file called `Xnat-Utils Basics.ipynb`