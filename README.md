# VPS_tutorial_draft
Draft version of KOII VPS Tutorial

# VPS node ultimate tutorial

## Before you start

Setting up and maintaining a Koii Node requires a basic level of technical knowledge, basic experience with Linux is advisable. 
Anyway we will guide you through the entire process step-by-step, in case you will encounter some obstacles please reach out to #node-vps channel in our Discord.

Make sure your hardware and internet speeds meet the minimum requirements for a Koii Node which are very minimal (similiar to Desktop version) but at least 2GB RAM and >1GHz processor and stable internet.

If you decide running a node on VPS isn't for you there are other options available, like Dekstop version. Go to our website where you will be able to download Desktop Node https://www.koii.network/node

### Operating System

Using Ubuntu Server 20.04.xx LTS is highly recommended for running Koii Nodes due to its stability and compatibility. 
The acronym LTS stands for "Long Term Support" which signifies that Ubuntu will provide updates and patches for this operating system until 2030.

Other supported operating systems include Debian ??.

### Rented VPS Node or Rented Dedicated Server

A Virtual Private Server (VPS) is a type of hosting service that simulates a dedicated server environment, but with shared hardware resources among multiple users. 
A dedicated server (DS) is a type of physical server that offers complete control over its hardware resources. 
This allows you to utilize all of the CPU, RAM, and storage available on the server without having to share it with other users. 
If your dedicated server has the necessary hardware to satisfy the combined requirements of multiple Koii Nodes, you may operate them on a single dedicated server.

Due to the variations in the ordering process and customer dashboard for each server provider, we cannot provide detailed instructions for each one. 
We suggest installing Ubuntu 20.04 as your operating system, but if you prefer to use a different operating system then you do that at your own risk.

# Koii Node Installation

## Connect with SSH
If you are renting a server or need to connect to your home-hosted server remotely, you will require an SSH client or terminal to communicate with your Koii node. 
If you have a preferred terminal please use that. 
Otherwise, Windows Command Prompt or the built-in terminal for MacOS and Linux users can be used. 
Putty is a free SSH client available for Windows users who prefer a graphical interface, it can be downloaded from www.putty.org.
To establish a remote connection to your Koii node, you will utilize SSH, also known as Secure Shell. This enables two computers to communicate over the internet.
Initially, you will require your Koii node's IP address:
If you are hosting from home, this will be your public IP address. 
If you are renting a server, your provider will give you an IP address.

We will begin by connecting as the root user, although some server providers may use ubuntu or admin instead.

To connect to your server, type the following command in your SSH client and then enter your server's password when prompted (replacing 11.111.11.11 with your server's IP address):
```
ssh root@11.111.11.11
```
Once you have successfully logged in, you will be presented with a command line that appears similar to:
```
root@servername:~$ 
```

## Setting Up the Environment on VPS

1. Install OS Updates
Before deploying Koii Node on your server you should install the most recent updates and security patches for your operating system. 
To accomplish this, use your terminal to execute the following command:
```
sudo apt update && sudo apt dist-upgrade -y
```
The updates will now begin downloading and installing. 
This process may take some time, depending on your server hardware and download speed. 
When the updates are completed, you will be returned to your command line.

2. Retrieve all the necessary files for Koii node on VPS (Cloning Main Repository)

In order to succesfully launch a Koii Node there are perequisites which has to be stored in your server for Koii Node to function. 
```
git clone https://github.com/koii-network/VPS-task !To be adjusted the name so it is not confusing!
```
Then we enter that exact directory where the installation files can be found.
```
cd VPS-task
```

3.Configure the Environment Variables

Update main configuration file:

```
nano .env-local
```

Set `ENVIRONMENT` to `"production"`
Update the `TASKS` field with the task IDs you want to run, seperataed by commas, no space between!
Update `TASK_STAKES` field with the minimum or greater stake amounts corresponding to each task in `TASKS`, seperated by commas. Full list of Tasks and their details (i.e. min stakes) found [here](# link to below).
Set `INITIAL_STAKING_WALLET_BALANCE` to the amount of KOII you want in the Staking wallet. This should be greater than the SUM of all `TASK_STAKES` + 1 KOII for covering gas. 
NB! Remember this must be less than 1 KOII of what you will have in your Main wallet (i.e. you set `TASK_STAKES=2,2` and `INITIAL_STAKING_WALLET_BALANCE= 5` then you must have in your main wallet 6 KOII so there is enough for covering gas)

```
Ctrl +S = Save
Ctrl +X = Exit
```

4.Install dependencies

### Ensure Koii CLI is Installed. 

Documentation [here](https://docs.koii.network/develop/command-line-tool/koii-cli/install-cli)


### Wallet setup (Set up New Koii Publick Key/New wallet) Documentation [here](https://docs.koii.network/develop/command-line-tool/koii-cli/create-wallet)

```
koii balance
```

It will show: "Error: Dynamic program error: No default signer found, run "koii-keygen new -o ....." to create a new one"
Please copy the command from the "" which is automatically generated path, as that will set the wallet keys in correct place.

```
koii-keygen new -o /YOUR-USER/.config/koii/id.json
```

To improve security, system want you to set up BIP39 Passphrase - additional layer for securing your account, please fill it in.
**SAVE the generated SEED PHRASE in order for you to recover your funds later!**

Check what is your Wallet address and Transfer some KOII in it! *at least 4 KOII to launch your first Free Token Task*
```
koii address
```

For getting started Koii Foundation have created a Koii Faucet in order to receive first KOII - start from [here](https://docs.koii.network/develop/command-line-tool/koii-cli/send-and-receive-tokens)


### Installing Docker

```
sudo apt install docker
sudo apt install docker-compose
```
Check docker version

```
docker-compose --version
```

! If you have version below 1.29, please execute next commands:

Copy the path which is given after this command:
```
which docker-compose 
```

Update the docker-compose library. !Make sure that path in the end is the same as you retrieved from previous command.
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
Give the system ther permission
```
sudo chmod +x /usr/local/bin/docker-compose
```


5.Run the Docker Compose in order to launch your node

```
cd VPS-task !!! Need to double chcek once this is adjusted above!!!!!!
docker-compose up
```
This command creates a staking wallet, stakes on the tasks, and then runs the tasks.

**After completing this step you have your node sucessfully running**

In order to verify this:

```
docker logs -f --tail 100 task_node
```

If the logs return, something like i.e.
```
Cluster RPC URL: https://testnet.koii.live
task_node  | *********************ENDPOINTS*********************
task_node  | /attention*
task_node  | /
task_node  | /nodes/:taskId
task_node  | /register-node/:taskId
task_node  | /get-all-running-tasks
task_node  | /namespace-wrapper
task_node  | /tasks
task_node  | *********************END ENDPOINTS*********************
task_node  | currentInterval undefined
task_node  | running tasks now...
task_node  | SETTING UP EXPRESS
task_node  | setup function called
task_node  | Free Token Task! listening on port 10000
task_node  | getTaskLevelDBPath
```
Then CONGRATULATIONS - it is success!

## How to interact now with the running Node?

1.Install dependencies
```
sudo apt install npm
```


execute the command 'create-task-cli' and you will see an interactive menu
```
create-task-cli
```
```
? Select operation › - Use arrow-keys. Return to submit.
❯   Create a new task
    update existing task
    Activate/Deactivate task
    Claim reward
    Fund task with more KOII
    Withdraw staked funds from task
    upload assets to IPFS(metadata/local vars)
```
### Example how to claim rewards from the "Free Token Task!"
```
Calling ClaimReward
✔ Enter the task id … 6GbpHRK3duDbo3dCEFXuJ2KD5Hg6Yo4A9LyHozeE7rjN
✔ Enter the stakePotAccount address … FnQm11NXJxPSjza3fuhuQ6Cu4fKNqdaPkVSRyLSWf14d
✔ Enter the beneficiaryAccount address (Address that the funds will be transferred to) … 5aU9fdhjpe2sZKzZ858tV1ZDGjht1Q1UQewEphdo5meZ
✔ Enter the path to Claimer wallet … /home/koii/koii-node/namespace/staking_wallet.json
Success
```
6GbpHRK3duDbo3dCEFXuJ2KD5Hg6Yo4A9LyHozeE7rjN represents the Task ID   
FnQm11NXJxPSjza3fuhuQ6Cu4fKNqdaPkVSRyLSWf14d represents the Task Creator ID   
5aU9fdhjpe2sZKzZ858tV1ZDGjht1Q1UQewEphdo5meZ represents the Wallet that will receive the claimed Coins   
/home/koii/koii-node/namespace/staking_wallet.json represents the default path to your staking wallet   

### Example how to Unstake from the "Free Token Task!"
```
1. Calling Withdraw staked funds from task
✔ Enter the task id … 6GbpHRK3duDbo3dCEFXuJ2KD5Hg6Yo4A9LyHozeE7rjN
✔ Enter the submitter wallet path address … /home/koii/koii-node/namespace/staking_wallet.json
Gives error but don't worry, follow on with next step ->

2.Calling ClaimReward
✔ Enter the task id … 6GbpHRK3duDbo3dCEFXuJ2KD5Hg6Yo4A9LyHozeE7rjN
✔ Enter the stakePotAccount address … stakepotaccountUSDX2sDSAcq8ZEvA3Tiv7tRpJ1Dv
✔ Enter the beneficiaryAccount address (Address that the funds will be transferred to) … YOUR-ADDRESS
✔ Enter the path to Claimer wallet … /home/koii/koii-node/namespace/staking_wallet.json
Success
```
6GbpHRK3duDbo3dCEFXuJ2KD5Hg6Yo4A9LyHozeE7rjN represents the Task ID   
stakepotaccountUSDX2sDSAcq8ZEvA3Tiv7tRpJ1Dv represents the Stake Pot Account    
YOUR-ADDRESS represents the Wallet that will receive the claimed Coins   
/home/koii/koii-node/namespace/staking_wallet.json represents the default path to your staking wallet 

## Additional helpful commands you should know
```
koii -u https://testnet.koii.live balance
koii --version
koii address
```

Feel free to reach out to us in Discord if you have any further questions or need assistance.


If you encounter any error try to instal components seperately:
Link where to find how to install Koii CLI on different OS: https://docs.koii.network/develop/command-line-tool/koii-cli/install-cli

Thing to note:
You should always have to keep your Staking_Public_Key balance filled. 


Full list of Tasks:
Task name	Task ID	Min stake

*Free Token Task!*	~AXcd6MctmDUQo3XDeBNa4NBAi4tfBYDpt4Adxyai3Do3~ -> NEW Task ID from 15.02.2024= 6GbpHRK3duDbo3dCEFXuJ2KD5Hg6Yo4A9LyHozeE7rjN 1.7 KOII

*Arweave Verifier*	BwwkRHB1GVWvVDbdXfUobRFLfz7wz6tz2u2W8mKCUjsj 1.9 KOII

*Inflation Monitoring*	EN4CA8EuLzUJGDC8p6WwCqCniwGB99sXh7Ma2WBKDL9o 0.9 KOII

*Big Big Task*	tqhFcPKvcWPagNWNEBC6LtpFujboi7FigVoVUw2Ph8n 0.01 KOII

*Adot Search AI*	5hoMYrrdPQneqsQyRGzmoHvKEbR5kA1dN6jdUWFnhgVH 1.9 KOII

*#koii Twitter Archive*	9Vfa7Jowd3q7xdiV4hSZt1t5L9PWXjFWb6VuLbUXPwUS 1.9 KOII

*Archive Twitter*	Hcpv6q57mcG9fGca9xMoivMuCVNWZd9atxRQfcde6Edb 1.9 KOII

*Mask Social Feeds*	7NvPT3RC24NhgMZZSW1Ke5WKcVLwQgwRbYfVYsvkJobH 1.9 KOII

