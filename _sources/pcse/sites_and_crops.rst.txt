.. _sites_and_crops:

Sites and Crops
===============

Every PCSE simulation contains a ``site`` and a ``crop``. The site is always active, while the 
crop can sometimes be inactivate. Importantly, a crop cannot exist without an active site. 

The ``site`` stores all the information about the soil including nutrient and water levels. 
These levels flucuate over time subject to user input (fertilization and irrigation), weather (rainfall, runoff),
and background processes. 

Meanwhile, the ``crop`` stores all information about the current state of the crop. The crop evolves
subject to the weather (Temperature and Day Length) and the available minerals in the soil. 

For a full list of output variables, see :ref:`pcse_output`. For a full list of parameters, see
:ref:`crop_config` and :ref:`site_config`. 