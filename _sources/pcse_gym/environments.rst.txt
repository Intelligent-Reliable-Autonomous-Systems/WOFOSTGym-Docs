.. _environments:

Available Environments
======================

The **WOFOST-Gym** benchmark provides 50+ Gymnasium environments in ``pcse_gym.envs``. Every environment inherits from the 
``pcse_gym.envs.wofost_base.NPK_Env``, ``pcse_gym.envs.wofost_base.Harvest_Env``, ``pcse_gym.envs.wofost_base.Plant_Env``. To see a list of environments and how 
to specify one, see :ref:`env_list`.

Furthermore, environments are subclassed based on whether or not they correspond to growing ``annual``, ``perennial``, or ``grape`` crops, given the different
ways that these crops are handled in the :ref:`pcse` simulator. 

Every subclass of environments has six environments which correspond to the following:

* LNPKW: Crop growth subject to limited water, Nitrogen, Potassium, and Phosphorous.
* LNPK: Crop growth subject to limited Nitrogen, Potassium, and Phosphorous.
* LN: Crop growth subject to limited Nitrogen.
* LW: Crop growth subject to limited water.
* LNW: Crop growth subject to limited Nitrogen and water
* PP: Crop growth under Potential Production, not limited by any nutrients (this can be helpful as a baseline). 

These 6 environments dictate the conditions under which a crop or site can envolve under. For more information, see :ref:`dynamics`.

Default Environments
--------------------

There are three types of environments that a user will generally consider: ``annual``, ``perennial``, or ``grape`` environments. 

* ``Annual`` environments typically simulate the growth of a single crop. Once this crop is harvested or dies, the crop simulation will end. 

* ``Perennial`` environments simulate the growth of a perennial crop which includes the growing phase and dormancy phase across a number of seasons. These environments can simulate the growth of a generic perennial crop.

* ``Grape`` environments are very similar to a perennial crop, except that they contain grape specific phenology in the PCSE simulation. These environments can simulate the growth of a woody plant perennail crop (grapes, cherries, etc.) with high fidelity subject to the accuracy of the crop parameters.


Planting and Harvesting Environments
------------------------------------

The ``agromanagment.yaml`` file (:ref:`agro_config`) sets the date of planting for crops. However, in a ``Plant_Env``, the planting action
is part of the action space. These environments are generally `not` compatible with training RL agents, given that the crop observations will not 
be available until the crop is planted. However, they may be of some interest when evaluating the optimal time to plant an annual crop, especially given a 
weather forecast. 

``Harvest_Env`` allows the date of harvest to be manually set. This can be useful if deciding when to harvest a crop is important, subject to
crop growth, crop death, or a weather forecast. 

Both ``Plant_Env`` and ``Harvest_Env``, and their annual and perennial variants, contain the 6 environments corresponding to 
variations on available nutrients. We generally do not use these environments in our experiments, but they may be useful to a user in the future. 

.. _env_list:

Environments List
-----------------

The list of available environments and how to specify them:

.. code-block:: console

    # Default Annual Environments
    --env-id lnpkw-v0
    --env-id pp-v0
    --env-id lnpk-v0
    --env-id ln-v0
    --env-id lnw-v0
    --env-id lw-v0
    # Single year planting Environments
    --env-id plant-npk-v0
    --env-id plant-pp-v0
    --env-id plant-lnpk-v0
    --env-id plant-ln-v0
    --env-id plant-lnw-v0
    --env-id plant-lw-v0
    # Single year harvesting environments
    --env-id harvest-npk-v0
    --env-id harvest-pp-v0
    --env-id harvest-lnpk-v0
    --env-id harvest-ln-v0
    --env-id harvest-lnw-v0
    --env-id harvest-lw-v0
    # Default perennial environments
    --env-id perennial-lnpkw-v0
    --env-id perennial-pp-v0
    --env-id perennial-lnpk-v0
    --env-id perennial-ln-v0
    --env-id perennial-lnw-v0
    --env-id perennial-lw-v0
    # Perennial planting Environments
    --env-id perennial-plant-npk-v0
    --env-id perennial-plant-pp-v0
    --env-id perennial-plant-lnpk-v0
    --env-id perennial-plant-ln-v0
    --env-id perennial-plant-lnw-v0
    --env-id perennial-plant-lw-v0
    # Perennial harvest environemnts
    --env-id perennial-harvest-npk-v0
    --env-id perennial-harvest-pp-v0
    --env-id perennial-harvest-lnpk-v0
    --env-id perennial-harvest-ln-v0
    --env-id perennial-harvest-lnw-v0
    --env-id perennial-harvest-lw-v0
    # Default Grape environments
    --env-id grape-lnpkw-v0
    --env-id grape-pp-v0
    --env-id grape-lnpk-v0
    --env-id grape-ln-v0
    --env-id grape-lnw-v0
    --env-id grape-lw-v0
        
    