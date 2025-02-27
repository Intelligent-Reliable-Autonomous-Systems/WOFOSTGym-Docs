.. _rewards:

Reward Wrappers
===============

While the ``pcse_gym/`` Gymnasium module natively supports a few Reward Wrappers, it is may be desirable
to create a different reward to train a RL agent on, given different values when growing a crop (reduced water use, reduced fertilizer runoff, etc.)

Base Class
----------
The abstract ``pcse_gym.wrappers.RewardWrapper`` base class inherits the ``gymnasium.Wrapper`` class. 
It has two functions ``_validate()`` which enforces correct environment wrapping order, and the 
``reset()`` function which correctly passes the keyword arguments to the base environment

.. caution::

    Always call the ``utils.make_gym_env()`` function to make an environment before 
    wrapping with a ``RewardWrapper``. Otherwise, the environment will be wrapped with a 
    ``gymnasium.Wrapper.OrderEnforcing`` wrapper which does not pass keyword arguments in ``reset()``. 
    See :ref:`env_creation`.

Creating a Reward Wrapper
-------------------------

To create a reward wrapper, inherit the ``pcse_gym.wrappers.RewardWrapper`` class. The ``__init__()`` 
function should have the following header:

.. code-block:: python

    class RewardFertilizationThresholdWrapper(RewardWrapper):
    """ 
    Modifies the reward to be a function with high penalties for if a 
    threshold is crossed during fertilization or irrigation
    """
    def __init__(self, env: gym.Env, args):
        """Initialize the :class:`RewardFertilizationThresholdWrapper` wrapper with an environment."""
        super().__init__(env)
        self.env = env

Any required arguments should be passed by the ``Args`` dataclass (specified in ``utils.py``). 


Changing the Reward
^^^^^^^^^^^^^^^^^^^

The reward function generally depends on the output of the simulation and the action taken. Upon taking an action, an ``action_tuple`` is returned
from the ``_take_action()`` function which specifies how much water, Nitrogen, Phosphorous, or Potassium was applied to the crop. 

From this action tuple and the output of the crop simulation, the reward can be updated. 

See the example below which penalizes the application of any fertilizer or water, while rewarding the total growth of the crop, ``WSO``. 

.. code-block:: python

        def _get_reward(self, output: dict, act_tuple:tuple):
        """Gets the reward as a penalty based on the amount of NPK/Water applied
        
        Args:
            output: dict     - output from model
            act_tuple: tuple -  NPK/Water amounts"""

            reward = output.iloc[-1]['WSO'] - \
                            (np.sum(self.cost * np.array([act_tuple[2:]])))
        return reward

.. important::

    When using a ``pcse_gym.RewardWrapper``, the output of the crop simulation must contain the output variables needed to compute the 
    new reward. For information on configuring simulation output, see :ref:`env_config`.

The Step Function
^^^^^^^^^^^^^^^^^

Often, the step function needs to be overridden with ``env.unwrapped`` calls to ensure compatibility with a variety of wrappers, 
especially if the environment was previously wrapped with ``NPKDictObservationWrapper`` or ``NPKDictActionWrapper``. 

To ensure compatibility, copy the ``step`` function from ``pcse_gym.wofost_base.NPK_Env`` to 
the ``pcse_gym.wrappers.NewRewardWrapper`` class. Below is the template, which does not need modifications:

.. code-block:: python

    def step(self, action):
        """Run one timestep of the environment's dynamics.

        Sends action to the WOFOST model and recieves the resulting observation
        which is then processed to the _get_reward() function and _process_output()
        function for a reward and observation

        Args:
            action: integer
        """
        # Send action signal to model and run model
        act_tuple = self.env.unwrapped._take_action(action)
        output = self.env.unwrapped._run_simulation()

        observation = self.env.unwrapped._process_output(output)
        
        reward = self._get_reward(output, act_tuple) 
        
        # Terminate based on crop finishing
        termination = output.iloc[-1]['FIN'] == 1.0
        # Truncate based on site end date
        truncation = self.env.unwrapped.date >= self.env.unwrapped.site_end_date

        self.env.unwrapped._log(output.iloc[-1]['WSO'], act_tuple, reward)
        return observation, reward, termination, truncation, self.env.unwrapped.log
    

