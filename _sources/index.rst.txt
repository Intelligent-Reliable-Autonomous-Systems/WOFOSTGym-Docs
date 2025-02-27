.. wofost-docs documentation master file, created by
   sphinx-quickstart on Sun Dec 22 14:41:09 2024.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

WOFOST-Gym Documentation
=========================

WOFOST-Gym is a benchmark built using OpenAI's `Gymnasium <https://gymnasium.farama.org/index.html>`_ 
and `PCSE <https://pcse.readthedocs.io/en/stable/>`_ for crop yield agriculture simulations that spans annual and perennial crops. 
It natively supports training a variety of Reinforcement Learning Agents and generating data
for other RL problems including Offline RL, Off Policy Evaluation, and Transfer Learning. 

To visit the associated paper website, click `here <https://wsolow.github.io/wofost-website>`_.

To view the associated paper, click this `link <https://arxiv.org/abs/2502.19308>`_.

To cite this work, please cite the research paper as follows:

.. code-block:: text

   @article{solow_wofostgym_2025,
      title={WOFOSTGym: A Crop Simulator for Learning Annual and Perennial Crop Management Strategies}, 
      author={William Solow and Sandhya Saisubramanian and Alan Fern},
      year={2025},
      eprint={2502.19308},
      archivePrefix={arXiv},
      primaryClass={cs.AI},
      url={https://arxiv.org/abs/2502.19308}, 
   }


.. toctree::
   :maxdepth: 2
   :glob:
   :caption: Contents:

   installation
   config/config_agro
   gen_data
   rl_agents/train_agent
   viz_data
   pcse_gym/pcse_gym
   pcse/pcse
   
   

