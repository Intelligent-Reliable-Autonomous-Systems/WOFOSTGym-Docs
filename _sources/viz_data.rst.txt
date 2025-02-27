Visualizing Output
==================

Visualizing data in ``WOFOST-Gym`` take a few different forms. Sometimes, it may be useful to visualize the various observations
as they evolve throughout the course of a simulation. Other times, it can be useful to compare performances of multiple runs of policies on various
farms. 

To support these objectives, ``vis_data.py`` contains a multitude of visualization functions that can be called from other modules, or from ``vis_data.py``. 

To run from ``vis_data``, type this:

.. code-block:: console

    python3 vis_data.py --data-file <data_file> --plt <plotting function name>

The ``data_file`` is the relative path to the ``.npz`` data file to load. ``--plt`` is the name of the plotting function
in ``vis_data.py``. 

.. caution::

    Different plotting functions are only compatible with certain data file configurations. Check that the ``observations`` loaded in the data file
    suit the use case of the plotting function specified by ``--plt``. 

Plotting functions
------------------

* ``plot_output`` plots the individual observation variables as they evolve throughout the simulation on indidual figures. 

* ``plot_policy`` plots the actions (fertilizer and irrigation applied) and an observation variable (typically Weight of Storage Organs) as taken throughout a simulation.

* ``plot_policy_multiple`` has the same functionality as ``plot_policy`` but can compare multiple policies on a single farm at the same time. 

* ``plot_matrix`` requires data to be in the shape ``(num_farms, num_policies, ...)`` and will then plot the average rewards obtained by each policy on each farm. See :ref:`gen_data_multiple` for information on data generation for this plot. 