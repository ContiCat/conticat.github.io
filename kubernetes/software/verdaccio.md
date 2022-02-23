# verdaccio

## main usage

* none

## conceptions

* none

## purpose
* none

## pre-requirements
* [create local cluster for testing](../resources/local.cluster.for.testing.md)
* [ingress](../basic/ingress.nginx.md)
* [cert-manager](../basic/cert.manager.md)

## Do it

1. prepare [verdaccio.values.yaml](resources/verdaccio.values.yaml.md)
2. prepare images
    * ```shell  
      DOCKER_IMAGE_PATH=/root/docker-images && mkdir -p ${DOCKER_IMAGE_PATH}
      BASE_URL="https://conti-docker-images.oss-cn-hangzhou.aliyuncs.com"
      LOCAL_IMAGE="localhost:5000"
      for IMAGE in "docker.io/verdaccio/verdaccio:5.2.0" 
      do
          IMAGE_FILE=$(echo ${IMAGE} | sed "s/\//_/g" | sed "s/\:/_/g").dim
          LOCAL_IMAGE_FIEL=${DOCKER_IMAGE_PATH}/${IMAGE_FILE}
          if [ ! -f ${LOCAL_IMAGE_FIEL} ]; then
              curl -o ${IMAGE_FILE} -L ${BASE_URL}/${IMAGE_FILE} \
                  && mv ${IMAGE_FILE} ${LOCAL_IMAGE_FIEL} \
                  || rm -rf ${IMAGE_FILE}
          fi
          docker image load -i ${LOCAL_IMAGE_FIEL}
          docker image inspect ${IMAGE} || docker pull ${IMAGE}
          docker image tag ${IMAGE} ${LOCAL_IMAGE}/${IMAGE}
          docker push ${LOCAL_IMAGE}/${IMAGE}
      done
      ```
3. install by helm
    * ```shell
      helm install \
          --create-namespace --namespace application \
          my-verdaccio \
          https://conti-charts.oss-cn-hangzhou.aliyuncs.com/github.com/verdaccio/charts/releases/download/verdaccio-4.6.2/verdaccio-4.6.2.tgz \
          --values verdaccio.values.yaml \
          --atomic
      ```
## test
1. check connection
    * ```shell
      curl --insecure --header 'Host: npm.test.cnconti.cc' https://localhost
      ```
2. visit `https://npm.test.cnconti.cc`
      
## uninstall 
* ```shell
  helm -n application uninstall my-verdaccio
  ```


















