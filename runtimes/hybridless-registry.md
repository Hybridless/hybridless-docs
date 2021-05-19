# Hybridless registry

Hybridless framework maintains a Docker registry shadowing all the hybridless used images because of the recently added **download rate limits** for **unautheticated** users on the Docker registry. You can see more details about this [here](https://docs.docker.com/docker-hub/download-rate-limit/).

The hybridless registry is currently hosted at [GitHub Container Registry](https://github.blog/2020-09-01-introducing-github-container-registry/) and maintained by GitHub Action on the hybridless repository. You can check what images are currently being shadowed [here](https://github.com/Hybridless/hybridless/blob/master/.github/workflows/hybridless-registry.yml).

This allows deployments to freely pull images from the Hybridless repository without worrying about rate limits or any other constraints. 

### Security & Updates

To maintain **transparency** and prevent any code injection this process is fully automated via GitHub Actions and images checksum can be public seems on the action logs accessible [here](https://github.com/Hybridless/hybridless/actions/workflows/hybridless-registry.yml) and they should match the same checksum present on the docker hub.

The repository is **scheduled to update** and push from the Docker registry **weekly**. 

### 

### Information

Registry Address: `ghcr.io/hybridless/`

Registry Configuration: [https://github.com/hybridless/hybridless/blob/master/.github/workflows/hybridless-registry.yml](https://github.com/Hybridless/hybridless/blob/master/.github/workflows/hybridless-registry.yml)

Registry Logs: [https://github.com/hybridless/hybridless/actions/workflows/hybridless-registry.yml](https://github.com/Hybridless/hybridless/actions/workflows/hybridless-registry.yml)



### Custom Registry

Optionally you can always push base images from the docker registry if doing authentication before the deployment execution \(have not tested yet\), or using your own registry or even ECR from AWS.

