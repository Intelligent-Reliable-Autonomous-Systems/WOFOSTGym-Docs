Getting Started
===============

Installation
------------

To use Wofost-Gym, first clone the repository from GitHub. This ensures that the submodules
pcse and pcse_gym are included.

.. code-block:: console

  git clone git@github.com:Intelligent-Reliable-Autonomous-Systems/WOFOSTGym.git

We recommend using `conda <https://conda.org/>`_ to manage the virtual environment. Code was written using
Python 3.12, although later versions may be compatible. Perform:

.. code-block:: console

    conda create -n <env-name> python=3.12
    conda activate <env-name>

Now, we can install the required dependencies for WOFOSTGym using pip. Perform the following (in order).

.. code-block:: console

    pip install -e pcse -e pcse_gym
    pip install tyro torch omegaconf wandb tensorboard

Congratulations! WOFOST-Gym has been successfully installed. 

For Imitation Learning and IRL experiments, the following packages are also required: 

.. code-block:: console

    pip install -e imitation -e stable-baselines3
    pip install tqdm huggingface_sb3

First Simulation
----------------

To run your first crop simulation using WOFOST-Gym, navigate to the base directory ``wofost-gym/`` and run the following:

.. code-block:: console

    python3 test_wofost.py --save-folder <Location to Folder>

This initial run may take a few seconds. The weather cache is being generated in ``/pcse/.pcse``. Output should be generated in 
the form of Matplotlib Plots. 

After this test you are ready to use WOFOSTGym to train and evaluate agents in agriculture based scenarios! 