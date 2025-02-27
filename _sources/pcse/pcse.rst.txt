.. _pcse:

Python Crop Simulation Environment
==================================

The Python Crop Simulation Environment (PCSE) is what underlies the **WOFOST-Gym** simulation. For the full documentation, 
see `PCSE Docs <https://pcse.readthedocs.io/en/stable/>`_. **WOFOST-Gym** modifies the PCSE environment substantially to support 
the desired uses for training RL Agents and easy environment configuration. However, the original documentation may be useful to 
clarify some points. 

Each ``pcse_gym.env`` Gymnasium environment calls a ``PCSE.Engine`` file, passing the specified parameters (:ref:`agro_config`). 
The ``env.step()`` function then runs the simulation subject to the daily weather and actions specified. For more information on the 
``PCSE.Engine``, see `PCSE Engine <https://pcse.readthedocs.io/en/stable/reference_guide.html#the-engine>`_. Different engines in 
``wofost-gym.pcse.engine`` support annual and perennial crop growth. 

Follow along in the pages below which highlight the important changes that have been made. 



.. toctree::
   :maxdepth: 2
   :glob:
   
   sites_and_crops
   weather
   dynamics
   output_vars