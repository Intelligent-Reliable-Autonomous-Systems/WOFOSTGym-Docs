.. _train_agent:

Training RL Agents
==================

Support is provided for a variety of Online RL Algorithms including PPO, SAC and DQN. To add support for a new RL Agent, 
see :ref:`create_agent`

RL Algorithms (Agents) are specified in the ``rl_algs/`` directory. Each algorithm is self-contained in a ``<agent>.py`` file. 
Common functions for creating an environment, loading data to a buffer, etc are stored in the ``rl_algs/rl_utils.py`` file. 
RL Algorithm implementations follow the `CleanRL <https://github.com/vwxyzjn/cleanrl>`_ paradigm. 

.. important::

    When training a RL Agent, the ``site_start_date`` and ``site_end_date`` should correspond to the ``crop_start_date`` and ``crop_end_date``, otherwise there will be 
    crop observation variables missing which the RL Agent cannot handle.

Starting a Training Run
-----------------------

To train a RL Agent, run the following:

.. code-block:: console

    python3 train_agent.py --agent-type PPO --save-folder logs/ppo/

This trains a PPO Agent with the default hyperparameters and saves the output to ``logs/ppo/``. Support for 
`Weights and Biases <https://wandb.ai/site>`_ is included and can be toggled with: 

.. code-block:: console

    python3 train_agent.py --agent_type <agent_type> --save-folder logs/test/ --track

.. important::

    The ``--agent-type`` must match the name of the RL Agent ``file.py`` and name of the ``nn.Module`` class that selects actions.
    For more information, see :ref:`create_agent`

Algorithm Configuration
-----------------------

Every ``<agent>.py`` file in ``rl_algs/`` has an associated ``Args`` dataclass. See each ``.py`` file for the 
corresponding hyperparameters. 

To specify a hyperparameter, run the code:

.. code-block:: console

    python3 train_agent.py --agent-type PPO --save-folder logs/ppo/ --PPO.gamma 0.85

The above block of code trains a PPO agent with ``gamma`` value of 0.85. 

.. important::

    Ensure that the argumement specified, i.e. ``--PPO.gamma`` correspond to the agent type, in this case ``--agent-type PPO``. 
    Otherwise, the argument will not be passed to the correct algorithm. 

.. note::

    As with any top-level script in **WOFOST-Gym**, a configuration can be loaded via the ``--load-config-fpath <Path to file>`` argument.
    For training agents, this can be used to load a previously saved hyperparameter configuration, and/or can be used to load a previously saved 
    environment. See :ref:`command_line` for more information. 



.. toctree::
   :maxdepth: 2
   :glob:
   
   create_agent