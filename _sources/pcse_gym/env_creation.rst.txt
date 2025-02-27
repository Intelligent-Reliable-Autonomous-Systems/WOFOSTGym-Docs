.. _env_creation:

Environment Creation
====================

PCSE Gym environments are specified using the ``utils.Args`` dataclass and calling the ``utils.make_gym_env(args)`` function. 

The ``utils.make_gym_env(args)`` function makes the Gym environment and saves and/or loads the correct ``config.yaml`` file that specifies
the crop simulation. 

For information on environment configuration, see :ref:`env_config`.

.. caution::

    We do **not** recommend calling 
    
    .. code-block:: python

        gymnasium.make(env-id, **kwargs) 
    
    directly, as ``utils.make_gym_env(args)`` also handles correctly passing arguments to the ``gymnasium.make`` function.

As an example, a default environment can be created with the following:

.. code-block:: python

    import utils, tyro

    args = tyro.cli(utils.Args)
    env = utils.make_gym_env(args)

.. _wrappers:

Environment Wrappers
--------------------

There are a few cases in which a ``Gymnasium.Wrapper`` should be used.

If using a prespecified policy :ref:`policies`, the environment should be wrapped in a 
``pcse_gym.wrappers.NPKDictActionWrapper`` and ``pcse_gym.wrappers.NPKDictObservationWrapper``. These two wrappers wrap
the environment actions and observations as dictionaries to interface with the ``Policy`` class in ``pcse_gym.policies`` which makes
policy specification easy. 

To wrap the environment in these wrappers, do the following: 

.. code-block:: python

    import utils, tyro

    args = tyro.cli(utils.Args)
    env = utils.make_gym_env(args)
    env = pcse_gym.wrappers.NPKDictActionWrapper(env)
    env = pcse_gym.wrappers.NPKDictObservationWrapper(env)

.. caution::

    When using a trained RL Agent policy, ensure that the environment is **not** wrapped in ``pcse_gym.wrappers.NPKDictActionWrapper`` or ``pcse_gym.wrappers.NPKDictObservationWrapper``,
    as this will make the agent unable to execute a policy. 

Additionally, the user may want to specify different reward functions (e.g. to penalize fertilizer runoff or set irrigation limits) and then train a RL agent with this reward function. 
To do so, the environment should be instantiated as follows: 

.. code-block:: python

    import utils, tyro

    args = tyro.cli(Args)

    # Make the gym environment with wrappers
    env = utils.make_gym_env(args)
    env = pcse_gym.wrappers.NPKDictActionWrapper(env)
    env = pcse_gym.wrappers.NPKDictObservationWrapper(env)
    env = utils.wrap_env_reward(env, args)

And, the reward wrapper should be specified in the command line argument or config.yaml file:

.. code-block:: console

    python3 test_wofost.py --env-reward RewardFertilizationThresholdWrapper

For examples on how to create a ``RewardWrapper`` see :ref:`rewards`

.. important::

    Always call ``env = utils.wrap_env_reward(env, args)`` after any calls to other ``pcse_gym.wrappers`` as ``RewardWrappers`` overwrite the 
    step function, creating the desired reward. 
