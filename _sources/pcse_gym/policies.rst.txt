.. _policies:

Prespecified Policies
=====================

Farmers and growers often operate using open-loop policies, or relatively simple closed loop policies subject to the observations avaiable to them. 

``pcse_gym.policies`` provides a variety of configurable prespecified policies that might be used on a farm. 

Using a Prespecified Policy
---------------------------

Using a prespecified policy in **WOFOST-Gym** usually arises when generating data for comparison with another policy, or on another farm. See 
:ref:`data_policies` for an example on how to call the policy to generate data. 

Every prespecified policy assumes that the ``Envirnoment`` is wrapped using a ``NPKDictActionWrapper`` and ``NPKDictObservationWrapper``. 
Using these wrappers makes it much easier to specify a policy, as it makes the actions and observations key-word accessible. A prespecified policy will
throw an error if the environment is not correctly wrapped. 

Every policy has a ``__call()`` function which takes in the crop observation. For example:

.. code-block:: python3

    policy = policies.Below_N(args)

    # Make Environment
    env = utils.make_gym_env(args)
    env = env.NPKDictActionWrapper(env)
    env = env.NPKDictObservationWrapper(env)
    obs, _ = env.reset()

    action = policy(obs)

will return the action to be taken given the current observation. 

.. important::
    
    Every policy requires that specific observation variables be availble in the ``obs`` dictionary. If an environment 
    does not have these variables, an error will be thrown. For information on configuring environment output, see :ref:`env_config`.


Creating a Prespecified Policy
------------------------------

It is likely that a prespecified policy needs to be created to fit the specific use case. 

Every prespecified policy should have the following header: 

.. code-block:: python 

    class Threshold_N(Policy):
    """Policy applying a small amount of Nitrogen at a given interval
    """
    required_vars = ["TOTN"]

    def __init__(self, env: gym.Env, args):
        """Initialize the :class:`Interval_N`.

        Args: 
            env: The Gymnasium Environment
            required_vars: list of required state space variables 
        """
        super().__init__(env, required_vars=self.required_vars)

The above policy requires the ``"TOTN"`` variable as output from the environment. 

``args`` is usually an instance of ``gen_data.DataArgs`` which contains variables for:

* ``threshold`` which controls the total amount of fertilizer than can be applied, or the limit below which fertilizer will be applied.
* ``amount`` which controls the amount of fertilizer or water applied.
* ``interval`` which controls how often fertilizer or water is applied.

After the header, the ``_get_action(self, obs)`` function should be created. For example, suppose we want to apply nitrogen fertilizer as long as a total threshold has not been reached.
The function to do this would look like:

.. code-block:: python

    def _get_action(self, obs):
        """Returns an action that applies Nitrogen until a threshold is met
        """
        if obs['TOTN'] > self.threshold:
            return {'n': 0, 'p': 0, 'k': 0, 'irrig':0 }
        else:
            return {'n': self.amount, 'p': 0, 'k': 0, 'irrig':0 }

The ``TOTN`` observation records the total amount of Nitrogen applied. If it is above some threshold, no Nitrogen will be applied,
as shown by the ``{'n': 0, 'p': 0, 'k': 0, 'irrig':0 }`` block.

Every output of ``_get_action(obs)`` should be a dictionary with the keys ``'n'``, ``'p'``, ``'k'``, and ``'irrig'``
which give the amount of Nitrogen, Phosphorous, Potassium, and water to be applied. 

The amount of fertilizer or water that is actually applied is controlled by the ``--npk.fert-amount`` and ``npk.irrig-amount`` parameters.
For more information see :ref:`env_config`. 

        


