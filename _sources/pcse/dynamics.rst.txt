.. _dynamics:

Simulation Dynamics
===================

Crop growth is simulated subject to limited nutrients and water available. To understand 
the soil modules more in depth, see `Water Modules <https://pcse.readthedocs.io/en/stable/code.html#water-balance-modules>`_ and 
`Nitrogen Modules <https://pcse.readthedocs.io/en/stable/code.html#nitrogen-and-carbon-modules>`_.

The **WOFOST-Gym** PCSE environment provides 6 default settings under which crops can grow. 

* Crop growth subject to limited water, Nitrogen, Potassium, and Phosphorous.
* Crop growth subject to limited Nitrogen, Potassium, and Phosphorous.
* Crop growth subject to limited Nitrogen.
* Crop growth subject to limited water.
* Crop growth subject to limited Nitrogen and water
* Crop growth under Potential Production, not limited by any nutrients (this can be helpful as a baseline). 

When the crop is not limited by a specific nutrient, that nutrient is considered abundant and its value is 
held at a high constant value within the simulation module. Therefore, the crop will always have a sufficient amount
of that nutrient to sustain growth. 