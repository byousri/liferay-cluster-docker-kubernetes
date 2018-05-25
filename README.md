# liferay-cluster-docker-kubernetes


Docker compose and Kubernetes with Liferay 6.2 cluster with replicate node session:

- Mysql
- liferay 6.2-ce-ga6 node 1
- liferay 6.2-ce-ga6 node 2
- haproxy


$ docker-compose up -d

- http://localhost/ load balancer
- http://localhost:9090 load balancer stats

$ docker-compose down