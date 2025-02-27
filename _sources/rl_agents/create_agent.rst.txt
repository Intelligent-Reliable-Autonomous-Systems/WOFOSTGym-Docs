.. _create_agent:

Creating a New RL Agent
=======================

The **WOFOST-Gym** Benchmark provides support for a variety of RL Agents. However, one purpose of the 
benchmark is to further research in training RL agents on the challenges that arise in sequential decision
making problems in agriculture. Thus, it is crucial that new RL Agents are able to be easily integrated to 
work with **WOFOST-Gym**. 

Currently, **WOFOST-Gym** natively supports PPO, SAC, and DQN, along with Offline RL Algorithm BCQ. 

Creating the Agent.py File
--------------------------

In the following examples, we will assume that the user is creating a file for the TD3 algorithm. 

1. Create a file named ``TD3.py`` in ``rl_algs/``. 
2. Create a ``Args`` class decorated with ``@dataclass`` which inherits ``rl_utils.RL_Args``. Place all TD3-specific hyperparameters (learning rate, batch size, etc. ) here. 
3. Create the ``nn.Module`` class for the Actor, call it ``TD3``. This is **very** important as it ensures that the ``train_agent.py`` file can find the TD3 algorithm when using ``--agent-type TD3``. 
4. Add the line ``from rl_algs.TD3 import Args as TD3_Args`` to the ``train_agent.py`` class.
5. Add the line ``TD3: TD3_Args = field(default_factory=TD3_Args)`` to the class ``AgentArgs`` in ``train_agent.py``. 
6. Put all training loop code in the ``rl_algs/TD3.py`` file under the function with the header ``train(kwargs)``, where ``kwargs`` is of type ``AgentArgs`` as specified in ``train_agent.py``. 

After these steps, we should be able to train a TD3 agent:

.. code-block:: console

    python3 train_agent.py --agent-type TD3 --save-folder logs/TD3/

For more information on training a RL agent, see :ref:`train_agent`.

.. important::
    The name of the ``Agent.py`` file must match the name of the ``nn.Module`` class that serves as the policy. Otherwise, the API will not be able to find
    the Policy class in ``Agent.py``. See :ref:`actor` for an example. 

The Args Class
--------------

The ``rl_utils.RL_Args`` dataclass contains experiment specific information for all RL Agents including Weights and Biases tracking
and default experiment naming. An example Args class is shown below:

.. code-block:: python3

    import rl_utils

    @dataclass
    class Args(rl_utils.RL_Args):
        # Algorithm specific arguments
        total_timesteps: int = 1000000
        """total timesteps of the experiments"""
        learning_rate: float = 2.5e-4
        """the learning rate of the optimizer"""
        num_envs: int = 1
        """the number of parallel game environments"""
        buffer_size: int = 10000
        """the replay memory buffer size"""
        gamma: float = 0.99
        """the discount factor gamma"""
        tau: float = 1.0
        """the target network update rate"""
        target_network_frequency: int = 650
        """the timesteps it takes to update the target network"""
        batch_size: int = 128
        """the batch size of sample from the reply memory"""
        start_e: float = 1
        """the starting epsilon for exploration"""
        end_e: float = 0.05
        """the ending epsilon for exploration"""
        exploration_fraction: float = 0.5
        """the fraction of `total-timesteps` it takes from start-e to go end-e"""
        learning_starts: int = 10000
        """timestep to start learning"""
        train_frequency: int = 10
        """the frequency of training"""
        checkpoint_frequency: int = 50
        """How often to save the agent during training"""

.. _actor:

The Actor Class
---------------

Every ``agent.py`` file has an actor class, which is the ``nn.Module`` (neural network) class that learns the policy to perform. This network
**must** be named the same as the ``agent.py`` file, text-case included. If a file is named ``TD3.py``, the policy network class must also be declared as ``class TD3(nn.Module)``.
Policy classes should also inherit the abstract class ``rl_algs.Agent``.

Some RL Algorithms, like Soft Actor-Critic, use more than one network. In this case, the other network can be declared under any name, as it is not required to load the policy (for data generation etc.)

See an example of the actor class in ``rl_algs/PPO.py`` below:

.. code-block:: python

    import torch.nn as nn
    from rl_algs import Agent

    class PPO(nn.Module, Agent):
        def __init__(self, envs):
            super().__init__()
            self.env = envs
            self.critic = nn.Sequential(
                layer_init(nn.Linear(np.array(envs.single_observation_space.shape).prod(), 64)),
                nn.Tanh(),
                layer_init(nn.Linear(64, 64)),
                nn.Tanh(),
                layer_init(nn.Linear(64, 1), std=1.0),
            )
            self.actor = nn.Sequential(
                layer_init(nn.Linear(np.array(envs.single_observation_space.shape).prod(), 64)),
                nn.Tanh(),
                layer_init(nn.Linear(64, 64)),
                nn.Tanh(),
                layer_init(nn.Linear(64, envs.single_action_space.n), std=0.01),
            )

In addition to this header declaration, every Policy class must have a ``get_action(obs)`` function. This is a helper function for compatibility with data generation
for policies. 

See an example below using the ``PPO`` agent:

.. code-block:: python

    def get_action(self, x):
        """
        Helper function to get action for compatibility with generating data
        """
        logits = self.actor(x)
        probs = Categorical(logits=logits)
        return probs.sample()


The Train Function
------------------

Every ``agent.py`` file needs a ``train(kwargs)`` function to be called when training the agent. The train function 
takes in the ``AgentArgs`` dataclass in ``train_agent.py`` as an argument. 

The most important line in the ``train(kwargs)`` function is: ``args = kwargs.PPO`` if we are in ``PPO.py``. This line gets the 
PPO-specific hyperparameter arguments. 

After this line, setup and training can continue as normal. We recommend starting with the following block of code:

.. code-block:: python

    def train(kwargs):
        args = kwargs.PPO
        run_name = f"PPO/{kwargs.env_id}__{args.exp_name}__{args.seed}__{int(time.time())}"
        args.batch_size = int(args.num_envs * args.num_steps)
        args.minibatch_size = int(args.batch_size // args.num_minibatches)
        args.num_iterations = args.total_timesteps // args.batch_size
        
        if kwargs.track:
            import wandb

            wandb.init(
                project=args.wandb_project_name,
                entity=args.wandb_entity,
                sync_tensorboard=True,
                config=vars(args),
                name=run_name,
                monitor_gym=True,
                save_code=True,
            )
        assert isinstance(kwargs.save_folder, str), f"Save Folder: `{kwargs.save_folder}` must be of type `str`"
        writer = SummaryWriter(f"{kwargs.save_folder}/{run_name}")
        writer.add_text(
            "hyperparameters",
            "|param|value|\n|-|-|\n%s" % ("\n".join([f"|{key}|{value}|" for key, value in vars(args).items()])),
        )

        # TRY NOT TO MODIFY: seeding
        random.seed(args.seed)
        np.random.seed(args.seed)
        torch.manual_seed(args.seed)
        torch.backends.cudnn.deterministic = args.torch_deterministic

        device = torch.device("cuda" if torch.cuda.is_available() and args.cuda else "cpu")

        # env setup
        envs = gym.vector.SyncVectorEnv(
            [make_env(kwargs, i, args.capture_video, run_name) for i in range(args.num_envs)],
        )
        assert isinstance(envs.single_action_space, gym.spaces.Discrete), "only discrete action space is supported"

After this code block, training setup can continue as normal. We refer to `CleanRL <https://github.com/vwxyzjn/cleanrl>`_ for 
more training examples. 


