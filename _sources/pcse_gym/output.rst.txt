.. _env_config:

Environment Configuration
=========================

Here, we detail the most important parts of the environment configuration. 

Environment Output
------------------

The environment output is controlled by the ``--npk.output-vars`` and ``--npk.weather-vars`` parameters
in the ``pcse_gym.args.NPK_Args`` dataclass. This dataclass is present in the ``utils.Args`` class which is passed
to all scripts that generate data or train agents. 

The ``output`` variables correspond to the site and crop variables output by the simulation at each time step. Meanwhile, the 
``weather`` variables correspond to the daily weather from the specified location and year for the given day. A full list of 
output and weather variables can be found at :ref:`pcse_output`. 

The default output and weather variables are specified as:

.. code-block:: Python

    """Output Variables"""
    output_vars: list = field(default_factory = lambda: ['FIN', 'DVS', 'WSO', 'NAVAIL', 'PAVAIL', 'KAVAIL', 'SM', 'TOTN', 'TOTP', 'TOTK', 'TOTIRRIG'])
    """Weather Variables"""
    weather_vars: list = field(default_factory = lambda: ['IRRAD', 'TEMP', 'RAIN'])

This configuration will make the state space of the gym environment a tuple of length ``(15,)`` (a default parameter ``DAYS``, for the days elapsed, is automatically added).
The output is in order of specification, although wrapping the environment with a ``NPKDictObservationWrapper`` will make the variables keyword accessible (This is not compatible with training RL Agents).

Passing lists through command lines is difficult. Thus, we recommend changing the parameters in the ``NPK_Args`` dataclass, or passing a configuration 
file. See :ref:`command_line`.

Other Variables
---------------

Other variables configure the PCSE crop simulation, which can be passed via command line as ``--npk.<variable>`` or through a ``config.yaml`` file.

These are:

``intvn_interval``: How often an action can be taken (1 = 1 day, 7 = 1 week, etc.). For longer intervals, the outputted weather will expand to store the last `intvn_interval` days of weather.

``forecast_length``: It may be useful to train an agent given a weather forecast. ``forecast_length=1`` gives the weather today, ``forecast_length=7`` gives the current weather and the next 6 days.
``forecast_noise``: Forecasts are rarely accurate and decrease in accuracy over longer periods. ``forecast_noise`` introduces Gaussian noise to the true weather data as the forecast length increases.

``num_fert`` and ``num_irrig``: Specify the number of fertilization and irrigation actions availble. 

``random_reset``: Resets the environment to draw weather from a random year. Useful for training RL Agents (essentially domain randomization).

