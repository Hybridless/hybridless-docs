# Hybridless registry

Hybridless framework maintains a Docker registry shadowing all the hybridless **runtime base images** because of the recently added **download rate limits** for **unauthenticated** users on the Docker registry. You can see more details about this [here](https://docs.docker.com/docker-hub/download-rate-limit/).

The hybridless registry is currently hosted at [GitHub Container Registry](https://github.blog/2020-09-01-introducing-github-container-registry/) and maintained by GitHub Action on the hybridless repository. You can check what images are currently being shadowed [here](https://github.com/Hybridless/hybridless/blob/master/.github/workflows/hybridless-registry.yml).

This allows deployments to freely pull images from the Hybridless registry without worrying about rate limits or any other constraints. It's also an important moment to refresh that base images are **pulled every single deployment** \(if ephemeral build\) and therefore can be **very demanding,** especially if you are doing cloud-based builds, you can't count on your auto-assigned public IP for the build instance to not being rate limited for build dependant resource.

GitHub luckily provides **no rate-limiting** for open source projects, which is our case 🥳 So all runtimes base images are pulled from Docker hub to Hybridless registry as a facilitator for those who use built-in runtimes on hybridless.

## Security & Updates

To maintain **transparency** and prevent any code injection this process is fully automated via GitHub Actions and images checksum can be public seems on the action logs accessible [here](https://github.com/Hybridless/hybridless/actions/workflows/hybridless-registry.yml) and they should match the same checksum present on the docker hub.

The repository is **scheduled to update** and push from the Docker registry **weekly**. 

Scan on push on ECR repositories created by hybridless to push your images is strongly encouraged, so from the base image hybridless use to your application code, everything is scanned. 

{% hint style="danger" %}
Absolutely, **no images** from the **applications being deployed** with Hybridless are pushed to the Hybridless registry and neither public visible to anyone. **Only pulling operations are done on the registry.**
{% endhint %}

{% hint style="info" %}
This feature is not supported yet, but soon will be available on the plugin and event-level so you can opt-in and enable image scanning services.
{% endhint %}

### 

## Registry Information

Registry Address: `ghcr.io/hybridless/`

Registry Configuration: [https://github.com/hybridless/hybridless/blob/master/.github/workflows/hybridless-registry.yml](https://github.com/Hybridless/hybridless/blob/master/.github/workflows/hybridless-registry.yml)

Registry Logs: [https://github.com/hybridless/hybridless/actions/workflows/hybridless-registry.yml](https://github.com/Hybridless/hybridless/actions/workflows/hybridless-registry.yml)



## Custom Registry

Optionally you can always pull base images from the **docker registry** if doing authentication before the deployment execution \(have not tested yet\) or using your **own registry** or even **ECR** from AWS.

