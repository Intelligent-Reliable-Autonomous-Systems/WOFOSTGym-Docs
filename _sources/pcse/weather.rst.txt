.. _weather:

Weather
=======

The weather in PCSE is generally historical data provided by the `NASA Power <https://github.com/kdmayer/nasa-power-api>`_ API. 
This weather module has data from 1984 to 2023 with most latitudes and longitudes available. For configuring
the weather to be used in a crop simulation, see :ref:`site_calendar`.

As the simulation processes, the ``pcse.NASAPowerWeatherProvider`` module will continue to draw 
daily weather until the simulation terminates. 

.. caution:: 

    Starting a simulation with the year ``2023`` and running the simulation for 2 years may
    result in an error, as the ``pcse.NASAPowerWeatherProvider``can no longer continue to draw weather. 

In multi-year perennial simulations, the weather for a given year is generally drawn randomly from years in the range
``[1984, 2023]``. This behavior simulates a different weather for each year, and ensures that the simulation never runs outlined
of daily weather to draw from. If this is not desired behavior, it can be overridden in ``pcse_gym.envs.wofost_base.NPK_Env``. 
