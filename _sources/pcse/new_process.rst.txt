.. _new_process:

Adding a New Crop Process
=========================

While the WOFOST crop growth model modelled by PCSE contains many crop and soil processes, 
it may sometimes be the case that the user wants to modify the underlying crop growth model 
in PCSE to add a new process (e.g. nutrient leeching from the subsoil). Adding a new crop process
enables new responses to be studied in WOFOSTGym. This guide outlines how to add a new process to 
the PCSE model so that its variables can be modified through the WOFOSTGym API. 

All crop submodules are contained within the ``pcse/pcse/crop`` and ``pcse/pcse/soil`` folders. First,
choose which submodule the new process should be computed within. Then, add the required states, rates, and parameters
to the subclasses defined within the parent module as shown below: 

.. code-block:: python

    class NPK_Soil_Dynamics(SimulationObject):

        class Parameters(ParamTemplate):
            NEW_PARAM = Float(-99.0)
        class StateVariables(StatesTemplate):
            NEW_STATE = Float(-99.0)
        class RateVariables(RatesTemplate): 
            NEW_RATE = Float(-99.0)

Then, add the new states and rates to the ``StateVariables`` and ``RateVariables`` constructor in the 
initialize and reset functions as so: 

.. code-block:: python

    self.states = self.StateVariables(
                kiosk,
                publish=["NEW_STATE"],
                NEW_STATE=NEW_STATE_INIT_VALUE
            )

            self.rates = self.RateVariables(
                kiosk,
                publish=["NEW_RATE"]
            )

This will ensure that your new states and rates can be accessed by other submodules in PCSE and also by the 
WOFOSTGym API. Then, define the new crop subprocess in the ``calc_rates()`` and ``integrate()`` functions.
Rates can be accessed by ``r.NEW_RATE`` and states can be accessed by ``s.NEW_STATE``. Parameters can be accessed
by ``p.NEW_PARAM``. 

Now the new crop or soil process is completely defined! The next step is ensuring that WOFOSTGym can access it. In the 
``pcse_gym/pcse_gym/utils.py`` file, add the new states and rates to the ``OUTPUT_VARS`` list exactly as it was defined 
in the StateVariables or RateVariables class. 

For a new parameter, it is recommended to add this line of code to the ``set_params()`` function in the same utils.py file:

.. code-block:: python

    if args.NEW_PARAM is not None:
        env.parameterprovider.set_override("NEW_PARAM", args.NEW_PARAM, check=False)

and the below line of code in the ``pcse_gym/args.py`` in the ``WOFOST_Args`` class:

.. code-block:: python

    class WOFOST_Args:
        """ NEW PARAM FOR XX"""
        NEW_PARAM: Optional[float] = None


These additions will enable the parameter to be set via command line. 

.. caution::

    If a new parameter has been added, it must also be added to the crop and soil yaml files. Otherwise, a missing 
    parameter error will be thrown by the PCSE module.  

Now that the WOFOSTGym API is aware of the new state and rate variables, they can be included as part of the observation
space by modifying the ``npk.output_vars`` parameter via command line. 

Adding a new Crop or Soil Submodules
====================================
To add a new Crop or Soil submodule, create a new file in the ``crop/`` or ``soil/`` folders.
Every submodule needs to inherit from the SimulationObject class. A template is included below: 

.. code-block:: python

    from datetime import date

    from pcse.util import AfgenTrait
    from pcse.utils.traitlets import Float
    from pcse.utils.decorators import prepare_rates, prepare_states
    from pcse.base import ParamTemplate, StatesTemplate, RatesTemplate, SimulationObject
    from pcse.utils import signals
    from pcse.base import VariableKiosk
    from pcse.nasapower import WeatherDataContainer

    class NPK_Soil_Dynamics(SimulationObject):

        class Parameters(ParamTemplate):

        class StateVariables(StatesTemplate):

        class RateVariables(RatesTemplate):

        def initialize(self, day: date, kiosk: VariableKiosk, parvalues: dict) -> None:

            self.params = self.Parameters(parvalues)
            self.kiosk = kiosk
            self.states = self.StateVariables(
                kiosk,
                publish=[],
            )

            self.rates = self.RateVariables(
                kiosk,
                publish=[]
            )

            self._connect_signal(self._on_APPLY_NPK, signals.apply_npk)

        @prepare_rates
        def calc_rates(self, day: date, drv: WeatherDataContainer) -> None:
            """Compute Rates for model"""
            pass

        @prepare_states
        def integrate(self, day: date, delt: float = 1.0) -> None:
            """Integrate states with rates"""
            pass

All Parameters, States, and Rates should be defined in their respective subclasses as so: 

.. code-block:: python

    class NPK_Soil_Dynamics(SimulationObject):

        class Parameters(ParamTemplate):
            PARAM = Float(-99.0)
        class StateVariables(StatesTemplate):
            STATE = Float(-99.0)
        class RateVariables(RatesTemplate): 
            RATE = Float(-99.0)

Any state and rate that should be visible to the kiosk or to the WOFOSTGym wrapper should be published.
See the above section on adding states and rates. 

After defining all the module processes, its ``integrate()`` and ``calc_rates()`` methods need to be called.
For soil submodules, these are in the ``soil_wrappers.py`` file. Add an instance of the new object to the constructor,
and add its method calls to the high level ``integrate()`` and ``calc_rates()`` calls. Ensure that the corresponding 
``reset()`` function is called in the reset wrapper as well. 

For crop submodules, these are defined in the ``wofost8.py`` file. Follow the same process for adding the functions listed above. 