# Cgroups & Namespaces

## Memory 

`docker run -d --name mb100 --memory 100m alpine top` </br>
The memory usage and limits of containers can be identified via the docker stats command.</br>
`docker stats --no-stream`</br>

## CPU Shares

<pre><code>
docker run -d --name c768 --cpuset-cpus 0 --cpu-shares 768 benhall/stress
docker run -d --name c256 --cpuset-cpus 0 --cpu-shares 256 benhall/stress
sleep 5
docker stats --no-stream
docker rm -f c768 c256
</pre></code>

## Namespace

### Network namespace

```
docker run -it alpine ip addr show
docker run -it --net=host alpine ip addr show
```

### PID namespace
```
docker run -it alpine ps aux
docker run -it --pid=host alpine ps aux
```


### Share namespace

```
docker run -d --name http nginx:alpine
docker run --net=container:http benhall/curl curl -s localhost
docker run --pid=container:http alpine ps aux
```
