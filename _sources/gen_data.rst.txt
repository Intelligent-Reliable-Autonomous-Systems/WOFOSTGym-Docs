.. _gen_data:

Generating Data
===============

For generating data from multiple farms and policies simultaneously, see :ref:`gen_multiple_data`.

Generally, there are two types of data a user may want to generated: data from a prespecified policy and data from a trained RL Agent. 
See :ref:`data_policies` and :ref:`data_rl` for more information. 

Data is generated over a range of ``years`` and range of ``latitudes`` and longitudes. These ranges can be specified by command line arguments:

.. code-block:: console

    python3 gen_data.py 
    --lat-low 50
    --lat-high 51
    --lon-low 120
    --lon-high 123
    --year-low 1984
    --year-high 2001

Additionally, two file types are supports ``.npz`` and ``.csv`` files. ``.npz`` files save the output in a 
``(obs, action, reward, next_obs, done, info)`` format which is compatible for Offline RL training and loading into Replay Buffers. Meanwhile, 
``.csv`` files save data in a daily observation format which can be useful for a variety of problems. 

To specify a file type, include the argument:

.. code-block:: console

    python3 gen_data.py 
    --file-type csv
    OR
    --file-type npz

.. note:: 

    As with any script in **WOFOST-Gym**, the ``--load-data-fpath`` argument is allowed, meaning that data can be generated from a previously created configuration file.


.. important::

    * Latitudes are valid in the range (-90, 90)
    * Longitudes are valid in the range (-180, 180)
    * Years are valid in the range (1984, 2023)
    * Outside of these ranges, a missing data error will be thrown from ``pcse.NASAPower``

.. _data_policies:

Prespecified Policies
---------------------

Many real-world agriculture policies are relatively simple and often open loop policies. That is, a valid policy may be `Apply 10 kg/ha of Nitrogen fertilizer every 3 weeks`.

One of the goals of the **WOFOST-Gym** benchmark is to support Off Policy Evaluation, and in many cases the baseline we are competing against is an open-loop policy, or a relatively simple closed-looop policy
such as `Apply 15 kg/ha of Nitrogen Fertilizer if the Nitrogen level in the soil drops below X amount`. 

To generate data from a prespecified policy, run the following:

.. code-block:: console

    python3 gen_data.py --file-type npz --save-folder test --data-file test_data --policy-name Below_N

This code block will generate data in ``.npz`` format, saving it to folder ``test`` under the name ``test_data.npz``. The generated data will be from the 
``Below_N`` policy located in ``pcse_gym/policies.py``. 

Every Prespecified policy takes three specific arguments: ``--threshold``, ``--interval``, and ``--amount``
which control the total amount that can be applied (or the threshold below which to take the action), the interval of application, and the amount to apply
of water, Nitrogen, Phosphorous, or Potassium. For more information on Prespecified Policies, see :ref:`policies`.

.. _data_rl:

RL Agent Policies
-----------------

For some use cases, we may want to use data generated from a trained RL agent to see how it performs in a variety of scenarios. 

To generate data from a trained RL Agent, run the following:

.. code-block:: console

    python3 gen_data.py --file-type npz --save-folder test --data-file test_data --agent-type <Agent Type> --agent_path <Path to agent.pt File>

This code block will generate data in ``.npz`` format, saving it to folder ``test`` under the name ``test_data.npz``. The generated data will be from the 
specified ``agent.pt`` file. 

.. important::
    
    * We make it very easy to specify RL agents, but this requires the ``RL_Agent.py`` file to be in a specific format. See :ref:`create_agent` for more information. 
    * Confirm that the ``agent_type`` and ``agent.pt`` file specified are of the same agent. 


.. _gen_multiple_data:

Generating Data from Multiple Sources
-------------------------------------

There are many instances where one may want to generate data from multiple policies and farms. For instance, a farmer may want to know 
how her policy performs when fertilizing wheat at another farm. This is the problem of Off Policy Evaluation. 

To generate data from multiple policies and farms, navigate to the ``gen_data_multiple.py`` file. For ease of use, each farm is specified by a ``.yaml``
configuration file which is created when an environment is called. See :ref:`env_creation` for more information. Modify multiple ``.yaml`` configuration files
to achieve the desired site and crop configuration. Loading configurations is as follows:

.. code-block:: python

    config_fpaths = ["test/test1/config.yaml", "test/test2/config.yaml"]
    envs = utils.make_gym_envs(args, config_fpaths=config_fpaths)

    # Wrap environment with necessary wrappers
    envs = [utils.wrap_env_reward(env, args) for env in envs]


Additionally, each policy and the required arguments for the policy required are specified in a list and a dictionary. Policies can either be
Reinforcement Learning agents (e.g. PPO, SAC, DQN, etc) or pre-specified policies in ``pcse_gym.policies``. For agent policies, the only argument needed is
is the relative path to the agent. The pre-specified policies require parameters. See :ref:`policies` for more information.

An example configuration would look like:

.. code-block:: python

    # Pass the constructor for the policy, either a pcse_gym.policies or rl_utils.Agent class
    pols = [PPO, policies.Interval_N, policies.Threshold_N]
    
    # Specify the kwargs for the policies. If using a PyTorch policy from rl_algs, use
    # the kwargs: `{"state_fpath": <path-to-agent>}
    pols_kwargs = [{"state_fpath": "PPO_test/PPO/lnpkw-v0__rl_utils__1__1735688110/agent.pt"}, {"interval":7}, {"threshold":10, "amount":2}]

To run the script, type:

.. code-block:: console

     python3 gen_data_multiple.py --save-folder test/multiple/ --data-file multiple    

The output will be a ``.npz`` file with arrays of shape ``(num_farms, num_policies, ..)`` which can then be used for algorithm training or evaluation, or data visualization. 