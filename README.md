
# accessibility-assessment-fluentbit

Builds a fluentbit container image to forward logs to redis (and ultimately to Elasticsearch) from other containers in the ECS cluster. 
The files for building the plugin are based on the work in https://github.com/majst01/aws-ecs-fluentbit

### Notes

### Building

* There is a Makefile that controls the build of the container image; run using `make build`
* The Makefile generates a semver for the image to be built using `version-incrementor`
* Packer tags the images with this semver.
* The image and tags are pushed to the artifactory docker repository using `make push_image`. This requires artifactory credentials as ENVs `ARTIFACTORY_USERNAME` and `ARTIFACTORY_PASSWORD`

### TBD

#### Testing

There was an attempt to use dgoss/goss to test this container image, as per the `aws-ecs-tls-proxy` container image. However, as this container image is based on a distroless image, goss would not run as it requires `sh` to be present in the container. A possible solution would be to re-package the fluentbit code in a debian-slim image. Fluentbit will not build and execute in an alpine OS, due to incompatibilies in the alpine C library.

#### Jenkins Pipeline

 This docker image is built in a pipeline called `accessibility-assessment-fluentbit-docker-image`

# Deployment

The fluentbit container is deployed as a service in the MDTP ECS clusters. There is one service for each of the protected and public ECS clusters.
The service is fronted by an NLB which passes the traffic on port 24224 through to one of the tasks in the service.
The resources for the service are provisioned in the `ecs-mdtp` module in `webops-terraform`. 
The version of the image to launch in the service is defined in the `ecs-mdtp` component and passed into the module per environment.






