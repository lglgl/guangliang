docker swarm init --advertise-addr 192.168.10.187


Swarm initialized: current node (2tllh25ye4tzdlzmdz7x6ssnq) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-1wve4hiyyit8bi5rhdumrx8lbcqcdv8duml2taupt1uvmozf2a-6m95zjdkgtj0yxsinp69g6fhs 192.168.10.187:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.


docker node ls

docker network create -d overlay --attachable swarm-net


docker-compose exec prod_cluster_zookeeper_02 bash







安装docker-compose

yum install -y docker-compose --downloadonly --downloaddir=/root/software/docker-compose



