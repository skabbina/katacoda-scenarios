
# Optional Step

CIS publishes [Docker Benchmark](https://www.cisecurity.org/benchmark/docker/), which provides prescriptive guidance for establishing a secure configuration posture for Docker Engine.

However note that, kubernetes environments **do not** comply with many of these recommendations. For instance, kubernetes and CNI containers run as root or run in privileged mode. Hence docker bench is recommended for Docker only deployments. In case of Kubernetes deployments, equivalent controls would be implemented through  Kubernetes Admission Controllers and other mechanisms. 

Run docker bench and review the results.

```
docker run -it --net host --pid host --userns host --cap-add audit_control \
    -e DOCKER_CONTENT_TRUST=$DOCKER_CONTENT_TRUST \
    -v /etc:/etc:ro \
    -v /usr/bin/docker-containerd:/usr/bin/containerd:ro \
    -v /usr/lib/systemd:/usr/lib/systemd:ro \
	-v /usr/bin/runc:/usr/bin/runc:ro \
    -v /var/lib:/var/lib:ro \
    -v /var/run/docker.sock:/var/run/docker.sock:ro \
    --label docker_bench_security \
    docker/docker-bench-security
```{{execute}}

